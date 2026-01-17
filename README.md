<!DOCTYPE html>
<html lang="bn">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Tractor Diary Pro</title>
    <style>
        :root { --primary: #27ae60; --dark: #2c3e50; --bg: #f0f2f5; --white: #ffffff; }
        body { font-family: 'Segoe UI', Arial, sans-serif; background-color: var(--bg); margin: 0; padding: 15px; }
        .container { max-width: 500px; margin: auto; }
        .card { background: var(--white); padding: 25px; border-radius: 15px; box-shadow: 0 4px 15px rgba(0,0,0,0.1); margin-bottom: 20px; }
        input { width: 100%; padding: 12px; margin: 10px 0; border: 1px solid #ddd; border-radius: 8px; box-sizing: border-box; }
        .btn { width: 100%; padding: 14px; border: none; border-radius: 8px; cursor: pointer; font-weight: bold; color: white; margin-top: 10px; }
        .btn-primary { background: var(--primary); }
        .btn-blue { background: #3498db; }
        .btn-reset { background: #e74c3c; font-size: 12px; margin-top: 5px; }
    </style>
</head>
<body>

<div class="container">
    <div id="authContainer" class="card">
        <h2 id="authTitle" style="text-align:center; color: var(--dark);">🚜 ট্রাক্টর ডায়েরি প্রো</h2>
        <input type="email" id="userEmail" placeholder="আপনার ইমেইল">
        <input type="password" id="userPass" placeholder="পাসওয়ার্ড">
        <div id="loginActions">
            <button class="btn btn-primary" onclick="handleLogin()">লগইন</button>
            <p style="text-align:center; font-size:14px;">অ্যাকাউন্ট নেই? <a href="#" onclick="toggleAuth(true)">নতুন তৈরি করুন</a></p>
        </div>
        <div id="signupActions" style="display:none;">
            <button class="btn btn-blue" onclick="handleSignup()">রেজিস্ট্রেশন করুন</button>
            <p style="text-align:center; font-size:14px;">ইতিমধ্যে অ্যাকাউন্ট আছে? <a href="#" onclick="toggleAuth(false)">লগইন করুন</a></p>
        </div>
    </div>

    <div id="mainApp" style="display:none;">
        <div class="card">
            <h3>নতুন এন্ট্রি যোগ করুন</h3>
            <input type="text" id="entryTitle" placeholder="কাজের নাম">
            <input type="number" id="entryAmount" placeholder="টাকার পরিমাণ">
            <button class="btn btn-primary" onclick="saveToCloud()">সেভ করুন</button>
            <button class="btn" style="background:#888; margin-top: 20px;" onclick="handleLogout()">লগআউট</button>
        </div>
    </div>
</div>

<script src="https://www.gstatic.com/firebasejs/9.17.1/firebase-app-compat.js"></script>
<script src="https://www.gstatic.com/firebasejs/9.17.1/firebase-auth-compat.js"></script>
<script src="https://www.gstatic.com/firebasejs/9.17.1/firebase-database-compat.js"></script>

<script>
    // আপনার স্ক্রিনশট থেকে সংগৃহীত চূড়ান্ত তথ্য
    const firebaseConfig = {
      apiKey: "AIzaSyCksWZLCmO1XUX6voboiHqBt3kbcNWD6mc",
      authDomain: "tractordiary-d0446.firebaseapp.com",
      projectId: "tractordiary-d0446",
      storageBucket: "tractordiary-d0446.firebasestorage.app",
      messagingSenderId: "912227405486",
      appId: "1:912227405486:web:1203ce97a1967b277ce73f",
      databaseURL: "https://tractordiary-d0446-default-rtdb.firebaseio.com"
    };

    firebase.initializeApp(firebaseConfig);
    const auth = firebase.auth();
    const db = firebase.database();

    auth.onAuthStateChanged(user => {
        if (user) {
            document.getElementById('authContainer').style.display = 'none';
            document.getElementById('mainApp').style.display = 'block';
        } else {
            document.getElementById('authContainer').style.display = 'block';
            document.getElementById('mainApp').style.display = 'none';
        }
    });

    function handleSignup() {
        const email = document.getElementById('userEmail').value;
        const pass = document.getElementById('userPass').value;
        auth.createUserWithEmailAndPassword(email, pass).then(() => alert("সফল!")).catch(err => alert(err.message));
    }

    function handleLogin() {
        const email = document.getElementById('userEmail').value;
        const pass = document.getElementById('userPass').value;
        auth.signInWithEmailAndPassword(email, pass).catch(err => alert("ভুল ইমেইল/পাসওয়ার্ড"));
    }

    function saveToCloud() {
        const title = document.getElementById('entryTitle').value;
        const amount = document.getElementById('entryAmount').value;
        const user = auth.currentUser;
        if (title && amount) {
            db.ref('users/' + user.uid + '/entries').push({ title, amount, date: new Date().toLocaleString() })
            .then(() => { alert("সেভ হয়েছে!"); document.getElementById('entryTitle').value = ""; document.getElementById('entryAmount').value = ""; });
        }
    }

    function handleLogout() { auth.signOut(); }
    function toggleAuth(showSignup) {
        document.getElementById('loginActions').style.display = showSignup ? 'none' : 'block';
        document.getElementById('signupActions').style.display = showSignup ? 'block' : 'none';
    }
</script>
</body>
</html>
