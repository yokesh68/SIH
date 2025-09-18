<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>RAWEN - Aadhaar Awareness Game</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <link href="https://fonts.googleapis.com/css2?family=Inter:wght@400;500;600;700&display=swap" rel="stylesheet">
    <style>
        body {
            font-family: 'Inter', sans-serif;
            -webkit-font-smoothing: antialiased;
            -moz-osx-font-smoothing: grayscale;
            background: linear-gradient(to bottom, #87CEEB 0%, #B0E0E6 100%);
            overflow: hidden; /* Prevent scrollbars from animated clouds */
        }
        .chat-container::-webkit-scrollbar {
            width: 6px;
        }
        .chat-container::-webkit-scrollbar-thumb {
            background-color: #4a5568;
            border-radius: 3px;
        }
        .chat-container::-webkit-scrollbar-track {
            background-color: #2d3748;
        }
        .modal {
            transition: opacity 0.3s ease-in-out;
        }
        .pulse-once {
            animation: pulse-once 0.8s cubic-bezier(0.4, 0, 0.6, 1);
        }
        @keyframes pulse-once {
            0%, 100% {
                transform: scale(1);
            }
            50% {
                transform: scale(1.05);
            }
        }
        #hint-btn:disabled {
            background-color: #4a5568;
            cursor: not-allowed;
            opacity: 0.5;
        }
        .speaker-icon {
            cursor: pointer;
            margin-left: 8px;
            display: inline-block;
            vertical-align: middle;
            opacity: 0.7;
            transition: opacity 0.2s;
        }
        .speaker-icon:hover {
            opacity: 1;
        }
        .bot-avatar {
            width: 40px;
            height: 40px;
            border-radius: 50%;
            margin-right: 12px;
            flex-shrink: 0;
            background-color: #4a5568;
            display: flex;
            align-items: center;
            justify-content: center;
            border: 2px solid #4a5568;
        }
        #clouds {
            position: fixed;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            z-index: 0;
            pointer-events: none;
            overflow: hidden;
        }
        .cloud {
            position: absolute;
            background-image: url('data:image/svg+xml;base64,PHN2ZyB4bWxucz0iaHR0cDovL3d3dy53My5vcmcvMjAwMC9zdmciIHZpZXdCb3g9IjAgMCA5NiA5NiI+PHBhdGggZD0iTTc5LjE2Myw1MS4wNDhjLTEuMzY2LDAtMi43MTUtLjIwNi00LjAyNS0uNjE4Yy0uNzU4LTYuODYtNi40MTgtMTIuMjctMTMuNDY4LTEyLjI3Yy0uNTQsMC0xLjA3MS4wNDItMS41OTQuMTI0QzU4Ljk0MywyNy4yMzIsNTAuMjI0LDE5LDM5LjY4NCwxOWMtMTEuNDQxLDAtMjAuODMzLDguNzEtMjEuNzY4LDIwLjAyMWMtLjMyNi0uMDE4LS42NTItLjAyMS0uOTg1LS4wMjFjLTcuNzgyLDAtMTQuMDkyLDYuMzEtMTQuMDkyLDE0LjA5MmMwLDcuNzgyLDYuMzEsMTQuMDkyLDE0LjA5MiwxNC4wOTJoNjIuMjMyYzYuNjQ1LDAsMTIuMDQxLTUuMzk2LDEyLjA0MS0xMi4wNDFDMTEuMjA0LDU2LjQ0NCw4NS44MDgsNTEuMDQ4LDc5LjE2Myw1MS4wNDh6IiBmaWxsPSIjRkZGRkZGIiBvcGFjaXR5PSIwLjciLz48L3N2Zz4=');
            background-repeat: no-repeat;
            background-size: contain;
            animation-name: moveClouds;
            animation-timing-function: linear;
            animation-iteration-count: infinite;
        }
        .cloud-1 { width: 200px; height: 120px; top: 10%; animation-duration: 35s; animation-delay: -5s; }
        .cloud-2 { width: 300px; height: 180px; top: 25%; animation-duration: 25s; animation-delay: -15s; }
        .cloud-3 { width: 150px; height: 90px; top: 65%; animation-duration: 45s; animation-delay: -2s; }
        .cloud-4 { width: 250px; height: 150px; top: 80%; animation-duration: 30s; animation-delay: -10s; }


        @keyframes moveClouds {
            0% { transform: translateX(100vw); }
            100% { transform: translateX(-300px); }
        }
        .captcha-code {
            background: #2d3748;
            border: 1px solid #4a5568;
            padding: 10px 20px;
            border-radius: 8px;
            font-family: 'Courier New', Courier, monospace;
            font-size: 1.5rem;
            letter-spacing: 5px;
            text-decoration: line-through;
            user-select: none;
        }
    </style>
</head>
<body class="flex items-center justify-center min-h-screen p-4">

    <div id="clouds">
        <div class="cloud cloud-1"></div>
        <div class="cloud cloud-2"></div>
        <div class="cloud cloud-3"></div>
        <div class="cloud cloud-4"></div>
    </div>

    <div class="w-full max-w-2xl h-[90vh] flex flex-col bg-gray-800/80 backdrop-blur-sm rounded-2xl shadow-2xl overflow-hidden border border-gray-700 relative z-10">
        <!-- Header -->
        <div class="bg-gray-700/80 p-4 grid grid-cols-3 items-center border-b border-gray-600">
            <div class="flex justify-start"> <!-- Column 1: Logo -->
                <img src="https://www.svgrepo.com/show/494038/robot-6.svg" alt="RAWEN logo" class="h-10 w-10">
            </div>
            <div class="text-center"> <!-- Column 2: Title & Status -->
                <h1 class="text-xl font-bold">RAWEN 🤖</h1>
                <p class="text-sm text-green-400">Online</p>
            </div>
            <div class="flex justify-end"></div> <!-- Column 3: Empty/Placeholder -->
        </div>

        <!-- Chat Area -->
        <div id="chat-container" class="flex-1 p-6 overflow-y-auto chat-container">
            <!-- Messages will be appended here -->
        </div>

        <!-- Input Area -->
        <div class="p-4 bg-gray-800/80 border-t border-gray-700">
            <div class="flex items-center space-x-2">
                <input type="text" id="user-input" class="flex-1 bg-gray-700 text-white placeholder-gray-400 p-3 rounded-xl focus:outline-none focus:ring-2 focus:ring-indigo-500" placeholder="Type your answer here...">
                <button id="hint-btn" class="bg-gray-600 hover:bg-gray-700 text-white font-bold py-3 px-4 rounded-xl transition duration-300">Hint</button>
                <button id="send-btn" class="bg-indigo-600 hover:bg-indigo-700 text-white font-bold py-3 px-6 rounded-xl transition duration-300 transform hover:scale-105">
                    Send
                </button>
            </div>
        </div>
    </div>

    <!-- Info Modal -->
    <div id="info-modal" class="modal fixed inset-0 bg-black bg-opacity-70 flex items-center justify-center p-4 hidden opacity-0 z-20">
        <div id="modal-content" class="bg-gray-800 border border-indigo-500 rounded-2xl p-8 max-w-lg w-full text-center shadow-2xl transform scale-95 transition-transform duration-300">
            <div id="modal-icon" class="mx-auto bg-green-500 h-16 w-16 rounded-full flex items-center justify-center mb-4 text-4xl">
                 <!-- Icon will be set by JS -->
            </div>
            <h2 id="modal-title" class="text-2xl font-bold mb-4">Correct!</h2>
            <div class="flex items-center justify-center">
                 <p id="modal-text" class="text-gray-300 mb-6 whitespace-pre-line text-left"></p>
                 <span id="modal-speaker-icon"></span>
            </div>
            <button id="next-level-btn" class="w-full bg-indigo-600 hover:bg-indigo-700 text-white font-bold py-3 px-6 rounded-xl transition duration-300 transform hover:scale-105">
                Next Question
            </button>
        </div>
    </div>
    
    <!-- Aadhaar Check Modal -->
    <div id="aadhaar-check-modal" class="modal fixed inset-0 bg-black bg-opacity-70 flex items-center justify-center p-4 hidden opacity-0 z-50">
        <div class="bg-gray-800 border border-indigo-500 rounded-2xl p-8 max-w-md w-full shadow-2xl transform scale-95 transition-transform duration-300">
            <h2 class="text-2xl font-bold mb-2 text-center">Aadhaar Link Status Check (Simulation)</h2>
            <p class="text-center text-sm text-yellow-400 mb-6">This is a practice simulation. No real data is used.</p>
            
            <!-- Step 1: Aadhaar Input -->
            <div id="aadhaar-step-1">
                <label for="aadhaar-number" class="block mb-2 text-sm font-medium text-gray-300">Enter your 12-digit Aadhaar Number</label>
                <input type="text" id="aadhaar-number" class="w-full bg-gray-700 text-white p-3 rounded-xl focus:outline-none focus:ring-2 focus:ring-indigo-500" placeholder="XXXX XXXX XXXX" maxlength="12">
                <p id="aadhaar-error" class="text-red-400 text-sm mt-2 hidden">Please enter a valid 12-digit number.</p>
                <button id="aadhaar-next-btn" class="w-full mt-4 bg-indigo-600 hover:bg-indigo-700 text-white font-bold py-3 rounded-xl transition">Next</button>
            </div>

            <!-- Step 2: Captcha -->
            <div id="aadhaar-step-2" class="hidden">
                <label for="captcha-input" class="block mb-2 text-sm font-medium text-gray-300">Enter the Captcha</label>
                <div class="flex justify-center my-4">
                    <div id="captcha-code" class="captcha-code text-white"></div>
                </div>
                <input type="text" id="captcha-input" class="w-full bg-gray-700 text-white p-3 rounded-xl focus:outline-none focus:ring-2 focus:ring-indigo-500" placeholder="Enter code above">
                 <p id="captcha-error" class="text-red-400 text-sm mt-2 hidden">Incorrect Captcha. Please try again.</p>
                <button id="captcha-verify-btn" class="w-full mt-4 bg-indigo-600 hover:bg-indigo-700 text-white font-bold py-3 rounded-xl transition">Verify Captcha</button>
            </div>

            <!-- Step 3: OTP -->
            <div id="aadhaar-step-3" class="hidden">
                <label for="otp-input" class="block mb-2 text-sm font-medium text-gray-300">Enter 6-digit OTP sent to your mobile</label>
                <input type="text" id="otp-input" class="w-full bg-gray-700 text-white p-3 rounded-xl focus:outline-none focus:ring-2 focus:ring-indigo-500" placeholder="XXXXXX" maxlength="6">
                <p id="otp-error" class="text-red-400 text-sm mt-2 hidden">Please enter a 6-digit OTP.</p>
                <button id="otp-verify-btn" class="w-full mt-4 bg-indigo-600 hover:bg-indigo-700 text-white font-bold py-3 rounded-xl transition">Verify OTP</button>
            </div>

            <!-- Step 4: Result -->
            <div id="aadhaar-step-4" class="hidden text-center">
                <div class="mx-auto bg-green-500 h-16 w-16 rounded-full flex items-center justify-center mb-4 text-4xl">
                    <svg class="w-10 h-10 text-white" fill="none" stroke="currentColor" viewBox="0 0 24 24" xmlns="http://www.w3.org/2000/svg"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M9 12l2 2 4-4m6 2a9 9 0 11-18 0 9 9 0 0118 0z"></path></svg>
                </div>
                <h3 class="text-xl font-bold">Status: Linked!</h3>
                <p class="text-gray-300 mt-2 mb-6">Congratulations! Your Aadhaar is linked to a bank account. <br><strong>Remember:</strong> You still need to visit your bank to confirm it is 'Seeded' for DBT.</p>
                <button id="aadhaar-close-btn" class="w-full bg-gray-600 hover:bg-gray-500 text-white font-bold py-3 rounded-xl transition">Close</button>
            </div>
        </div>
    </div>


    <!-- Language Selection Modal -->
    <div id="language-modal" class="modal fixed inset-0 bg-black bg-opacity-70 flex items-center justify-center p-4 hidden opacity-0 z-50">
        <div id="language-modal-content" class="bg-gray-800 border border-indigo-500 rounded-2xl p-6 max-w-xs w-full text-center shadow-2xl transform scale-95 transition-transform duration-300">
            <h3 class="text-lg font-semibold mb-4">Choose Voice Language</h3>
            <div class="space-y-3">
                <button onclick="speakInLanguage('en')" class="w-full bg-gray-600 hover:bg-gray-700 text-white font-bold py-3 px-4 rounded-xl transition duration-300">English</button>
                <button onclick="speakInLanguage('hi')" class="w-full bg-gray-600 hover:bg-gray-700 text-white font-bold py-3 px-4 rounded-xl transition duration-300">हिन्दी (Hindi)</button>
                <button onclick="speakInLanguage('ta')" class="w-full bg-gray-600 hover:bg-gray-700 text-white font-bold py-3 px-4 rounded-xl transition duration-300">தமிழ் (Tamil)</button>
            </div>
            <button onclick="hideLanguageModal()" class="mt-4 text-gray-400 hover:text-white transition">Cancel</button>
        </div>
    </div>


    <script>
        const chatContainer = document.getElementById('chat-container');
        const userInput = document.getElementById('user-input');
        const sendBtn = document.getElementById('send-btn');
        const hintBtn = document.getElementById('hint-btn');
        const infoModal = document.getElementById('info-modal');
        const modalContent = document.getElementById('modal-content');
        const modalTitle = document.getElementById('modal-title');
        const modalText = document.getElementById('modal-text');
        const modalIcon = document.getElementById('modal-icon');
        const nextLevelBtn = document.getElementById('next-level-btn');
        const modalSpeakerIcon = document.getElementById('modal-speaker-icon');
        const languageModal = document.getElementById('language-modal');
        const languageModalContent = document.getElementById('language-modal-content');
        
        // Aadhaar Check Modal Elements
        const aadhaarCheckModal = document.getElementById('aadhaar-check-modal');
        const aadhaarStep1 = document.getElementById('aadhaar-step-1');
        const aadhaarStep2 = document.getElementById('aadhaar-step-2');
        const aadhaarStep3 = document.getElementById('aadhaar-step-3');
        const aadhaarStep4 = document.getElementById('aadhaar-step-4');
        const aadhaarNumberInput = document.getElementById('aadhaar-number');
        const aadhaarNextBtn = document.getElementById('aadhaar-next-btn');
        const aadhaarError = document.getElementById('aadhaar-error');
        const captchaCode = document.getElementById('captcha-code');
        const captchaInput = document.getElementById('captcha-input');
        const captchaVerifyBtn = document.getElementById('captcha-verify-btn');
        const captchaError = document.getElementById('captcha-error');
        const otpInput = document.getElementById('otp-input');
        const otpVerifyBtn = document.getElementById('otp-verify-btn');
        const otpError = document.getElementById('otp-error');
        const aadhaarCloseBtn = document.getElementById('aadhaar-close-btn');

        let textToSpeak = null;
        let generatedCaptcha = '';

        const speakerSVG = `<svg xmlns="http://www.w3.org/2000/svg" width="20" height="20" fill="currentColor" class="bi bi-volume-up-fill" viewBox="0 0 16 16"><path d="M11.536 14.01A8.473 8.473 0 0 0 14.026 8a8.473 8.473 0 0 0-2.49-6.01l-.708.707A7.476 7.476 0 0 1 13.025 8c0 2.071-.84 3.946-2.197 5.303l.708.707z"/><path d="M10.121 12.596A6.48 6.48 0 0 0 12.025 8a6.48 6.48 0 0 0-1.904-4.596l-.707.707A5.482 5.482 0 0 1 11.025 8a5.482 5.482 0 0 1-1.61 3.89l.706.706z"/><path d="M8.707 11.182A4.486 4.486 0 0 0 10.025 8a4.486 4.486 0 0 0-1.318-3.182L8 5.525A3.489 3.489 0 0 1 9.025 8 3.49 3.49 0 0 1 8 10.475l.707.707zM6.717 3.55A.5.5 0 0 1 7 4v8a.5.5 0 0 1-.812.39L3.825 10.5H1.5A.5.5 0 0 1 1 10V6a.5.5 0 0 1 .5-.5h2.325l2.363-1.89a.5.5 0 0 1 .529-.06z"/></svg>`;
        const botAvatarSVG = `<svg class="h-8 w-8 text-indigo-300" viewBox="0 0 24 24" fill="currentColor" xmlns="http://www.w3.org/2000/svg"><path d="M12 2C6.48 2 2 6.48 2 12s4.48 10 10 10 10-4.48 10-10S17.52 2 12 2zm0 18c-4.41 0-8-3.59-8-8s3.59-8 8-8 8 3.59 8 8-3.59 8-8 8z"/><circle cx="8.5" cy="11.5" r="1.5"/><circle cx="15.5" cy="11.5" r="1.5"/><path d="M12 14c-2.33 0-4.31 1.46-5.11 3.5h10.22c-.8-2.04-2.78-3.5-5.11-3.5z"/></svg>`;

        const allQuestions = [
            {
                question: { en: "I have branches, but no fruit, trunk, or leaves. What am I?", hi: "मेरी शाखाएँ हैं, पर फल, तना या पत्ते नहीं। मैं क्या हूँ?", ta: "எனக்கு கிளைகள் உண்டு, ஆனால் பழம், தண்டு, அல்லது இலைகள் இல்லை. நான் யார்?" },
                answers: ["bank", "बैंक"],
                hint: { en: "Think of a place with tellers and vaults.", hi: "एक ऐसी जगह के बारे में सोचें जहाँ टेलर और तिजोरियाँ हों।", ta: "டெల్లர்கள் மற்றும் பெட்டகங்களைக் கொண்ட ஒரு இடத்தைப் பற்றி சிந்தியுங்கள்." },
                info: { en: "Correct! The answer is a Bank.\n\nBut here's a key point: just Linking Aadhaar is for ID proof. For scholarships, your account needs to be 'Aadhaar Seeded' for Direct Benefit Transfer (DBT).\n\nWithout seeding, scholarship payments face delays. Always ask your bank: 'Is my account DBT-enabled and Aadhaar Seeded?'", hi: "सही! जवाब है बैंक।\n\nलेकिन यहाँ एक महत्वपूर्ण बात है: सिर्फ आधार लिंक करना पहचान प्रमाण के लिए है। छात्रवृत्ति के लिए, आपके खाते को डायरेक्ट बेनिफिट ट्रांसफर (डीबीटी) के लिए 'आधार सीडेड' होना चाहिए।\n\nसीडिंग के बिना, छात्रवृत्ति भुगतान में देरी होती है। हमेशा अपने बैंक से पूछें: 'क्या मेरा खाता डीबीटी-सक्षम और आधार सीडेड है?'", ta: "சரி! பதில் வங்கி.\n\nஆனால் இங்கே ஒரு முக்கிய குறிப்பு: ஆதார் இணைப்பது அடையாளச் சான்றுக்கு மட்டுமே. கல்வி உதவித்தொகைக்கு, உங்கள் கணக்கு நேரடிப் பலன் பரிமாற்றத்திற்காக (DBT) 'ஆதார் விதைக்கப்பட்டதாக' இருக்க வேண்டும்.\n\nவிதைக்கப்படாவிட்டால், கல்வி உதவித்தொகை செலுத்துவதில் தாமதம் ஏற்படும். எப்போதும் உங்கள் வங்கியிடம் கேளுங்கள்: 'எனது கணக்கு DBT-இயக்கப்பட்டது மற்றும் ஆதார் விதைக்கப்பட்டதா?'" }
            },
            {
                question: { en: "For scholarships, your account needs to be enabled for D B T. What does 'B' stand for in DBT?", hi: "छात्रवृत्ति के लिए, आपके खाते को डी बी टी के लिए सक्षम होना चाहिए। डीबीटी में 'बी' का क्या अर्थ है?", ta: "கல்வி உதவித்தொகைக்கு, உங்கள் கணக்கு D B Tக்கு இயக்கப்பட்டிருக்க வேண்டும். DBTயில் 'B' என்பது எதைக் குறிக்கிறது?" },
                answers: ["benefit", "बेनिफिट"],
                hint: { en: "It's the 'B' in DBT, which you receive from a government scheme.", hi: "यह डीबीटी में 'बी' है, जो आपको एक सरकारी योजना से मिलता है।", ta: "இது DBTயில் உள்ள 'B', இது அரசாங்கத் திட்டத்திலிருந்து நீங்கள் பெறுவது." },
                info: { en: "Exactly! DBT stands for Direct Benefit Transfer.\n\nThis is the system the government uses to send scholarship money and other subsidies directly to your Aadhaar-seeded bank account. It's fast, secure, and reduces errors.", hi: "बिल्कुल! डीबीटी का मतलब डायरेक्ट बेनिफिट ट्रांसफर है।\n\nयह वह प्रणाली है जिसका उपयोग सरकार छात्रवृत्ति के पैसे और अन्य सब्सिडी सीधे आपके आधार-सीडेड बैंक खाते में भेजने के लिए करती है। यह तेज़, सुरक्षित है और त्रुटियों को कम करता है।", ta: "சரியாக! DBT என்பது நேரடிப் பலன் பரிமாற்றம்.\n\nஅரசாங்கம் கல்வி உதவித்தொகை மற்றும் பிற மானியங்களை உங்கள் ஆதார்-விதைக்கப்பட்ட வங்கிக் கணக்கிற்கு நேரடியாக அனுப்பப் பயன்படுத்தும் அமைப்பு இது. இது வேகமானது, பாதுகாப்பானது மற்றும் பிழைகளைக் குறைக்கிறது." }
            },
            {
                question: { en: "True or False: Linking your Aadhaar to a bank account automatically makes it ready for DBT scholarship payments.", hi: "सही या गलत: अपने आधार को बैंक खाते से लिंक करने से यह स्वचालित रूप से डीबीटी छात्रवृत्ति भुगतान के लिए तैयार हो जाता है।", ta: "சரி அல்லது தவறு: உங்கள் ஆதாரை வங்கிக் கணக்குடன் இணைப்பது தானாகவே DBT கல்வி உதவித்தொகை செலுத்துதல்களுக்குத் தயாராகிவிடும்." },
                answers: ["false", "गलत"],
                hint: { en: "Consider if official bank procedures are usually automatic or require a specific request.", hi: "विचार करें कि क्या आधिकारिक बैंक प्रक्रियाएं आमतौर पर தானாகவே நடைபெறுமா அல்லது ஒரு குறிப்பிட்ட கோரிக்கை தேவையா என்பதைக் கவனியுங்கள்." },
                info: { en: "You got it! It's FALSE.\n\nThis is the most common mistake. Linking and Seeding are two different processes. You must explicitly request your bank to 'Seed' your Aadhaar for DBT. Don't assume linking is enough!", hi: "आप समझ गए! यह गलत है।\n\nयह सबसे आम गलती है। लिंकिंग और सीडिंग दो अलग-अलग प्रक्रियाएं हैं। आपको डीबीटी के लिए अपने आधार को 'सीड' करने के लिए अपने बैंक से स्पष्ट रूप से अनुरोध करना होगा। यह न मानें कि लिंकिंग ही काफी है!", ta: "நீங்கள் சரியாகச் சொன்னீர்கள்! இது தவறு.\n\nஇது மிகவும் பொதுவான தவறு. இணைத்தல் மற்றும் விதைத்தல் இரண்டு வெவ்வேறு செயல்முறைகள். DBTக்காக உங்கள் ஆதாரை 'விதைக்க' உங்கள் வங்கியை நீங்கள் வெளிப்படையாகக் கோர வேண்டும். இணைப்பது மட்டும் போதும் என்று கருத வேண்டாம்!" }
            },
            {
                question: { en: "Which is the MOST reliable way to confirm your account is DBT-enabled? (Type A, B, C, or D)\nA) Checking SMS\nB) Using a UPI app\nC) Visiting your bank branch\nD) Calling customer care", hi: "यह पुष्टि करने का सबसे विश्वसनीय तरीका कौन सा है कि आपका खाता डीबीटी-सक्षम है? (ए, बी, सी, या डी टाइप करें)\nए) एसएमएस की जांच करना\nबी) यूपीआई ऐप का उपयोग करना\nसी) अपनी बैंक शाखा में जाना\nडी) कस्टमर केयर को कॉल करना", ta: "உங்கள் கணக்கு DBT-இயக்கப்பட்டது என்பதை உறுதிப்படுத்த மிகவும் நம்பகமான வழி எது? (A, B, C, அல்லது D என தட்டச்சு செய்யவும்)\nA) SMS சரிபார்ப்பது\nB) UPI செயலியைப் பயன்படுத்துவது\nC) உங்கள் வங்கி கிளைக்குச் செல்வது\nD) வாடிக்கையாளர் சேவையை அழைப்பது" },
                answers: ["c", "सी"],
                hint: { en: "Which option involves talking to a person face-to-face for a guaranteed confirmation?", hi: "किस विकल्प में गारंटीकृत पुष्टि के लिए किसी व्यक्ति से आमने-सामने बात करना शामिल है?", ta: "உறுதியான உறுதிப்படுத்தலுக்கு ஒரு நபருடன் நேருக்கு நேர் பேசுவதை எந்த விருப்பம் உள்ளடக்கியது?" },
                info: { en: "Perfect! The answer is C.\n\nWhile other methods might give clues, the only guaranteed way is to visit your bank branch and ask an official to confirm that your account is mapped in the NPCI database for DBT. This ensures your scholarship finds its way to you!", hi: "बहुत बढ़िया! जवाब है सी।\n\nहालांकि अन्य तरीके संकेत दे सकते हैं, लेकिन एकमात्र गारंटीकृत तरीका यह है कि आप अपनी बैंक शाखा में जाएं और एक अधिकारी से यह पुष्टि करने के लिए कहें कि आपका खाता डीबीटी के लिए एनपीसीआई डेटाबेस में मैप किया गया है। यह सुनिश्चित करता है कि आपकी छात्रवृत्ति आप तक पहुंच जाए!", ta: "சரியானது! பதில் C.\n\nமற்ற முறைகள் துப்புகளைத் தரக்கூடும், ஆனால் உங்கள் கணக்கு DBTக்காக NPCI தரவுத்தளத்தில் வரைபடமாக்கப்பட்டுள்ளது என்பதை உறுதிப்படுத்த உங்கள் வங்கிக் கிளைக்குச் சென்று ஒரு அதிகாரியிடம் கேட்பதே ஒரே உறுதியான வழியாகும். இது உங்கள் கல்வி உதவித்தொகை உங்களை வந்தடைவதை உறுதி செய்கிறது!" }
            }
        ];

        let availableQuestions = [];
        let currentQuestionIndex = 0;
        let tamilVoice = null;

        function setTamilVoice() {
            const voices = speechSynthesis.getVoices();
            if (voices.length === 0) return;
            tamilVoice = voices.find(voice => voice.lang === 'ta-IN') || voices.find(voice => voice.lang === 'ta');
        }

        function speakText(text, lang) {
            speechSynthesis.cancel();
            const utterance = new SpeechSynthesisUtterance(text);
            utterance.lang = lang;
            if (lang === 'ta' && tamilVoice) {
                utterance.voice = tamilVoice;
            }
            speechSynthesis.speak(utterance);
        }

        function addMessage(textObj, sender, isRaw = false) {
            const messageDiv = document.createElement('div');
            messageDiv.classList.add('mb-4', 'flex', 'items-end');
            
            if (sender === 'user') {
                 messageDiv.classList.add('justify-end');
            } else {
                 messageDiv.classList.add('justify-start');
                 const avatar = document.createElement('div');
                 avatar.className = 'bot-avatar';
                 avatar.innerHTML = botAvatarSVG;
                 messageDiv.appendChild(avatar);
            }

            const bubble = document.createElement('div');
            bubble.classList.add('px-4', 'py-3', 'rounded-xl', 'max-w-xs', 'lg:max-w-md', 'break-words', 'animate-fade-in');
            
            if (sender === 'user') {
                bubble.classList.add('bg-indigo-600', 'text-white', 'rounded-br-none');
                bubble.textContent = textObj;
            } else {
                bubble.classList.add('bg-gray-700', 'text-white', 'rounded-bl-none');
                const textContent = textObj.en.replace(/\n/g, '<br>');
                if (isRaw) {
                    bubble.innerHTML = textContent;
                } else {
                    bubble.textContent = textObj.en;
                }

                const speaker = document.createElement('span');
                speaker.className = 'speaker-icon';
                speaker.innerHTML = speakerSVG;
                bubble.appendChild(speaker);
                speaker.onclick = () => showLanguageModal(textObj);
            }
            
            messageDiv.appendChild(bubble);
            chatContainer.appendChild(messageDiv);
            chatContainer.scrollTop = chatContainer.scrollHeight;
        }

        function showInfoModal() {
            const currentQuestion = allQuestions[currentQuestionIndex];
            const info = currentQuestion.info;
            modalIcon.innerHTML = `<svg class="w-10 h-10 text-white" fill="none" stroke="currentColor" viewBox="0 0 24 24" xmlns="http://www.w3.org/2000/svg"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M9 12l2 2 4-4m6 2a9 9 0 11-18 0 9 9 0 0118 0z"></path></svg>`;
            modalText.textContent = info.en;
            modalSpeakerIcon.innerHTML = `<span class="speaker-icon">${speakerSVG}</span>`;
            modalSpeakerIcon.onclick = () => showLanguageModal(info);

            infoModal.classList.remove('hidden');
            setTimeout(() => {
                infoModal.classList.remove('opacity-0');
                modalContent.classList.remove('scale-95');
                modalContent.classList.add('pulse-once');
            }, 10);
        }

        function hideInfoModal() {
            infoModal.classList.add('opacity-0');
            modalContent.classList.add('scale-95');
            setTimeout(() => {
                infoModal.classList.add('hidden');
            }, 300);
            speechSynthesis.cancel();
        }

        function showLanguageModal(textObj) {
            textToSpeak = textObj;
            languageModal.classList.remove('hidden');
            setTimeout(() => {
                languageModal.classList.remove('opacity-0');
                languageModalContent.classList.remove('scale-95');
            }, 10);
        }

        function hideLanguageModal() {
            languageModal.classList.add('opacity-0');
            languageModalContent.classList.add('scale-95');
            setTimeout(() => {
                languageModal.classList.add('hidden');
            }, 300);
        }
        
        function speakInLanguage(lang) {
            if (textToSpeak && textToSpeak[lang]) {
                speakText(textToSpeak[lang], lang);
            }
            hideLanguageModal();
        }

        function handleGameCompletion() {
            const completionMessage = {
                en: "Congratulations! You've completed all the puzzles. You're now more aware about Aadhaar Seeding for DBT! and please visit National Scholarship Portal: NSP 🎉",
                hi: "बधाई हो! आपने सभी पहेलियाँ पूरी कर ली हैं। अब आप आधार सीडिंग और डीबीटी के बारे में अधिक जागरूक हैं! और कृपया राष्ट्रीय छात्रवृत्ति पोर्टल: एनएसपी पर जाएँ 🎉",
                ta: "வாழ்த்துக்கள்! நீங்கள் எல்லா புதிர்களையும் முடித்துவிட்டீர்கள். ஆதார் விதைப்பு மற்றும் DBT பற்றி நீங்கள் இப்போது நன்கு அறிவீர்கள்! மற்றும் தேசிய கல்வி உதவித்தொகை போர்ட்டலைப் பார்வையிடவும்: NSP 🎉"
            };
            addMessage(completionMessage, 'bot');

            setTimeout(() => {
                const finalActionsDiv = document.createElement('div');
                finalActionsDiv.id = 'final-actions';
                finalActionsDiv.classList.add('p-4', 'bg-gray-700/80', 'rounded-xl', 'mt-4', 'text-center', 'border', 'border-indigo-500');
                finalActionsDiv.innerHTML = `
                    <p class="text-sm text-indigo-200 mb-4 font-semibold">
                        Hint: If your Aadhaar is seeded with DBT, please visit the NSP Scholarship portal for more information.
                    </p>
                    <div class="flex flex-col sm:flex-row justify-center items-center gap-4">
                        <button id="play-again-btn" class="bg-gray-600 hover:bg-gray-500 text-white font-bold py-3 px-6 rounded-xl transition duration-300 w-full sm:w-auto">
                            Play Again
                        </button>
                        <a href="https://scholarships.gov.in/" target="_blank" rel="noopener noreferrer" class="bg-green-600 hover:bg-green-500 text-white font-bold py-3 px-6 rounded-xl transition duration-300 w-full sm:w-auto text-center inline-block">
                            National Scholarship Portal: NSP
                        </a>
                    </div>
                `;

                chatContainer.appendChild(finalActionsDiv);
                chatContainer.scrollTop = chatContainer.scrollHeight;

                document.getElementById('play-again-btn').addEventListener('click', () => {
                    finalActionsDiv.remove();
                    init();
                }, { once: true });
            }, 1000);
        }

        function askNewQuestion() {
            if (currentQuestionIndex >= allQuestions.length) {
                handleGameCompletion();
                return;
            }
            
            const currentQuestion = allQuestions[currentQuestionIndex];
            hintBtn.disabled = false;
            setTimeout(() => {
                const isMultiLine = currentQuestion.question.en.includes('\n');
                addMessage(currentQuestion.question, 'bot', isMultiLine);
            }, 500);
        }

        function handleUserInput() {
            const text = userInput.value.trim().toLowerCase();
            if (!text) return;

            addMessage(userInput.value, 'user');
            userInput.value = '';
            
            const currentQuestion = allQuestions[currentQuestionIndex];
            if (currentQuestion.answers.some(ans => text.includes(ans))) {
                setTimeout(() => {
                    addMessage({en: "That's correct! Well done! 👏"}, 'bot');
                    setTimeout(showInfoModal, 1000);
                }, 500);
            } else {
                setTimeout(() => {
                    addMessage({en: "Not quite. Give it another thought! 🤔"}, 'bot');
                }, 500);
            }
        }
        
        nextLevelBtn.addEventListener('click', () => {
            hideInfoModal();
            currentQuestionIndex++;
            
            if (currentQuestionIndex === 2) { // After the 2nd question, show the simulation offer
                showAadhaarCheckOffer();
            } else {
                setTimeout(() => {
                    addMessage({en: "Here's your next challenge!"}, 'bot');
                    askNewQuestion();
                }, 500);
            }
        });

        sendBtn.addEventListener('click', handleUserInput);
        userInput.addEventListener('keypress', (e) => {
            if (e.key === 'Enter') {
                handleUserInput();
            }
        });

        hintBtn.addEventListener('click', () => {
            const currentQuestion = allQuestions[currentQuestionIndex];
            addMessage(currentQuestion.hint, 'bot');
            hintBtn.disabled = true;
        });

        // --- Aadhaar Check Simulation Logic ---

        function showAadhaarCheckOffer() {
            const offerDiv = document.createElement('div');
            offerDiv.classList.add('p-4', 'bg-gray-700/80', 'rounded-xl', 'mt-4', 'text-center', 'border', 'border-indigo-500');
            offerDiv.innerHTML = `
                <p class="text-white mb-4">Would you like to try a simulation of how to check your Aadhaar-Bank link status online?</p>
                <button id="start-simulation-btn" class="bg-indigo-600 hover:bg-indigo-700 text-white font-bold py-2 px-4 rounded-xl">Yes, let's practice!</button>
            `;
            chatContainer.appendChild(offerDiv);
            chatContainer.scrollTop = chatContainer.scrollHeight;
            
            document.getElementById('start-simulation-btn').addEventListener('click', () => {
                offerDiv.remove();
                showAadhaarCheckModal();
            }, { once: true });
        }
        
        function showAadhaarCheckModal() {
            // Reset modal to step 1
            aadhaarStep1.classList.remove('hidden');
            aadhaarStep2.classList.add('hidden');
            aadhaarStep3.classList.add('hidden');
            aadhaarStep4.classList.add('hidden');
            aadhaarNumberInput.value = '';
            captchaInput.value = '';
            otpInput.value = '';
            aadhaarError.classList.add('hidden');
            captchaError.classList.add('hidden');
            otpError.classList.add('hidden');

            aadhaarCheckModal.classList.remove('hidden');
            setTimeout(() => {
                aadhaarCheckModal.classList.remove('opacity-0');
                aadhaarCheckModal.querySelector('.transform').classList.remove('scale-95');
            }, 10);
        }

        function hideAadhaarCheckModal() {
            aadhaarCheckModal.querySelector('.transform').classList.add('scale-95');
            aadhaarCheckModal.classList.add('opacity-0');
            setTimeout(() => {
                aadhaarCheckModal.classList.add('hidden');
            }, 300);
        }
        
        function generateCaptcha() {
            const chars = 'ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz0123456789';
            let result = '';
            for (let i = 0; i < 6; i++) {
                result += chars.charAt(Math.floor(Math.random() * chars.length));
            }
            generatedCaptcha = result;
            captchaCode.textContent = generatedCaptcha;
        }

        aadhaarNextBtn.addEventListener('click', () => {
            if (aadhaarNumberInput.value.length === 12 && /^\d+$/.test(aadhaarNumberInput.value)) {
                aadhaarError.classList.add('hidden');
                aadhaarStep1.classList.add('hidden');
                generateCaptcha();
                aadhaarStep2.classList.remove('hidden');
            } else {
                aadhaarError.classList.remove('hidden');
            }
        });

        captchaVerifyBtn.addEventListener('click', () => {
            if (captchaInput.value.toLowerCase() === generatedCaptcha.toLowerCase()) {
                captchaError.classList.add('hidden');
                aadhaarStep2.classList.add('hidden');
                aadhaarStep3.classList.remove('hidden');
            } else {
                captchaError.classList.remove('hidden');
                generateCaptcha();
            }
        });
        
        otpVerifyBtn.addEventListener('click', () => {
            if (otpInput.value.length === 6 && /^\d+$/.test(otpInput.value)) {
                otpError.classList.add('hidden');
                aadhaarStep3.classList.add('hidden');
                aadhaarStep4.classList.remove('hidden');
            } else {
                otpError.classList.remove('hidden');
            }
        });

        aadhaarCloseBtn.addEventListener('click', () => {
            hideAadhaarCheckModal();
            setTimeout(() => {
                 addMessage({en: "Great practice! Now, for your next real challenge..."}, 'bot');
                 askNewQuestion();
            }, 500);
        });

        function init() {
            // Clear chat
            chatContainer.innerHTML = '';

            const initialGreeting = {
                en: "Hi! I'm RAWEN. Let's play a game to test your wits and learn something important! 🧠",
                hi: "नमस्ते! मैं रावेन हूँ। अपनी बुद्धि का परीक्षण करने और कुछ महत्वपूर्ण सीखने के लिए एक खेल खेलते हैं! 🧠",
                ta: "வணக்கம்! நான் ரேவன். உங்கள் அறிவைச் சோதித்து முக்கியமான ஒன்றைக் கற்றுக்கொள்ள ஒரு விளையாட்டு விளையாடுவோம்! 🧠"
            };
            addMessage(initialGreeting, 'bot');
            
             if ('speechSynthesis' in window) {
                speechSynthesis.onvoiceschanged = setTamilVoice;
                setTamilVoice();
            }
            
            currentQuestionIndex = 0;
            const finalActions = document.getElementById('final-actions');
            if(finalActions) finalActions.remove();

            askNewQuestion();
        }

        init();
    </script>
</body>
</html>
