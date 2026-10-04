    <script>
        const REQUIRED_CODE = "03250904";

        // Handle enter key on password input
        document.getElementById('secret-code').addEventListener('keypress', function(e) {
            if (e.key === 'Enter') unlockGarden();
        });

        // Function to check the pin code
        function unlockGarden() {
            const enteredCode = document.getElementById('secret-code').value;
            const errorMsg = document.getElementById('error-msg');

            if (enteredCode === REQUIRED_CODE) {
                document.getElementById('lock-screen').style.display = 'none';
                document.getElementById('app-screen').style.display = 'block';
                displayLetters(); // Load any saved letters
            } else {
                errorMsg.innerText = "The code isn't right, love. Try again! 🌸";
                document.getElementById('secret-code').value = '';
            }
        }

        // Function to save and track letters locally forever
        function sendLetter() {
            const fromName = document.getElementById('from-input').value.trim();
            const toName = document.getElementById('to-input').value.trim();
            const message = document.getElementById('message-input').value.trim();

            if (!fromName || !toName || !message) {
                alert("Please fill out all fields before sending! 🌿");
                return;
            }

            // Create a custom tracking structure for the letter
            const newLetter = {
                from: fromName,
                to: toName,
                body: message,
                date: new Date().toLocaleDateString('en-US', { 
                    month: 'short', 
                    day: 'numeric', 
                    year: 'numeric',
                    hour: '2-digit',
                    minute: '2-digit'
                }) // FIXED: Closed the date object options and string builder safely
            };

            // Grab existing letters from localStorage or set an empty array if empty
            const letters = JSON.parse(localStorage.getItem('garden_letters')) || [];
            
            // Add new letter to the front of the list so freshest appears first
            letters.unshift(newLetter);
            
            // Save updated letters back to the browser's storage
            localStorage.setItem('garden_letters', JSON.stringify(letters));

            // Reset input text areas
            document.getElementById('message-input').value = '';
            
            // Refresh layout block to display the new letter
            displayLetters();
        }

        // Function to render letters inside the layout archive
        function displayLetters() {
            const container = document.getElementById('letters-container');
            const letters = JSON.parse(localStorage.getItem('garden_letters')) || [];

            if (letters.length === 0) {
                container.innerHTML = `<p style="text-align:center; font-style:italic; opacity:0.7;">The garden is quiet. No letters written yet... 🌿</p>`;
                return;
            }

            container.innerHTML = letters.map(letter => `
                <div class="letter-card">
                    <div class="letter-meta">
                        <span><strong>From:</strong> ${escapeHTML(letter.from)} &nbsp;&nbsp; <strong>To:</strong> ${escapeHTML(letter.to)}</span>
                        <span>${letter.date}</span>
                    </div>
                    <div class="letter-body">${escapeHTML(letter.body)}</div>
                </div>
            `).join('');
        }

        // Simple helper helper to stop text inputs from breaking HTML tags
        function escapeHTML(str) {
            return str.replace(/[&<>'"]/g, 
                tag => ({ '&': '&amp;', '<': '&lt;', '>': '&gt;', "'": '&#39;', '"': '&quot;' }[tag] || tag)
            );
        }
    </script>
</body>
</html>
