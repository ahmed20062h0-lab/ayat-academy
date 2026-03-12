
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Quran Memorization Academy Platform</title>
    <style>
        :root {
            --primary: #2E8B57; /* Sea Green */
            --secondary: #F4F4F4;
            --accent: #D4AF37; /* Gold */
            --text: #333;
            --white: #ffffff;
        }
        body { font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif; margin: 0; padding: 0; background-color: var(--secondary); color: var(--text); }
        
        /* Layout */
        header { background-color: var(--primary); color: var(--white); padding: 20px; text-align: center; box-shadow: 0 2px 5px rgba(0,0,0,0.1); }
        .container { max-width: 1000px; margin: 20px auto; padding: 20px; }
        .card { background: var(--white); padding: 20px; border-radius: 8px; box-shadow: 0 2px 10px rgba(0,0,0,0.05); margin-bottom: 20px; }
        
        /* Forms */
        input, select, textarea { width: 100%; padding: 12px; margin: 8px 0; border: 1px solid #ddd; border-radius: 4px; box-sizing: border-box; }
        button { background-color: var(--primary); color: white; padding: 12px 20px; border: none; border-radius: 4px; cursor: pointer; width: 100%; font-size: 16px; transition: 0.3s; }
        button:hover { background-color: #246B43; }
        .btn-secondary { background-color: #555; }
        
        /* Sections */
        .hidden { display: none; }
        .notification-banner { background-color: #e8f5e9; color: #2e7d32; padding: 15px; border-radius: 5px; margin-bottom: 20px; border-left: 5px solid var(--primary); }
        
        /* Tables */
        table { width: 100%; border-collapse: collapse; margin-top: 10px; }
        th, td { padding: 12px; text-align: left; border-bottom: 1px solid #ddd; }
        th { background-color: var(--primary); color: white; }
        
        /* Tabs */
        .tabs { display: flex; gap: 10px; margin-bottom: 20px; }
        .tab-btn { flex: 1; background: #ddd; color: #333; }
        .tab-btn.active { background: var(--primary); color: white; }

        /* Reminder Badge */
        .reminder-badge { background-color: #ff9800; color: white; padding: 5px 10px; border-radius: 15px; font-size: 0.8em; display: inline-block; margin-bottom: 10px; }
    </style>
</head>
<body>

<header>
    <h1>Quran Memorization Academy</h1>
    <p>Platform for Students & Teachers</p>
</header>

<div class="container">
    <!-- Navigation Tabs -->
    <div class="tabs">
        <button class="tab-btn active" onclick="showSection('register')">Student Registration</button>
        <button class="tab-btn" onclick="showSection('student-dashboard')">Student Dashboard</button>
        <button class="tab-btn" onclick="showSection('admin-dashboard')">Admin Panel</button>
    </div>

    <!-- 1. Registration Section -->
    <div id="register" class="card">
        <h2>Student Registration</h2>
        <div id="welcome-msg" class="notification-banner hidden">
            <strong>Welcome!</strong> Your account has been created. We will contact you shortly.
        </div>
        <form id="regForm">
            <label>Full Name</label>
            <input type="text" id="name" required placeholder="Enter your full name">
            
            <label>Phone Number (for SMS)</label>
            <input type="tel" id="phone" required placeholder="+1234567890">
            
            <label>Subscription Package</label>
            <select id="package">
                <option value="Basic">Basic (1 Lesson/Week)</option>
                <option value="Standard">Standard (3 Lessons/Week)</option>
                <option value="Premium">Premium (Daily Lessons)</option>
            </select>
            
            <button type="submit">Register Now</button>
        </form>
    </div>

    <!-- 2. Student Dashboard -->
    <div id="student-dashboard" class="card hidden">
        <h2>My Learning Dashboard</h2>
        
        <!-- Reminder Section -->
        <div id="reminder-alert" class="notification-banner hidden">
            <strong>⏰ Reminder:</strong> Your lesson starts in 5 minutes!
        </div>
        <div id="portion-alert" class="notification-banner hidden">
            <strong>📖 Memorization Task:</strong> Prepare Surah Al-Mulk (Verses 1-10) for tomorrow.
        </div>

        <h3>Class Schedule</h3>
        <table id="schedule-table">
            <thead>
                <tr>
                    <th>Day</th>
                    <th>Time</th>
                    <th>Status</th>
                </tr>
            </thead>
            <tbody>
                <!-- Populated by JS -->
            </tbody>
        </table>

        <h3>Lesson Follow-up & Comments</h3>
        <div class="card" style="background: #f9f9f9;">
            <p><strong>Teacher's Note:</strong> <span id="teacher-note">Great recitation today. Focus on Tajweed rules in verse 3.</span></p>
            <textarea id="student-comment" placeholder="Add your comment or question here..."></textarea>
            <button onclick="addComment()">Submit Comment</button>
            <div id="comment-history" style="margin-top:10px; font-style:italic;"></div>
        </div>
    </div>

    <!-- 3. Admin Dashboard -->
    <div id="admin-dashboard" class="card hidden">
        <h2>Admin Panel</h2>
        <div class="notification-banner">
            <strong>Admin Alert:</strong> You have new registrations.
        </div>
        
        <h3>Registered Students</h3>
        <table id="admin-table">
            <thead>
                <tr>
                    <th>Name</th>
                    <th>Phone</th>
                    <th>Package</th>
                    <th>Action</th>
                </tr>
            </thead>
            <tbody>
                <!-- Populated by JS -->
            </tbody>
        </table>

        <h3>Send Manual Reminder</h3>
        <input type="text" id="admin-reminder-text" placeholder="Enter reminder message">
        <button onclick="sendAdminReminder()">Send to All Students</button>
    </div>
</div>

<script>
    // --- DATA MANAGEMENT (Simulating Database with LocalStorage) ---
    const DB_KEY = 'quranAcademyData';
    
    // Initial Data Structure
    const defaultData = {
        students: [],
        schedule: [
            { day: 'Monday', time: '18:00', status: 'Upcoming' },
            { day: 'Wednesday', time: '18:00', status: 'Upcoming' },
            { day: 'Friday', time: '10:00', status: 'Upcoming' }
        ],
        comments: []
    };

    function getData() {
        const data = localStorage.getItem(DB_KEY);
        return data ? JSON.parse(data) : defaultData;
    }

    function saveData(data) {
        localStorage.setItem(DB_KEY, JSON.stringify(data));
    }

    // --- NAVIGATION LOGIC ---
    function showSection(sectionId) {
        document.querySelectorAll('.card').forEach(el => el.classList.add('hidden'));
        document.getElementById(sectionId).classList.remove('hidden');
        
        // Update Tab Styles
        document.querySelectorAll('.tab-btn').forEach(btn => btn.classList.remove('active'));
        event.target.classList.add('active');

        if(sectionId === 'student-dashboard') loadStudentData();
        if(sectionId === 'admin-dashboard') loadAdminData();
    }

    // --- REGISTRATION LOGIC ---
    document.getElementById('regForm').addEventListener('submit', function(e) {
        e.preventDefault();
        const name = document.getElementById('name').value;
        const phone = document.getElementById('phone').value;
        const packageType = document.getElementById('package').value;

        const data = getData();
        const newStudent = { id: Date.now(), name, phone, package: packageType, registeredAt: new Date() };
        
        data.students.push(newStudent);
        saveData(data);

        // Show Welcome Message
        document.getElementById('welcome-msg').classList.remove('hidden');
        
        // Simulate SMS Notification
        alert(`SMS Sent to ${phone}: Welcome ${name}!`);
        
        // Reset Form
        document.getElementById('regForm').reset();
    });

    // --- STUDENT DASHBOARD LOGIC ---
    function loadStudentData() {
        const data = getData();
        const tbody = document.querySelector('#schedule-table tbody');
        tbody.innerHTML = '';

        data.schedule.forEach(slot => {
            const row = `<tr><td>${slot.day}</td><td>${slot.time}</td><td>${slot.status}</td></tr>`;
            tbody.innerHTML += row;
        });

        // Simulate Reminder Check (5 mins before)
        // In real app, this checks server time. Here we simulate it visually.
        setTimeout(() => {
            document.getElementById('reminder-alert').classList.remove('hidden');
        }, 2000);

        // Simulate Portion Reminder
        setTimeout(() => {
            document.getElementById('portion-alert').classList.remove('hidden');
        }, 4000);
    }

    function addComment() {
        const text = document.getElementById('student-comment').value;
        if(!text) return;
        
        const data = getData();
        data.comments.push({ student: 'Current User', text: text, date: new Date() });
        saveData(data);

        document.getElementById('comment-history').innerHTML += `<p><strong>You:</strong> ${text}</p>`;
        document.getElementById('student-comment').value = '';
        alert("Comment sent to Teacher!");
    }

    // --- ADMIN DASHBOARD LOGIC ---
    function loadAdminData() {
        const data = getData();
        const tbody = document.querySelector('#admin-table tbody');
        tbody.innerHTML = '';

        data.students.forEach(student => {
            const row = `<tr>
                <td>${student.name}</td>
                <td>${student.phone}</td>
                <td>${student.package}</td>
                <td><button class="btn-secondary" style="width:auto; padding:5px 10px;">View</button></td>
            </tr>`;
            tbody.innerHTML += row;
        });
    }

    function sendAdminReminder() {
        const text = document.getElementById('admin-reminder-text').value;
        if(!text) return;
        
        const data = getData();
        data.students.forEach(student => {
            alert(`SMS Sent to ${student.phone}: ${text}`);
        });
        alert("Reminders sent to all registered students.");
    }

    // --- INITIALIZATION ---
    // Request Notification Permission on Load
    if ("Notification" in window) {
        Notification.requestPermission();
    }
</script>

</body>
</html>