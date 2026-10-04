<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Our Secret Garden Letters</title>
    <style>
        @import url('https://googleapis.com');

        :root {
            --forest-green: #1a331e;
            --soft-pink: #ffcad4;
            --deep-pink: #e8aeb7;
            --cream: #faedcd;
            --card-bg: rgba(26, 51, 30, 0.75);
        }

        body {
            margin: 0;
            padding: 0;
            font-family: 'Playfair Display', serif;
            background-color: var(--forest-green);
            background-image: 
                radial-gradient(circle at 20% 30%, rgba(255, 202, 212, 0.05) 0%, transparent 40%),
                radial-gradient(circle at 80% 70%, rgba(255, 202, 212, 0.05) 0%, transparent 40%);
            color: var(--cream);
            min-height: 100vh;
            display: flex;
            justify-content: center;
            align-items: center;
            overflow-x: hidden;
            position: relative;
        }

        /* Whimsical Floating Lilies Background Details */
        .lily-decoration {
            position: fixed;
            font-size: 5.5rem;
            opacity: 0.18;
            pointer-events: none;
            z-index: 1;
            user-select: none;
            filter: drop-shadow(0 0 10px var(--soft-pink));
            animation: float 6s ease-in-out infinite;
        }
        .lily-1 { top: 40px; left: 40px; transform: rotate(-15deg); }
        .lily-2 { bottom: 40px; right: 40px; transform: rotate(15deg); animation-delay: 2s; }
        .lily-3 { top: 60%; left: 5%; font-size: 3rem; opacity: 0.08; animation-delay: 4s; }

        @keyframes float {
            0%, 100% { transform: translateY(0) rotate(-15deg); }
            50% { transform: translateY(-15px) rotate(-10deg); }
        }

        .container {
            width: 90%;
            max-width: 600px;
            background: var(--card-bg);
            border: 2px solid var(--soft-pink);
            outline: 4px double var(--soft-pink);
            outline-offset: -12px;
            border-radius: 30px;
            padding: 40px 30px;
            box-shadow: 0 20px 50px rgba(0, 0, 0, 0.6), inset 0 0 20px rgba(255, 202, 212, 0.1);
            backdrop-filter: blur(12px);
            -webkit-backdrop-filter: blur(12px);
            z-index: 2;
            margin: 40px 0;
            transition: all 0.5s ease;
        }

        h1 {
            font-family: 'Caveat', cursive;
            color: var(--soft-pink);
            text-align: center;
            font-size: 3.8rem;
            margin-top: 0;
            margin-bottom: 10px;
            text-shadow: 0 4px 8px rgba(0,0,0,0.4);
            letter-spacing: 1px;
        }

        /* Lock Screen Panel Layout */
        #lock-screen {
            text-align: center;
            display: flex;
            flex-direction: column;
            align-items: center;
            padding: 20px 0;
        }

        #lock-screen p {
            font-style: italic;
            font-size: 1.2rem;
            color: var(--soft-pink);
            margin-bottom: 25px;
            opacity: 0.9;
        }

        /* Input Controls and Typography Elements */
        .input-group {
            margin-bottom: 22px;
            width: 100%;
            text-align: left;
        }

        label {
            display: block;
            font-size: 1.1rem;
            margin-bottom: 8px;
            color: var(--soft-pink);
            font-weight: bold;
            letter-spacing: 0.5px;
        }

        input[type="text"], input[type="password"], textarea {
            width: 100%;
            padding: 14px 18px;
            border: 1.5px solid var(--soft-pink);
            border-radius: 15px;
            background: rgba(255, 255, 255, 0.07);
            color: #fff;
            font-family: 'Playfair Display', serif;
            font-size: 1.05rem;
            box-sizing: border-box;
            transition: all 0.3s ease;
        }

        input:focus, textarea:focus {
            outline: none;
            background: rgba(255, 255, 255, 0.13);
            box-shadow: 0 0 15px rgba(255, 202, 212, 0.4);
            border-color: #fff;
        }

        textarea {
            height: 160px;
            resize: vertical;
            line-height: 1.5;
        }

        /* Elegant Button Accents */
        button {
            background-color: var(--soft-pink);
            color: var(--forest-green);
            border: none;
            padding: 10px 40px;
            font-weight: bold;
            border-radius: 25px;
            cursor: pointer;
            transition: all 0.3s ease;
            font-family: 'Caveat', cursive;
            font-size: 1.7rem;
            box-shadow: 0 6px 15px rgba(0,0,0,0.3);
            display: inline-block;
        }

        button:hover {
            background-color: #fff;
            transform: translateY(-2px) scale(1.03);
            box-shadow: 0 10px 20px rgba(255, 202, 212, 0.4);
        }

        /* Main Letter Screen - Hidden initially */
        #app-screen {
            display: none;
            animation: fadeIn 0.8s ease forwards;
        }

        .form-row {
            display: flex;
            gap: 20px;
        }

        /* Dynamic Letters Archive Workspace */
        .archive-section {
            margin-top: 40px;
            border-top: 2px dashed rgba(255, 202, 212, 0.4);
            padding-top: 30px;
        }

        .archive-title {
            font-family: 'Caveat', cursive;
            color: var(--soft-pink);
            font-size: 2.6rem;
            text-align: center;
            margin-bottom: 25px;
        }

        #letters-container {
            max-height: 450px;
            overflow-y: auto;
            padding-right: 12px;
        }

        /* Scrollbar Formatting */
        #letters-container::-webkit-scrollbar {
            width: 6px;
        }
        #letters-container::-webkit-scrollbar-thumb {
            background: var(--soft-pink);
            border-radius: 10px;
        }

        .letter-card {
            background: rgba(255, 255, 255, 0.04);
            border: 1px solid rgba(255, 202, 212, 0.15);
            border-left: 4px solid var(--soft-pink);
            padding: 20px;
            border-radius: 4px 16px 16px 4px;
            margin-bottom: 22px;
            animation: cardAppear 0.5s ease forwards;
            position: relative;
            box-shadow: 0 4px 15px rgba(0,0,0,0.15);
        }

        .letter-meta {
            font-size: 0.95rem;
            color: var(--soft-pink);
            margin-bottom: 12px;
            display: flex;
            justify-content: space-between;
            align-items: center;
            font-style: italic;
            border-bottom: 1px solid rgba(255, 202, 212, 0.1);
            padding-bottom: 6px;
        }

        .letter-body {
            line-height: 1.7;
            white-space: pre-wrap;
            color: #fff;
            font-size: 1.05rem;
        }
        
        .letter-card::after {
            content: '🪷';
            position: absolute;
            bottom: 10px;
            right: 15px;
            opacity: 0.2;
            font-size: 1.4rem;
            filter: drop-shadow(0 0 5px var(--soft-pink));
        }

        #error-msg {
            color: #ff8b8b;
            margin-top: 15px;
            font-weight: bold;
            font-size: 1.1rem;
            min-height: 24px;
        }

        @keyframes fadeIn {
            from { opacity: 0; transform: translateY(15px); }
            to { opacity: 1; transform: translateY(0); }
        }

        @keyframes cardAppear {
            from { opacity: 0; transform: scale(0.95) translateY(10px); }
            to { opacity: 1; transform: scale(1) translateY(0); }
        }

        @media (max-width: 480px) {
            .form-row {
                flex-direction: column;
                gap: 0;
            }
            .container {
                padding: 30px 20px;
            }
            h1 {
                font-size: 3rem;
            }
        }
    </style>
</head>
<body>

    <!-- Beautiful Whimsical Lily Accents -->
    <div class="lily-decoration lily-1">🪷</div>
    <div class="lily-decoration lily-2">🪷</div>
    <div class="lily-decoration lily-3">🪷</div>

    <div class="container">
        <!-- LOCK SCREEN -->
        <div id="lock-screen">
            <h1>Our Secret Garden</h1>
            <p>Enter the magic code to see our letters...</p>
            <div class="input-group" style="max-width: 280px;">
                <input type="password" id="secret-code" placeholder="••••••••" style="text-align: center; letter-spacing: 5px;">
            </div>
            <button type="button" onclick="unlockGarden()">Unlock Garden</button>
            <div id="error-msg"></div>
        </div>

        <!-- APP SCREEN -->
        <div id="app-screen">
            <h1>Write a Letter</h1>
            
            <div class="form-row">
                <div class="input-group">
                    <label for="from-input">From:</label>
                    <input type="text" id="from-input" placeholder="Your name...">
                </div>
                <div class="input-group">
                    <label for="to-input">To:</label>
                    <input type="text" id="to-input" placeholder="Her name...">
                </div>
            </div>

            <div class="input-group">
                <label for="message-input">What's on your mind?</label>
                <textarea id="message-input" placeholder="Type your thoughts here, love..."></textarea>
            </div>

            <div style="text-align: center;">
