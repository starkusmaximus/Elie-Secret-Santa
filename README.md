// Initialize Google Analytics
(function() {
    const script = document.createElement('script');
    script.src = 'https://www.googletagmanager.com/gtag/js?id=G-RNXWNGXZ0Z';
    script.async = true;
    document.head.appendChild(script);

    window.dataLayer = window.dataLayer || [];
    function gtag() { dataLayer.push(arguments); }
    gtag('js', new Date());
    gtag('config', 'G-RNXWNGXZ0Z');
})();

// Main app initialization
document.addEventListener("DOMContentLoaded", () => {
    // Create and inject HTML elements
    document.body.innerHTML = `
        <h1>🎄 Secret Santa 🎄</h1>
        <select id="participantList">
            <option value="" selected>Select your name</option>
        </select>
        <button id="assignButton">Find Your Secret Santa!</button>
        <div class="result" id="result">Surprise!</div>
        <div class="coal" id="coal">🎅 You've been naughty! You're getting coal this year! 🎅</div>
        <div id="countdown" class="countdown"></div>
    `;

    // Define families and participants
    const families = {
        "Family1": ["Jane", "Allan", "Josie"],
        "Family2": ["Christina", "Andrew", "Hellie"],
        "Family3": ["Susan", "Sandy", "Kirstie"],
        "Family4": ["Susie P"]
    };
    const participants = [].concat(...Object.values(families));
    const participantList = document.getElementById("participantList");

    let selectedParticipant = '';
    let hasSelected = false;
    let secondSelectionInProgress = false;

    // Populate participant dropdown
    participants.forEach(name => {
        const option = document.createElement("option");
        option.value = name;
        option.textContent = name;
        participantList.appendChild(option);
    });

    // Track participant selection
    participantList.addEventListener("change", function() {
        selectedParticipant = this.value;
        gtag('event', 'name_selected', {
            'event_category': 'Selection',
            'event_label': selectedParticipant,
        });
    });

    // Set up button click event
    document.getElementById("assignButton").addEventListener("click", assignSecretSanta);

    function assignSecretSanta() {
        if (!selectedParticipant) {
            alert("Please select your name first!");
            return;
        }

        if (hasSelected) {
            if (!secondSelectionInProgress) {
                secondSelectionInProgress = true;
                startCountdown();
            }
            return;
        }

        hasSelected = true;
        startCountdown();
    }

    function startCountdown() {
        const countdownElement = document.getElementById("countdown");
        let count = 3;
        countdownElement.textContent = count;

        const interval = setInterval(() => {
            count--;
            countdownElement.textContent = count;

            if (count === 0) {
                clearInterval(interval);
                countdownElement.style.visibility = "hidden";

                if (secondSelectionInProgress) {
                    showCoalMessage();
                } else {
                    revealResult();
                }
            }
        }, 1000);
    }

    function showCoalMessage() {
        document.getElementById("coal").style.visibility = "visible";
        gtag('event', 'coal_message_displayed', {
            'event_category': 'Message',
            'event_label': 'Coal Message',
        });
    }

    function revealResult() {
        const selectedFamily = Object.keys(families).find(family => families[family].includes(selectedParticipant));
        const potentialReceivers = participants.filter(name => name !== selectedParticipant && !families[selectedFamily].includes(name));
        const selectedReceiver = potentialReceivers[Math.floor(Math.random() * potentialReceivers.length)];

        const resultElement = document.getElementById("result");
        resultElement.textContent = `Your Secret Santa is: ${selectedReceiver}!`;
        resultElement.style.visibility = "visible";

        gtag('event', 'santa_assigned', {
            'event_category': 'Assignment',
            'event_label': `Assigned to ${selectedReceiver}`,
        });
    }
});
