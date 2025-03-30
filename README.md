<!DOCTYPE html>
<html>
<head>
    <title>To-Do List</title>
    <style>
body {
    background-image: url("download.jpeg");
    background-size: cover;
    background-position: center;
    background-attachment: fixed;
    font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
    color: #ffffff;
}
.container {
     /*background-color: rgba(255, 255, 255, 0.85); !* semi-transparent white *!*/
    padding: 20px;
    border-radius: 15px;
    box-shadow: 0 8px 20px rgba(0, 0, 0, 0.3);
    max-width: 800px;
    margin: auto;
}
</style>

    <link rel="stylesheet" href="{{ url_for('static', filename='style.css') }}">

</head>
<body>
<center>
<!-- LOGIN PAGE -->
<div id="login-page" class="container transparent-box">
    <h2>Login</h2>
    <input type="text" id="username" placeholder="Enter username"><br>
    <input type="password" id="password" placeholder="Enter password"><br>
    <button onclick="login()">Login</button>
    <p id="login-error" style="color: red;"></p>
</div>
    </center>

<!-- MAIN APP CONTENT - hidden at first -->
<div id="main-app" style="display: none;">
      <!-- To-Do List (Center) -->
        <div class="container transparent-box">
           <center><h1> To-Do List</h1></center>
            <form action="/add" method="POST">
                <input type="text" name="content" placeholder="Enter a new task" required>
                <button type="submit">Add</button>
            </form>
            <ul>
                {% for task in tasks %}
                <li class="task {% if task.complete %}completed{% endif %}">
                    {{ task.content }}
                    <div class="actions">
                        {% if task.complete %}
                            <a href="/complete/{{ task.id }}"> Redo</a>
                        {% else %}
                            <a href="/complete/{{ task.id }}">  Complete</a>
                        {% endif %}
                        <a href="/delete/{{ task.id }}">  Delete</a>
                    </div>
                </li>
                {% endfor %}
            </ul>
        </div>

 <!--   Clock (left Side) -->
  <center> <h3>Stopwatch</h3></center>
<div id="stopwatch" class="transparent-box">
  <div class="stopwatch-dial">
    <div class="stopwatch-hand" id="sweep-hand"></div>
    <div class="stopwatch-center"></div>
  </div>
    <center>
  <div class="stopwatch-time" id="stopwatch-time">00:00.0</div>
  <div class="stopwatch-controls">
    <button onclick="startStopwatch()">Start</button>
    <button onclick="stopStopwatch()">Stop</button>
    <button onclick="resetStopwatch()">Reset</button>
            </div>
    </center>
</div>

        <!--   Calendar (Right Side) -->
     <center> <h3>Calendar</h3></center>
<div id="calendar" class="transparent-box">
  <div class="calendar-header">
    <button onclick="prevMonth()">&#8592;</button>
    <div id="monthYear"></div>
    <button onclick="nextMonth()">&#8594;</button>
  </div>
  <div class="calendar-days">
    <div>Sun</div><div>Mon</div><div>Tue</div><div>Wed</div><div>Thu</div><div>Fri</div><div>Sat</div>
  </div>
  <div class="calendar-dates" id="calendarDates"></div>
</div>

    <script>
    let stopwatchInterval;
    let stopwatchStartTime;
    let elapsed = 0;
    const timeDisplay = document.getElementById("stopwatch-time");
    const hand = document.getElementById("sweep-hand");

    function updateStopwatch() {
        const now = new Date();
        const diff = now - stopwatchStartTime + elapsed;

        const minutes = Math.floor(diff / 60000);
        const seconds = Math.floor((diff % 60000) / 1000);
        const tenths = Math.floor((diff % 1000) / 100);

        timeDisplay.textContent =
            `${String(minutes).padStart(2, "0")}:${String(seconds).padStart(2, "0")}.${tenths}`;

        // rotate the hand clockwise (1 full circle = 60 seconds = 360deg)
        const degrees = ((diff % 60000) / 60000) * 360;
        hand.style.transform = `translateX(-50%) rotate(${degrees}deg)`;
    }

    function startStopwatch() {
        if (!stopwatchInterval) {
            stopwatchStartTime = new Date();
            stopwatchInterval = setInterval(updateStopwatch, 100);
        }
    }

    function stopStopwatch() {
        if (stopwatchInterval) {
            clearInterval(stopwatchInterval);
            stopwatchInterval = null;
            elapsed += new Date() - stopwatchStartTime;
        }
    }

    function resetStopwatch() {
        stopStopwatch();
        elapsed = 0;
        timeDisplay.textContent = "00:00.0";
        hand.style.transform = "translateX(-50%) rotate(0deg)";
    }
</script>


    <!-- JavaScript -->
    <script>
    const monthYear = document.getElementById("monthYear");
    const calendarDates = document.getElementById("calendarDates");
    const date = new Date();

    function renderCalendar() {
        const year = date.getFullYear();
        const month = date.getMonth();

        const firstDay = new Date(year, month, 1).getDay();
        const lastDate = new Date(year, month + 1, 0).getDate();
        const today = new Date();

        const monthNames = [
            "January", "February", "March", "April", "May", "June",
            "July", "August", "September", "October", "November", "December"
        ];

        monthYear.textContent = `${monthNames[month]} ${year}`;
        calendarDates.innerHTML = "";

        // Add empty cells before the first day
        for (let i = 0; i < firstDay; i++) {
            calendarDates.innerHTML += `<div></div>`;
        }

        // Add days
        for (let i = 1; i <= lastDate; i++) {
            const isToday = i === today.getDate() &&
                            month === today.getMonth() &&
                            year === today.getFullYear();
            calendarDates.innerHTML += `<div class="${isToday ? "today" : ""}">${i}</div>`;
        }
    }

    function prevMonth() {
        date.setMonth(date.getMonth() - 1);
        renderCalendar();
    }

    function nextMonth() {
        date.setMonth(date.getMonth() + 1);
        renderCalendar();
    }

    renderCalendar();
</script>
<script>
function login() {
    const username = document.getElementById("username").value;
    const password = document.getElementById("password").value;
    const error = document.getElementById("login-error");

    // You can set your desired username/password here
    if (username === "admin" && password === "1234") {
        document.getElementById("login-page").style.display = "none";
        document.getElementById("main-app").style.display = "block";
    } else {
        error.textContent = "Invalid username or password!";
    }
}
</script>

<script src="script.js"></script>
 </body>

</html>
