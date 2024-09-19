// ==UserScript==
// @name         Memefi Bot [SmartBot] Enhanced
// @namespace    https://smartbot.black/
// @version      1.8.0
// @description  Bot for playing Memefi on Telegram with UI and advanced features including Turbo Boost, AutoSpin, and game pause/resume
// @author       Smartbot Team
// @match        https://tg-app.memefi.club/*
// @icon         https://www.memefi.club/images/favicons/android-chrome-96x96.png?v=5
// @grant        none
// ==/UserScript==

(() => {
    let isRun = false;
    let isGamePaused = false;
    let settings = {
        minClickDelay: 1e-6, // Minimum delay between clicks in seconds
        maxClickDelay: 5e-6, // Maximum delay between clicks in seconds
        autoSpin: true, // Enable auto spinning
        autoTurbo: true, // Enable turbo tap mode
        energyThreshold: 20 // Minimum energy threshold to trigger the bot
    };

    const LAST_RUN_CLAIM = "lastRunClaim";
    const logPrefix = '[SmartBot]';
    const styles = {
        success: 'color: green; font-weight: bold;',
        error: 'color: red; font-weight: bold;'
    };

    function getRandomInt(min, max) {
        return Math.floor(Math.random() * (max - min + 1)) + min;
    }

    function getEnergy() {
        return (
            (document.querySelector('g[clip-path="url(#clip0_21455_16555)"]')
                ?.parentElement?.nextSibling?.querySelector("span")
                ?.innerText?.replace(",", "") ?? 0) * 1
        );
    }

    function getMaxEnergy() {
        return (
            (document.querySelector('g[clip-path="url(#clip0_21455_16555)"]')
                ?.parentElement?.nextSibling?.querySelector("span:nth-child(2)")
                ?.innerText?.replace(",", "")
                ?.replace("/", "") ?? 0) * 1
        );
    }

    function createTouch(target, x, y) {
        return new Touch({
            identifier: Date.now(),
            target: target,
            clientX: x,
            clientY: y,
            radiusX: 2.5,
            radiusY: 2.5,
            rotationAngle: 0,
            force: 0.5,
        });
    }

    function clickAtRandomIntervals(minX, maxX, minY, maxY) {
        function clickRandom() {
            if (isGamePaused) return;

            const x = getRandomInt(minX, maxX);
            const y = getRandomInt(minY, maxY);

            const pointerEventInit = {
                pointerId: 1,
                bubbles: true,
                cancelable: true,
                composed: true,
                view: window,
                detail: 1,
                screenX: x,
                screenY: y,
                clientX: x,
                clientY: y,
                pointerType: "touch",
                width: 1,
                height: 1,
                pressure: 0.5,
                isPrimary: true,
            };

            const element = document.elementFromPoint(x, y);

            if (element) {
                const pointerDownEvent = new PointerEvent("pointerdown", pointerEventInit);
                element.dispatchEvent(pointerDownEvent);
                console.log(`Pointer down at (${x}, ${y}) on element:`, element);
            }

            const delay = getRandomInt(settings.minClickDelay * 1e9, settings.maxClickDelay * 1e9);
            setTimeout(clickRandom, delay);
        }

        clickRandom();
    }

    function toggleUIVisibility(hide) {
        const uiElement = document.getElementById("memefi-bot-ui");
        if (hide) {
            uiElement.style.display = "none";
            localStorage.setItem("uiVisible", "false");
        } else {
            uiElement.style.display = "block";
            localStorage.setItem("uiVisible", "true");
        }
    }

    function startBot() {
        if (isGamePaused) return;

        if (getEnergy() < settings.energyThreshold) {
            alert("Not enough energy to start the bot!");
            return;
        }

        isRun = true;
        toggleUIVisibility(true); // Minimize UI
        const bodyRect = document.body.getBoundingClientRect();
        clickAtRandomIntervals(0, bodyRect.width, 0, bodyRect.height);
    }

    function checkAndClickTurboBoost() {
        const turboBoostElement = document.querySelector('img[src="/_MOCKED_ICONS_/turbo-boost.svg"]');

        if (turboBoostElement) {
            turboBoostElement.click();
            console.log(`${logPrefix} Turbo Boost clicked!`, styles.success);
            setTimeout(() => {
                const specialElement = document.querySelector('div.sc-braxZu.gmKjLQ');
                if (specialElement && specialElement.style.display === 'block') {
                    console.log(`${logPrefix} Special element detected. Starting fast auto-clicker...`, styles.success);
                    settings.minClickDelay = 10e-6; // Set faster click delay
                    clickAtRandomIntervals(0, document.body.scrollWidth, 0, document.body.scrollHeight);
                }
            }, 1000);
        }

        setTimeout(checkAndClickTurboBoost, 2000);
    }

    function toggleAutoTurbo() {
        settings.autoTurbo = !settings.autoTurbo;
        autoTurboButton.textContent = settings.autoTurbo ? 'Auto Use Turbo: On' : 'Auto Use Turbo: Off';
        autoTurboButton.style.backgroundColor = settings.autoTurbo ? '#98c379' : '#e06c75';
    }

    function toggleAutoSpin() {
        settings.autoSpin = !settings.autoSpin;
        autoSpinButton.textContent = settings.autoSpin ? 'AutoSpin: On' : 'AutoSpin: Off';
        autoSpinButton.style.backgroundColor = settings.autoSpin ? '#98c379' : '#e06c75';
    }

    function toggleGamePause() {
        isGamePaused = !isGamePaused;
        pauseResumeButton.textContent = isGamePaused ? 'Resume' : 'Pause';
        pauseResumeButton.style.backgroundColor = isGamePaused ? '#e5c07b' : '#98c379';
    }

    function createSettingElement(label, id, type, min, max, step, tooltip) {
        const container = document.createElement('div');
        container.className = 'setting-element';

        const labelElem = document.createElement('label');
        labelElem.textContent = label;
        container.appendChild(labelElem);

        const inputElem = document.createElement('input');
        inputElem.id = id;
        inputElem.type = type;
        inputElem.min = min;
        inputElem.max = max;
        inputElem.step = step;
        inputElem.value = settings[id.replace('minClickDelay', 'minClickDelay').replace('maxClickDelay', 'maxClickDelay')];
        container.appendChild(inputElem);

        const displayElem = document.createElement('span');
        displayElem.id = `${id}Display`;
        displayElem.textContent = inputElem.value;
        container.appendChild(displayElem);

        inputElem.oninput = () => {
            displayElem.textContent = inputElem.value;
        };

        const tooltipElem = document.createElement('div');
        tooltipElem.className = 'tooltip';
        tooltipElem.innerHTML = tooltip;
        container.appendChild(tooltipElem);

        return container;
    }

    function updateSettingsMenu() {
        document.getElementById('minClickDelay').value = settings.minClickDelay * 1e9;
        document.getElementById('minClickDelayDisplay').textContent = settings.minClickDelay * 1e9;
        document.getElementById('maxClickDelay').value = settings.maxClickDelay * 1e9;
        document.getElementById('maxClickDelayDisplay').textContent = settings.maxClickDelay * 1e9;
        autoTurboButton.textContent = settings.autoTurbo ? 'Auto Use Turbo: On' : 'Auto Use Turbo: Off';
        autoTurboButton.style.backgroundColor = settings.autoTurbo ? '#98c379' : '#e06c75';
        autoSpinButton.textContent = settings.autoSpin ? 'AutoSpin: On' : 'AutoSpin: Off';
        autoSpinButton.style.backgroundColor = settings.autoSpin ? '#98c379' : '#e06c75';
    }

    function createUI() {
        const ui = document.createElement("div");
        ui.id = "memefi-bot-ui";
        ui.style.position = "fixed";
        ui.style.top = "10px";
        ui.style.left = "10px";
        ui.style.padding = "10px";
        ui.style.backgroundColor = "#2d2d2d"; // Dark background for better visibility
        ui.style.color = "#ffffff"; // White text
        ui.style.border = "2px solid #ff5722"; // Bright orange border
        ui.style.zIndex = "99999";
        ui.style.borderRadius = "10px"; // Rounded corners
        ui.style.boxShadow = "0 0 10px rgba(0,0,0,0.5)"; // Shadow for depth
        ui.style.cursor = "move";

        ui.innerHTML = `
            <h3>Memefi Bot Settings</h3>
            <button id="minimizeUI" style="background-color: #ff5722; color: #ffffff; border: none; border-radius: 5px; padding: 5px 10px;">🗕</button>
            <div id="settingsContent">
                ${createSettingElement('Min Click Delay (ns):', 'minClickDelay', 'number', 0, 1e12, 1, 'Minimum delay between clicks in nanoseconds.').outerHTML}
                <br>
                ${createSettingElement('Max Click Delay (ns):', 'maxClickDelay', 'number', 0, 1e12, 1, 'Maximum delay between clicks in nanoseconds.').outerHTML}
                <br>
                ${createSettingElement('Energy Threshold:', 'energyThreshold', 'number', 0, 1000, 1, 'Energy threshold below which the bot will not start.').outerHTML}
                <br>
                <button id="saveSettings" style="background-color: #4caf50; color: #ffffff; border: none; border-radius: 5px; padding: 5px 10px;">Save Settings</button>
                <button id="startBot" style="background-color: #2196f3; color: #ffffff; border: none; border-radius: 5px; padding: 5px 10px;">Start Bot</button>
                <button id="hideUI" style="background-color: #f44336; color: #ffffff; border: none; border-radius: 5px; padding: 5px 10px;">Hide UI</button>
            </div>
        `;

        document.body.appendChild(ui);

        const minimizeButton = document.getElementById("minimizeUI");
        const settingsContent = document.getElementById("settingsContent");
        const hideUIButton = document.getElementById("hideUI");
        const showUIButton = document.createElement("button");
        showUIButton.id = "showUI";
        showUIButton.textContent = "Show UI";
        showUIButton.style.position = "fixed";
        showUIButton.style.bottom = "10px";
        showUIButton.style.left = "10px";
        showUIButton.style.backgroundColor = "#4caf50"; // Green background
        showUIButton.style.color = "#ffffff"; // White text
        showUIButton.style.border = "none";
        showUIButton.style.borderRadius = "5px";
        showUIButton.style.padding = "5px 10px";
        showUIButton.style.zIndex = "99999";

        document.body.appendChild(showUIButton);

        minimizeButton.onclick = () => {
            if (settingsContent.style.display === "none") {
                settingsContent.style.display = "block";
                minimizeButton.textContent = "🗕";
            } else {
                settingsContent.style.display = "none";
                minimizeButton.textContent = "🗗";
            }
        };

        hideUIButton.onclick = () => {
            toggleUIVisibility(true); // Hide UI
        };

        showUIButton.onclick = () => {
            toggleUIVisibility(false); // Show UI
        };

        document.getElementById("saveSettings").onclick = () => {
            settings.minClickDelay = parseInt(document.getElementById("minClickDelay").value, 10) / 1e9;
            settings.maxClickDelay = parseInt(document.getElementById("maxClickDelay").value, 10) / 1e9;
            settings.energyThreshold = parseInt(document.getElementById("energyThreshold").value, 10);
            updateSettingsMenu();
            alert("Settings saved!");
        };

        document.getElementById("startBot").onclick = startBot;

        // Initialize the UI visibility based on user preference
        if (localStorage.getItem("uiVisible") === "false") {
            toggleUIVisibility(true); // Initially hide UI
        } else {
            toggleUIVisibility(false); // Show UI initially
        }
    }

    createUI();

    // Initialize other functionalities
    if (settings.autoTurbo) {
        checkAndClickTurboBoost();
    }

    // Enable draggable UI
    const ui = document.getElementById("memefi-bot-ui");
    ui.onmousedown = (e) => {
        e.preventDefault();

        const offsetX = e.clientX - ui.getBoundingClientRect().left;
        const offsetY = e.clientY - ui.getBoundingClientRect().top;

        const onMouseMove = (event) => {
            ui.style.left = `${event.clientX - offsetX}px`;
            ui.style.top = `${event.clientY - offsetY}px`;
        };

        const onMouseUp = () => {
            document.removeEventListener("mousemove", onMouseMove);
            document.removeEventListener("mouseup", onMouseUp);
        };

        document.addEventListener("mousemove", onMouseMove);
        document.addEventListener("mouseup", onMouseUp);
    };
})();
