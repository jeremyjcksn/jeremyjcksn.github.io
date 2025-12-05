# jeremyjcksn-project4.github.io

:root {
    --primary-color: #3b82f6; /* Blue */
    --secondary-color: #6b7280; /* Gray */
    --success-color: #10b981; /* Green */
    --danger-color: #ef4444; /* Red */
    --background-color: #f3f4f6;
    --card-background: #ffffff;
}

body {
    font-family: 'Roboto', sans-serif;
    display: flex;
    justify-content: center;
    align-items: flex-start; 
    min-height: 100vh;
    background-color: var(--background-color);
    margin: 40px 0;
    color: #1f2937;
}

/* Overall container styling */
.app-card {
    background-color: var(--card-background);
    padding: 35px;
    border-radius: 12px;
    box-shadow: 0 10px 20px rgba(0, 0, 0, 0.1), 0 6px 6px rgba(0, 0, 0, 0.05);
    width: 90%;
    max-width: 450px;
    text-align: center;
}

h1 {
    color: var(--primary-color);
    font-weight: 700;
    margin-bottom: 25px;
    border-bottom: 2px solid #e5e7eb;
    padding-bottom: 10px;
}

.section-title {
    font-size: 1.25em;
    color: #4b5563;
    margin-top: 30px;
    margin-bottom: 15px;
    font-weight: 500;
}

/* Input Styling */
.input-group {
    text-align: left;
    margin-bottom: 20px;
}

.input-group label {
    display: block;
    margin-bottom: 5px;
    font-size: 0.9em;
    font-weight: 500;
    color: #4b5563;
}

#taskName {
    width: 100%;
    padding: 12px;
    border: 1px solid #d1d5db;
    border-radius: 6px;
    font-size: 1em;
    transition: border-color 0.3s, box-shadow 0.3s;
}

#taskName:focus {
    border-color: var(--primary-color);
    box-shadow: 0 0 0 3px rgba(59, 130, 246, 0.2);
    outline: none;
}

/* Timer Display Styling */
#timer-display {
    font-size: 4em;
    font-weight: 700;
    margin: 10px 0 30px;
    color: #1f2937;
    
    background-color: #eef2ff;
    padding: 15px 0;
    border-radius: 8px;
    letter-spacing: 2px;
}

/* Button Styling */
.controls button {
    padding: 10px 25px;
    margin: 0 8px;
    border: none;
    border-radius: 6px;
    cursor: pointer;
    font-size: 1em;
    font-weight: 600;
    transition: background-color 0.3s, transform 0.1s;
}

.primary-btn {
    background-color: var(--success-color);
    color: white;
}

.primary-btn:hover {
    background-color: #059669; /* Darker green on hover */
}

.primary-btn.running {
    background-color: var(--danger-color); /* Red for STOP state */
}

.primary-btn.running:hover {
    background-color: #b91c1c; /* Darker red on hover */
}

.secondary-btn {
    background-color: var(--secondary-color);
    color: white;
}

.secondary-btn:hover {
    background-color: #4b5563; /* Darker gray on hover */
}

.tertiary-btn { /* For Clear All Logs */
    background-color: #fecaca; /* Light red */
    color: var(--danger-color);
    padding: 8px 20px;
    margin-top: 15px;
}

.tertiary-btn:hover {
    background-color: #fca5a5;
}

/* Log Styling */
#log-list {
    list-style: none;
    padding: 0;
    margin-top: 10px;
}

#log-list li {
    background-color: #f9fafb; /* Very light gray for log entries */
    padding: 10px 15px;
    margin-bottom: 8px;
    border-radius: 6px;
    font-size: 0.9em;
    text-align: left;
    border-left: 4px solid var(--primary-color); /* Accent stripe */
    box-shadow: 0 1px 3px rgba(0, 0, 0, 0.05);
}

.log-placeholder {
    color: var(--secondary-color);
    text-align: center;
    border-left: none !important;
    font-style: italic;
    box-shadow: none !important;
}



<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Professional Task Timer</title>
    <link rel="stylesheet" href="task-timer.css">
    <link href="https://fonts.googleapis.com/css2?family=Roboto:wght@300;400;700&display=swap" rel="stylesheet">
</head>
<body>

    <div class="app-card">
        <h1>Productivity Timer</h1>

        <section id="current-task">
            <h2 class="section-title">Focus Task</h2>
            <div class="input-group">
                <label for="taskName">What are you working on?</label>
                <input type="text" id="taskName" placeholder="e.g., Finalizing Project Scope Document">
            </div>

            <div id="timer-display">00:00:00</div>

            <div class="controls">
                <button id="startStopButton" class="primary-btn">Start</button>
                <button id="resetButton" class="secondary-btn">Reset</button>
            </div>
        </section>

        <section id="task-log">
            <h2 class="section-title">Task History</h2>
            <ul id="log-list">
                <li class="log-placeholder">No tasks logged yet. Start timing!</li>
            </ul>
            <button id="clearLogButton" class="tertiary-btn">Clear All Logs</button>
        </section>
    </div>

    <script src="task-timer.js"></script>
</body>
</html>




// DOM Element References
const timerDisplay = document.getElementById('timer-display');
const startStopButton = document.getElementById('startStopButton');
const resetButton = document.getElementById('resetButton');
const clearLogButton = document.getElementById('clearLogButton');
const taskNameInput = document.getElementById('taskName');
const logList = document.getElementById('log-list');


let isRunning = false;
let startTime = 0;
let elapsedTime = 0;
let timerInterval;


function formatTime(ms) {
    const totalSeconds = Math.floor(ms / 1000);
    const hours = Math.floor(totalSeconds / 3600);
    const minutes = Math.floor((totalSeconds % 3600) / 60);
    const seconds = totalSeconds % 60;

    const pad = (num) => String(num).padStart(2, '0');

    return `${pad(hours)}:${pad(minutes)}:${pad(seconds)}`;
}


function startTimer() {
    startTime = Date.now() - elapsedTime;
    timerInterval = setInterval(function() {
        elapsedTime = Date.now() - startTime;
        timerDisplay.textContent = formatTime(elapsedTime);
    }, 1000); 
    isRunning = true;
    startStopButton.textContent = 'Stop';
    startStopButton.classList.add('running');
    taskNameInput.disabled = true; 
}

function stopTimer() {
    clearInterval(timerInterval);
    isRunning = false;
    startStopButton.textContent = 'Start';
    startStopButton.classList.remove('running');
    taskNameInput.disabled = false;
    logTask(); 
    resetTimer(); 
}

function resetTimer() {
    clearInterval(timerInterval);
    isRunning = false;
    elapsedTime = 0;
    timerDisplay.textContent = '00:00:00';
    startStopButton.textContent = 'Start';
    startStopButton.classList.remove('running');
    taskNameInput.disabled = false;
    if (clearInput) {
        taskNameInput.value = '';
    }
}


function logTask() {
    const taskName = taskNameInput.value.trim() || 'Untitled Task';
    
    // Convert ms to a readable duration string
    const totalSeconds = Math.floor(elapsedTime / 1000);
    const hours = Math.floor(totalSeconds / 3600);
    const minutes = Math.floor((totalSeconds % 3600) / 60);
    const seconds = totalSeconds % 60;
    
    let durationString = '';
    if (hours > 0) durationString += `${hours}h `;
    if (minutes > 0) durationString += `${minutes}m `;
    if (seconds > 0 || durationString === '') durationString += `${seconds}s`;

    if (totalSeconds === 0) return; 

    const finishTime = new Date().toLocaleTimeString();

    const listItem = document.createElement('li');
    listItem.textContent = `Task: ${taskName} | Duration: ${durationString.trim()} | Finished: ${finishTime}`;
    
    // Add the new log item to the top of the list
    logList.prepend(listItem); 
    taskNameInput.value = ''; 
}

function clearLog() {
    if (confirm("Are you sure you want to clear the entire log?")) {
        logList.innerHTML = '';
    }
}

// --- Event Listeners ---
startStopButton.addEventListener('click', () => {
    if (isRunning) {
        stopTimer();
    } else {
        startTimer();
    }
});

resetButton.addEventListener('click', () => {
    
    resetTimer(true); 
});

clearLogButton.addEventListener('click', clearLog);
