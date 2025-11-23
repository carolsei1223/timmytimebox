# timmytimebox
digital task organizer
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>TimmyTime Box</title>
    <!-- Load Tailwind CSS -->
    <script src="https://cdn.tailwindcss.com"></script>
    <style>
        /* Custom scrollbar for the brain dump area */
        .custom-scrollbar::-webkit-scrollbar {
            width: 8px;
        }
        .custom-scrollbar::-webkit-scrollbar-thumb {
            background-color: #6366f1; /* Indigo-500 */
            border-radius: 4px;
        }
        .custom-scrollbar::-webkit-scrollbar-track {
            background-color: #eef2ff; /* Indigo-50 */
        }
        .task-done {
            text-decoration: line-through;
            color: #9ca3af; /* Gray-400 */
        }
    </style>
</head>
<body class="bg-gray-50 min-h-screen p-4 sm:p-8 font-sans">

    <!-- Main Container mirroring the Time Box sheet -->
    <div class="max-w-4xl mx-auto bg-white rounded-xl shadow-2xl overflow-hidden border-4 border-indigo-600">
        
        <!-- Header -->
        <header class="p-6 bg-indigo-50 flex items-center justify-between">
            <h1 class="text-3xl font-extrabold text-indigo-800 tracking-tight">
                <span class="text-indigo-600">The</span> TimmyTime Box.
            </h1>
            <button id="processDayBtn" class="px-4 py-2 bg-green-500 text-white font-semibold rounded-lg shadow-md hover:bg-green-600 transition duration-150">
                <svg xmlns="http://www.w3.org/2000/svg" class="h-5 w-5 inline mr-2" viewBox="0 0 20 20" fill="currentColor">
                  <path fill-rule="evenodd" d="M10 18a8 8 0 100-16 8 8 0 000 16zm3.707-9.293a1 1 0 00-1.414-1.414L9 10.586 7.707 9.293a1 1 0 00-1.414 1.414l2 2a1 1 0 001.414 0l4-4z" clip-rule="evenodd" />
                </svg>
                Win The Day
            </button>
        </header>

        <!-- Main Content Area: Priorities, Brain Dump, and Schedule -->
        <main class="grid grid-cols-1 lg:grid-cols-3 divide-y lg:divide-y-0 lg:divide-x divide-indigo-200">
            
            <!-- Column 1: Top Priorities & Brain Dump -->
            <div class="col-span-1 lg:col-span-1 p-6 space-y-6">
                
                <!-- Top 3 Priorities -->
                <section>
                    <h2 class="text-xl font-bold text-indigo-700 mb-3 border-b pb-1">Top 3 Priorities (The WINS)</h2>
                    <div id="priorityList" class="space-y-3">
                        <!-- Priority Inputs will go here -->
                        <input type="text" id="p1" placeholder="P1: My absolute must-do WIN" class="w-full p-2 border border-indigo-300 rounded-md shadow-sm focus:ring-indigo-500 focus:border-indigo-500" maxlength="80">
                        <input type="text" id="p2" placeholder="P2: My second most important WIN" class="w-full p-2 border border-indigo-300 rounded-md shadow-sm focus:ring-indigo-500 focus:border-indigo-500" maxlength="80">
                        <input type="text" id="p3" placeholder="P3: My final WIN for the day" class="w-full p-2 border border-indigo-300 rounded-md shadow-sm focus:ring-indigo-500 focus:border-indigo-500" maxlength="80">
                    </div>
                    <p class="text-xs text-gray-500 mt-2">These will be sent to your Google Calendar.</p>
                </section>

                <!-- Brain Dump -->
                <section class="h-full flex flex-col">
                    <h2 class="text-xl font-bold text-indigo-700 mb-3 border-b pb-1">Brain Dump (Inbox)</h2>
                    <textarea id="brainDumpInput" placeholder="Type all tasks, thoughts, and random to-dos here. Hit 'Add' or type in the Schedule to timebox." 
                              class="w-full p-3 h-32 border border-indigo-300 rounded-md shadow-inner resize-none focus:ring-indigo-500 focus:border-indigo-500 custom-scrollbar"></textarea>
                    
                    <button id="addTaskBtn" class="mt-2 w-full p-2 bg-indigo-500 text-white font-semibold rounded-md shadow-md hover:bg-indigo-600 transition duration-150">
                        Add to Task List
                    </button>

                    <div id="taskList" class="mt-4 flex-grow overflow-y-auto custom-scrollbar p-2 bg-indigo-50 rounded-md space-y-2">
                        <!-- Tasks from Brain Dump will appear here -->
                        <p class="text-sm text-gray-500 text-center italic">Your dumped tasks will appear below.</p>
                    </div>
                    <p class="text-xs text-gray-500 mt-2">All tasks (done/undone) will be sent to your Google Sheet.</p>
                </section>
            </div>

            <!-- Column 2 & 3: Time Schedule (The Visual T-Graph) -->
            <section class="col-span-1 lg:col-span-2 p-6">
                <h2 class="text-xl font-bold text-indigo-700 mb-3 border-b pb-1">Daily Time Block Schedule</h2>
                <button id="generateScheduleBtn" class="w-full p-2 mb-4 bg-purple-500 text-white font-semibold rounded-md shadow-md hover:bg-purple-600 transition duration-150">
                    <span id="genBtnText">Ask Gemini to Create My Schedule</span>
                </button>
                
                <div id="scheduleGrid" class="grid grid-cols-2 text-sm max-h-[600px] overflow-y-auto custom-scrollbar">
                    <!-- Schedule Rows will be injected here by JavaScript -->
                    <!-- Timeslots for 5:00 AM to 11:30 PM -->
                </div>
                <div id="loadingIndicator" class="hidden text-center mt-4 p-4 bg-yellow-100 text-yellow-800 rounded-md">
                    Generating schedule with Gemini...
                </div>
            </section>

        </main>
    </div>

    <!-- Modals for Alerts/Feedback (Replaces alert()) -->
    <div id="feedbackModal" class="hidden fixed inset-0 bg-gray-600 bg-opacity-50 flex items-center justify-center p-4 z-50">
        <div class="bg-white p-6 rounded-lg shadow-xl max-w-sm w-full">
            <h3 id="modalTitle" class="text-xl font-bold mb-3 text-indigo-700"></h3>
            <p id="modalMessage" class="text-gray-700 mb-4"></p>
            <button onclick="closeModal()" class="w-full p-2 bg-indigo-500 text-white font-semibold rounded-md hover:bg-indigo-600 transition duration-150">Close</button>
        </div>
    </div>


    <script type="module">
        // --- CONSTANTS AND STATE ---
        const MODEL_NAME = "gemini-2.5-flash-preview-09-2025";
        const API_BASE = "https://generativelanguage.googleapis.com/v1beta/models/";
        const apiKey = ""; // Canvas will provide this

        let tasks = []; // Array to hold brain dump tasks
        
        // --- DOM Elements ---
        const brainDumpInput = document.getElementById('brainDumpInput');
        const addTaskBtn = document.getElementById('addTaskBtn');
        const taskList = document.getElementById('taskList');
        const scheduleGrid = document.getElementById('scheduleGrid');
        const generateScheduleBtn = document.getElementById('generateScheduleBtn');
        const genBtnText = document.getElementById('genBtnText');
        const loadingIndicator = document.getElementById('loadingIndicator');
        const processDayBtn = document.getElementById('processDayBtn');

        // --- Utility Functions (Simulated External API calls) ---

        /**
         * Simulates sending tasks to a Google Sheet (The Log/History).
         * In a real app, this would use the Google Sheets API.
         */
        async function updateGoogleSheet(dailyTasks) {
            console.log('--- Mock Google Sheets Update ---');
            console.log('Sending all tasks (Done/Undone) to the sheet for logging:');
            dailyTasks.forEach(task => {
                console.log(`[${task.done ? 'DONE' : 'TODO'}] ${task.text} - Logged at ${new Date().toISOString()}`);
            });
            console.log('----------------------------------');
            return true;
        }

        /**
         * Simulates sending Top 3 Priorities to Google Calendar.
         * In a real app, this would use the Google Calendar API.
         */
        async function updateGoogleCalendar(priorities) {
            console.log('--- Mock Google Calendar Update ---');
            console.log('Creating 3 all-day events in the calendar:');
            priorities.forEach((p, index) => {
                if (p.trim()) {
                    console.log(`[P${index + 1}] Event Created: ${p}`);
                }
            });
            console.log('----------------------------------');
            return true;
        }

        /**
         * Custom Modal instead of alert()
         */
        function showModal(title, message) {
            document.getElementById('modalTitle').textContent = title;
            document.getElementById('modalMessage').textContent = message;
            document.getElementById('feedbackModal').classList.remove('hidden');
        }

        window.closeModal = function() {
            document.getElementById('feedbackModal').classList.add('hidden');
        }

        // --- Core Application Logic ---

        /**
         * Initializes the daily schedule grid (5:00 to 23:30)
         */
        function initializeScheduleGrid() {
            scheduleGrid.innerHTML = '';
            for (let hour = 5; hour <= 23; hour++) {
                // Time label for :00
                const label00 = `${hour > 12 ? hour - 12 : hour}:00 ${hour >= 12 ? 'PM' : 'AM'}`;
                // Input field for :00
                scheduleGrid.appendChild(createScheduleCell(label00, `schedule-${hour}-00`, 'time-label'));
                scheduleGrid.appendChild(createScheduleCell('', `schedule-${hour}-00-task`, 'task-input'));

                // Time label for :30 (unless it's 23:30 which is the end)
                if (hour < 23) {
                    const label30 = `${hour > 12 ? hour - 12 : hour}:30 ${hour >= 12 ? 'PM' : 'AM'}`;
                    // Input field for :30
                    scheduleGrid.appendChild(createScheduleCell(label30, `schedule-${hour}-30`, 'time-label'));
                    scheduleGrid.appendChild(createScheduleCell('', `schedule-${hour}-30-task`, 'task-input'));
                }
            }
        }

        /**
         * Helper to create a single cell in the schedule grid.
         */
        function createScheduleCell(text, id, type) {
            const div = document.createElement('div');
            div.className = 'p-2 border border-indigo-100';

            if (type === 'time-label') {
                div.className += ' font-medium bg-indigo-50 text-indigo-700 text-right pr-4';
                div.textContent = text;
            } else if (type === 'task-input') {
                div.innerHTML = `<input type="text" id="${id}" placeholder="Block task/activity" class="w-full bg-transparent border-0 p-0 focus:ring-0 focus:outline-none text-gray-800">`;
            }
            return div;
        }

        /**
         * Adds a task from the brain dump input to the task list.
         */
        function addTaskFromInput() {
            const text = brainDumpInput.value.trim();
            if (text) {
                const newTask = { id: Date.now(), text: text, done: false };
                tasks.push(newTask);
                renderTaskList();
                brainDumpInput.value = ''; // Clear input
            }
        }

        /**
         * Renders the list of tasks from the 'tasks' array.
         */
        function renderTaskList() {
            taskList.innerHTML = '';
            if (tasks.length === 0) {
                taskList.innerHTML = '<p class="text-sm text-gray-500 text-center italic">Your dumped tasks will appear below.</p>';
                return;
            }

            tasks.forEach(task => {
                const item = document.createElement('div');
                item.className = 'flex items-center justify-between p-1 hover:bg-indigo-100 rounded cursor-pointer transition';
                item.dataset.id = task.id;

                const textSpan = document.createElement('span');
                textSpan.textContent = task.text;
                textSpan.className = `flex-grow mr-4 ${task.done ? 'task-done' : 'text-gray-800'}`;
                
                // Add click listener to toggle task completion
                textSpan.addEventListener('click', () => {
                    toggleTaskDone(task.id);
                });

                const doneMarker = document.createElement('span');
                doneMarker.className = `w-4 h-4 rounded-full border-2 flex items-center justify-center transition ${task.done ? 'bg-green-500 border-green-500' : 'border-gray-300'}`;
                if (task.done) {
                    doneMarker.innerHTML = '<svg xmlns="http://www.w3.org/2000/svg" class="h-3 w-3 text-white" viewBox="0 0 20 20" fill="currentColor"><path fill-rule="evenodd" d="M16.707 5.293a1 1 0 010 1.414l-8 8a1 1 0 01-1.414 0l-4-4a1 1 0 011.414-1.414L8 12.586l7.293-7.293a1 1 0 011.414 0z" clip-rule="evenodd" /></svg>';
                }

                item.appendChild(textSpan);
                item.appendChild(doneMarker);
                taskList.appendChild(item);
            });
        }

        /**
         * Toggles the 'done' status of a task.
         */
        function toggleTaskDone(id) {
            const task = tasks.find(t => t.id === id);
            if (task) {
                task.done = !task.done;
                renderTaskList();
            }
        }

        /**
         * Fetches a structured JSON schedule from the Gemini API based on the priorities and dump.
         * Uses exponential backoff for retries.
         */
        async function getScheduleFromGemini(prompt, retries = 3) {
            const apiUrl = `${API_BASE}${MODEL_NAME}:generateContent?key=${apiKey}`;
            const payload = {
                contents: [{ parts: [{ text: prompt }] }],
                systemInstruction: {
                    parts: [{ text: "You are an expert time management assistant (TimmyTime Box). Based on the user's priorities and brain dump, create a daily schedule. Respond only with a JSON array that maps to the half-hour slots from 5:00 to 23:30. Do NOT include any text outside the JSON block." }]
                },
                generationConfig: {
                    responseMimeType: "application/json",
                    responseSchema: {
                        type: "ARRAY",
                        items: {
                            type: "OBJECT",
                            properties: {
                                "timeSlot": { "type": "STRING", description: "Format H:MM, e.g., 5:00, 5:30, 10:00" },
                                "task": { "type": "STRING", description: "The specific task or activity planned for this slot, e.g., 'Work on P1: Report Draft', 'Lunch & Check Email', 'Deep Work on P2'" }
                            },
                            required: ["timeSlot", "task"]
                        }
                    }
                }
            };

            for (let i = 0; i < retries; i++) {
                try {
                    const response = await fetch(apiUrl, {
                        method: 'POST',
                        headers: { 'Content-Type': 'application/json' },
                        body: JSON.stringify(payload)
                    });

                    if (!response.ok) throw new Error(`HTTP error! status: ${response.status}`);

                    const result = await response.json();
                    const text = result.candidates?.[0]?.content?.parts?.[0]?.text;
                    
                    if (text) {
                        // The model should respond with a JSON string
                        return JSON.parse(text);
                    }
                    throw new Error("Gemini response was empty or malformed.");

                } catch (error) {
                    console.error(`Attempt ${i + 1} failed:`, error);
                    if (i === retries - 1) throw error; // If last retry, re-throw
                    // Exponential backoff
                    await new Promise(resolve => setTimeout(resolve, Math.pow(2, i) * 1000));
                }
            }
        }

        /**
         * Main function to generate the schedule using Gemini.
         */
        async function generateSchedule() {
            // 1. Gather all inputs
            const priorities = [
                document.getElementById('p1').value.trim(),
                document.getElementById('p2').value.trim(),
                document.getElementById('p3').value.trim()
            ].filter(p => p.length > 0);

            const allTasks = tasks.map(t => t.text).join(', ');
            
            if (priorities.length === 0 && allTasks.length === 0) {
                showModal("Input Required", "Please enter at least one Top Priority or some tasks in the Brain Dump before generating a schedule.");
                return;
            }

            // 2. Construct the prompt for Gemini
            const prompt = `
                Generate a full daily schedule from 5:00 AM to 11:30 PM in 30-minute blocks.
                The current date is ${new Date().toLocaleDateString()}.
                
                Key Goal: Ensure the Top 3 Priorities are strategically blocked out in the schedule first.
                
                Top 3 Priorities (Must be included):
                ${priorities.map((p, i) => `- P${i + 1}: ${p}`).join('\n')}
                
                Other Tasks/Brain Dump (Incorporate as needed):
                ${allTasks}
                
                Create a balanced schedule, including blocks for breaks, lunch, and deep work (especially for the P1-P3 tasks).`;

            // 3. Show loading and disable button
            loadingIndicator.classList.remove('hidden');
            generateScheduleBtn.disabled = true;
            genBtnText.textContent = 'Generating... (20s)';

            try {
                // 4. Call the Gemini API
                const jsonSchedule = await getScheduleFromGemini(prompt);
                
                // 5. Populate the schedule grid
                jsonSchedule.forEach(item => {
                    // Try to find the corresponding input ID, e.g., 9:30 -> schedule-9-30-task
                    const [timePart, minutePart] = item.timeSlot.split(':');
                    const hour = parseInt(timePart, 10);
                    const minute = parseInt(minutePart, 10);

                    const inputId = `schedule-${hour}-${minute}-task`;
                    const inputElement = document.getElementById(inputId);

                    if (inputElement) {
                        inputElement.value = item.task;
                    }
                });

                showModal("Schedule Generated!", "Gemini has created a time-blocked schedule based on your priorities. Review and adjust as needed!");

            } catch (error) {
                console.error("Gemini schedule generation failed:", error);
                showModal("Generation Failed", "Could not generate the schedule. The model response was invalid or the API call failed. Please check your network and try again.");
            } finally {
                // 6. Hide loading and re-enable button
                loadingIndicator.classList.add('hidden');
                generateScheduleBtn.disabled = false;
                genBtnText.textContent = 'Ask Gemini to Create My Schedule';
            }
        }

        /**
         * Finalizes the day: logs to sheet and schedules priorities to calendar.
         */
        async function processDay() {
            // 1. Get the Top Priorities for the calendar
            const priorities = [
                document.getElementById('p1').value.trim(),
                document.getElementById('p2').value.trim(),
                document.getElementById('p3').value.trim()
            ].filter(p => p.length > 0);

            // 2. Get all tasks (done/undone) for the sheet
            const allDailyTasks = [...tasks]; // Copy the brain dump tasks

            // Add scheduled items to the list of tasks for the sheet log
            document.querySelectorAll('#scheduleGrid input[type="text"]').forEach(input => {
                const taskText = input.value.trim();
                if (taskText) {
                    // Check if it's already in the brain dump list (e.g., if P1 was also in dump)
                    // For simplicity, we just log all scheduled items too.
                    allDailyTasks.push({ id: input.id, text: `[SCHEDULED] ${taskText}`, done: false }); 
                }
            });

            // 3. Show confirmation/loading modal
            showModal("Processing Day...", "Sending your data to Google services (simulated)...");

            // 4. Perform simulated integrations
            try {
                const sheetSuccess = await updateGoogleSheet(allDailyTasks);
                const calendarSuccess = await updateGoogleCalendar(priorities);
                
                if (sheetSuccess && calendarSuccess) {
                    showModal("Day Logged and Scheduled!", 
                        "Your daily log (all tasks) has been saved to your Google Sheet, and your Top 3 Priorities have been added to your Google Calendar. You have won the day! (A daily email of your tasks would be triggered by a backend service here).");
                } else {
                    throw new Error("Simulated API failure.");
                }

            } catch (error) {
                showModal("Processing Error", `A simulated error occurred during integration: ${error.message}`);
            }
        }


        // --- EVENT LISTENERS AND INITIALIZATION ---
        
        // Add task when button is clicked or Enter is pressed in the dump area
        addTaskBtn.addEventListener('click', addTaskFromInput);
        brainDumpInput.addEventListener('keypress', (e) => {
            if (e.key === 'Enter' && !e.shiftKey) {
                e.preventDefault();
                addTaskFromInput();
            }
        });

        // Generate schedule with Gemini
        generateScheduleBtn.addEventListener('click', generateSchedule);

        // Process the day (simulated integrations)
        processDayBtn.addEventListener('click', processDay);
        
        // Start the application
        initializeScheduleGrid();
        renderTaskList(); // Render initial empty state

        // Enhancement Suggestion: Data Persistence (using Firestore)
        // In a real application, you would use Firestore to save and load the 'tasks' array, 
        // the 'priorities', and the 'schedule' inputs when the user closes and re-opens the app.
        // This is necessary because localStorage is not persistent across users/devices.
        /*
        import { initializeApp } from "https://www.gstatic.com/firebasejs/11.6.1/firebase-app.js";
        import { getAuth, signInAnonymously, signInWithCustomToken } from "https://www.gstatic.com/firebasejs/11.6.1/firebase-auth.js";
        import { getFirestore, doc, setDoc, getDoc } from "https://www.gstatic.com/firebasejs/11.6.1/firebase-firestore.js";

        const appId = typeof __app_id !== 'undefined' ? __app_id : 'default-app-id';
        const firebaseConfig = JSON.parse(__firebase_config);
        const app = initializeApp(firebaseConfig);
        const db = getFirestore(app);
        const auth = getAuth(app);

        async function loadAndAuthenticate() {
            try {
                if (typeof __initial_auth_token !== 'undefined') { 
                    await signInWithCustomToken(auth, __initial_auth_token); 
                } else { 
                    await signInAnonymously(auth); 
                }
                const userId = auth.currentUser?.uid || 'anonymous';
                // You would now load data from: 
                // const userDocRef = doc(db, 'artifacts', appId, 'users', userId, 'data', 'timmy_time_data');
                // and then call renderTaskList() and populate the schedule/priorities
            } catch (error) {
                console.error("Firebase auth failed:", error);
            }
        }
        loadAndAuthenticate();
        */
    </script>
</body>
</html>

