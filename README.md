<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Secret Santa</title>

    <!-- Google Analytics -->
    <script async src="https://www.googletagmanager.com/gtag/js?id=G-RNXWNGXZ0Z"></script>
    <script>
        window.dataLayer = window.dataLayer || [];
        function gtag(){dataLayer.push(arguments);}
        gtag('js', new Date());
        gtag('config', 'G-RNXWNGXZ0Z');
    </script>

    <style>
        body {
            font-family: Arial, sans-serif;
            background-color: #f0f8ff;
            text-align: center;
            position: relative;
        }
        h1 {
            color: #d62728;
            font-size: 36px;
            margin-bottom: 30px;
        }
        select, button {
            font-size: 18px;
            padding: 10px;
            margin: 10px 0;
        }
        .result {
            font-size: 24px;
            color: green;
            font-weight: bold;
            margin-top: 20px;
            position: relative;
            visibility: hidden;
        }
        .coal {
            font-size: 24px;
            color: black;
            font-weight: bold;
            margin-top: 20px;
            visibility: hidden;
            background-color: #d62728;
            padding: 10px;
            border-radius: 5px;
        }
        .countdown {
            font-size: 48px;
            font-weight: bold;
            margin-top: 30px;
            color: #d62728;
        }
    </style>
</head>
<body>

<h1>🎄 Secret Santa 🎄</h1>

<select id="participantList">
    <option value="" selected>Select your name</option>
</select>

<button onclick="assignSecretSanta(); trackEvent()">Find Your Secret Santa!</button>

<div class="result" id="result">Surprise!</div>
<div class="coal" id="coal">🎅 You've been naughty! You're getting coal this year! 🎅</div>
<div id="countdown" class="countdown"></div>

<script>
    // Define families and participants
    const families = {
        "Family1": ["Jane", "Allan", "Josie"],
        "Family2": ["Christina", "Andrew", "Hellie"],
        "Family3": ["Susan", "Sandy", "Kirstie"],
        "Family4": ["Susie P"]
    };

    const participants = [].concat(...Object.values(families)); // Flatten family arrays into a single participants list
    const participantList = document.getElementById("participantList");
    let selectedParticipant = '';
    let hasSelected = false;

    // Populate the dropdown list with participants
    participants.forEach(name => {
        let option = document.createElement("option");
        option.value = name;
        option.textContent = name;
        participantList.appendChild(option);
    });

    // Listen for participant selection
    participantList.addEventListener("change", function() {
        selectedParticipant = this.value;
    });

    // Function to get a cookie by name
    function getCookie(name) {
        let cookies = document.cookie.split(';');
        for (let cookie of cookies) {
            let [key, value] = cookie.trim().split('=');
            if (key === name) return value;
        }
        return null;
    }

    // Function to set a cookie
    function setCookie(name, value, days) {
        let date = new Date();
        date.setTime(date.getTime() + (days * 24 * 60 * 60 * 1000));
        document.cookie = `${name}=${value}; expires=${date.toUTCString()}; path=/`;
    }

    // Function to assign a Secret Santa
    function assignSecretSanta() {
        if (!selectedParticipant) {
            alert("Please select your name first!");
            return;
        }

        // Check if a participant has already selected
        if (getCookie('hasSelected')) {
            // If they've already selected, show coal message when trying again
            document.getElementById("coal").style.visibility = "visible";
            return;
        }

        // Mark as selected
        hasSelected = true;

        // Disable the dropdown after selection
        setCookie('hasSelected', 'true', 30); // Cookie expires in 30 days

        // Show countdown before revealing Secret Santa
        startCountdown();
    }

    // Countdown function
    function startCountdown() {
        let countdownElement = document.getElementById("countdown");
        let count = 3;
        countdownElement.textContent = count;

        let interval = setInterval(function() {
            count--;
            countdownElement.textContent = count;
            if (count === 0) {
                clearInterval(interval);  // Stop the countdown
                countdownElement.style.visibility = "hidden";  // Hide countdown
                revealResult();  // Reveal Secret Santa
            }
        }, 1000);
    }

    // Reveal the Secret Santa result with family check
    function revealResult() {
        // Get the family of the selected participant
        let selectedFamily = Object.keys(families).find(family => families[family].includes(selectedParticipant));

        // Filter potential receivers to exclude the same family
        let potentialReceivers = participants.filter(name => name !== selectedParticipant && !families[selectedFamily].includes(name));
        
        // Select a random receiver from the filtered list
        let selectedReceiver = potentialReceivers[Math.floor(Math.random() * potentialReceivers.length)];

        let resultElement = document.getElementById("result");
        resultElement.textContent = `Your Secret Santa is: ${selectedReceiver}!`;

        resultElement.style.visibility = "visible";
    }

    // Listen for changes in the dropdown after selection
    participantList.addEventListener('click', function() {
        if (getCookie('hasSelected')) {
            // If the participant has already selected, show coal message
            document.getElementById("coal").style.visibility = "visible";
        }
    });

    // Track button click event
    function trackEvent() {
        gtag('event', 'click', {
            'event_category': 'Button Clicks',
            'event_label': 'Find Your Secret Santa Button',
            'value': 1
        });
    }
</script>

</body>
</html>
