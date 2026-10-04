<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Our Private Letterbox</title>
    <style>
        body { 
            font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, sans-serif; 
            background-color: #f2f7f4; 
            padding: 40px 20px; 
            display: flex; 
            justify-content: center; 
        }
        .container { 
            max-width: 600px; 
            width: 100%; 
            background: #1e3f20; /* Deep Forest Green */
            padding: 30px; 
            border-radius: 16px; 
            box-shadow: 0 8px 24px rgba(0,0,0,0.15); 
            color: #ffcad4; /* Soft Pink text */
        }
        h1 { 
            font-size: 26px; 
            text-align: center; 
            margin-bottom: 5px; 
            color: #ffb3c1; /* Brighter Pink accent */
        }
        p.subtitle { 
            text-align: center; 
            color: #ffe5ec; 
            font-size: 14px; 
            margin-top: 0; 
            margin-bottom: 25px; 
        }
        label { 
            font-weight: 600; 
            display: block; 
            margin-bottom: 8px; 
            font-size: 14px; 
            color: #ffb3c1;
        }
        input[type="password"], textarea { 
            width: 100%; 
            padding: 12px; 
            margin-bottom: 20px; 
            border: 2px solid #ffcad4; 
            border-radius: 8px; 
            font-size: 15px; 
            box-sizing: border-box; 
            background-color: #2d5a30; /* Darker Forest Input Field */
            color: #fff;
        }
        input[type="password"]::placeholder, textarea::placeholder {
            color: #ffcad4;
            opacity: 0.6;
        }
        input[type="password"]:focus, textarea:focus {
            outline: none;
            border-color: #ffb3c1;
            background-color: #346938;
        }
        .actions { 
            display: flex; 
            gap: 15px; 
            margin-bottom: 25px; 
        }
        button { 
            flex: 1; 
            padding: 12px; 
            border: none; 
            border-radius: 8px; 
            font-size: 16px; 
            font-weight: 600; 
            cursor: pointer; 
            transition: all 0.2s ease;
        }
        .btn-encrypt { 
            background-color: #ffb3c1; 
            color: #1e3f20; 
        }
        .btn-encrypt:hover { 
            background-color: #ffcad4; 
            transform: translateY(-1px);
        }
        .btn-decrypt { 
            background-color: #ff85a1; 
            color: #ffffff; 
        }
        .btn-decrypt:hover { 
            background-color: #ffb3c1; 
            transform: translateY(-1px);
        }
        .output-box { 
            background: #2d5a30; 
            border: 2px dashed #ffcad4; 
            padding: 15px; 
            border-radius: 8px; 
            min-height: 50px; 
            word-break: break-all; 
            font-family: monospace; 
            font-size: 14px; 
            white-space: pre-wrap; 
            color: #ffffff;
        }
        .error { 
            color: #ff4d6d; 
            font-weight: 600; 
        }
    </style>
</head>
<body>
<div class="container">
    <h1>Private Letterbox</h1>
    <p class="subtitle">End-to-End Encrypted Communication</p>
    
    <label for="secret-code">Shared Secret Code (Password):</label>
    <input type="password" id="secret-code" placeholder="Enter your secret mutual code...">
    
    <label for="message">Your Letter / Scrambled Text:</label>
    <textarea id="message" placeholder="Type a letter to encrypt, OR paste scrambled text here to decrypt..."></textarea>
    
    <div class="actions">
        <button class="btn-encrypt" onclick="processText(true)">🔒 Encrypt Letter</button>
        <button class="btn-decrypt" onclick="processText(false)">🔓 Decrypt Letter</button>
    </div>
    
    <label>Result:</label>
    <div id="output" class="output-box">Your processed text will appear here...</div>
</div>

<script>
    async function deriveKey(password, salt) {
        const enc = new TextEncoder();
        const baseKey = await window.crypto.subtle.importKey("raw", enc.encode(password), { name: "PBKDF2" }, false, ["deriveKey"]);
        return window.crypto.subtle.deriveKey({ name: "PBKDF2", salt: salt, iterations: 100000, hash: "SHA-256" }, baseKey, { name: "AES-GCM", length: 256 }, false, ["encrypt", "decrypt"]);
    }

    async function processText(isEncrypt) {
        const password = document.getElementById('secret-code').value;
        const textData = document.getElementById('message').value;
        const outputDiv = document.getElementById('output');
        
        // Strict check: enforce the code to match your specific layout constraint
        if (password !== "0920") { 
            outputDiv.innerHTML = '<span class="error">Access Denied: Invalid Secret Code!</span>'; 
            return; 
        }
        if (!textData) { 
            outputDiv.innerHTML = '<span class="error">Error: Please provide some text to process.</span>'; 
            return; 
        }

        try {
            const encoder = new TextEncoder(); 
            const decoder = new TextDecoder();
            
            if (isEncrypt) {
                const salt = window.crypto.getRandomValues(new Uint8Array(16));
                const iv = window.crypto.getRandomValues(new Uint8Array(12));
                const key = await deriveKey(password, salt);
                const encrypted = await window.crypto.subtle.encrypt({ name: "AES-GCM", iv: iv }, key, encoder.encode(textData));
                const resultArray = new Uint8Array(salt.length + iv.length + encrypted.byteLength);
                resultArray.set(salt, 0); 
                resultArray.set(iv, salt.length); 
                resultArray.set(new Uint8Array(encrypted), salt.length + iv.length);
                outputDiv.innerText = btoa(String.fromCharCode.apply(null, resultArray));
            } else {
                const rawData = atob(textData); 
                const bytes = new Uint8Array(rawData.length);
                for (let i = 0; i < rawData.length; i++) { bytes[i] = rawData.charCodeAt(i); }
                const salt = bytes.slice(0, 16); 
                const iv = bytes.slice(16, 28); 
                const ciphertext = bytes.slice(28);
                const key = await deriveKey(password, salt);
                const decrypted = await window.crypto.subtle.decrypt({ name: "AES-GCM", iv: iv }, key, ciphertext);
                outputDiv.innerText = decoder.decode(decrypted);
            }
        } catch (e) { 
            outputDiv.innerHTML = '<span class="error">Decryption Failed! Bad cipher text layout.</span>'; 
        }
    }
</script>
</body>
</html>
