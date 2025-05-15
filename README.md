<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <title>Aarya's Family Budget</title>
  <link href="https://fonts.googleapis.com/css2?family=Inter:wght@400;600&display=swap" rel="stylesheet">
  <style>
    * {
      box-sizing: border-box;
      font-family: 'Inter', sans-serif;
    }

    body {
      margin: 0;
      padding: 0;
      background: url('https://images.unsplash.com/photo-1600585154340-be6161a56a0c') no-repeat center center fixed;
      background-size: cover;
    }

    .container {
      max-width: 900px;
      margin: 50px auto;
      background: rgba(255, 255, 255, 0.95);
      border-radius: 12px;
      padding: 40px;
      box-shadow: 0 10px 25px rgba(0,0,0,0.15);
    }

    h1, h2 {
      text-align: center;
      color: #333;
    }

    input, select, button {
      width: 100%;
      padding: 12px;
      margin-top: 10px;
      margin-bottom: 20px;
      font-size: 16px;
      border: 1px solid #ccc;
      border-radius: 8px;
      transition: border 0.3s;
    }

    input:focus, select:focus {
      border-color: #4CAF50;
      outline: none;
    }

    button {
      background-color: #4CAF50;
      color: white;
      font-weight: 600;
      cursor: pointer;
      transition: background 0.3s;
    }

    button:hover {
      background-color: #3e9c44;
    }

    table {
      width: 100%;
      border-collapse: collapse;
      margin-top: 30px;
      background: white;
      border-radius: 8px;
      overflow: hidden;
    }

    th, td {
      padding: 14px;
      border-bottom: 1px solid #ddd;
      text-align: center;
    }

    th {
      background-color: #f4f4f4;
      font-weight: 600;
    }

    .total {
      text-align: right;
      font-size: 18px;
      color: #222;
      margin-top: 10px;
    }

    #logoutBtn {
      background-color: #e74c3c;
      margin-top: -10px;
    }

    .hidden {
      display: none;
    }

    @media (max-width: 600px) {
      .container {
        padding: 20px;
      }

      table, th, td {
        font-size: 14px;
      }

      h1 {
        font-size: 24px;
      }
    }
  </style>
</head>
<body>
  <div class="container">
    <!-- Login/Register Section -->
    <div id="loginSection">
      <h1>🔐 Aarya's Family Budget</h1>
      <h3 style="text-align:center;">Login or Register to Begin</h3>

      <label for="loginUsername">Username</label>
      <input type="text" id="loginUsername" placeholder="e.g. aarya123" />

      <label for="loginPassword">Password</label>
      <input type="password" id="loginPassword" placeholder="Enter password" />

      <button onclick="login()">Login</button>
      <button onclick="register()">Register</button>
    </div>

    <!-- Budget Section -->
    <div id="budgetSection" class="hidden">
      <h2>Welcome, <span id="userDisplay"></span></h2>
      <button id="logoutBtn" onclick="logout()">Logout</button>

      <label for="monthSelect">Select Month</label>
      <select id="monthSelect">
        <option value="January">January</option>
        <option value="February">February</option>
        <option value="March">March</option>
        <option value="April">April</option>
        <option value="May">May</option>
        <option value="June">June</option>
        <option value="July">July</option>
        <option value="August">August</option>
        <option value="September">September</option>
        <option value="October">October</option>
        <option value="November">November</option>
        <option value="December">December</option>
      </select>

      <label for="day">Enter Day (1–31)</label>
      <input type="number" id="day" min="1" max="31" placeholder="e.g. 5" />

      <label for="amount">Enter Expense Amount (₹)</label>
      <input type="number" id="amount" placeholder="e.g. 200" />

      <button onclick="addExpense()">Add Expense</button>

      <div class="total">
        <strong>Total This Month:</strong> ₹<span id="monthlyTotal">0</span>
      </div>

      <table>
        <thead>
          <tr>
            <th>Month</th>
            <th>Day</th>
            <th>Expense (₹)</th>
          </tr>
        </thead>
        <tbody id="expenseTableBody"></tbody>
      </table>
    </div>
  </div>

  <script>
    let currentUser = "";
    let data = {};

    function saveUsers(users) {
      localStorage.setItem("users", JSON.stringify(users));
    }

    function getUsers() {
      return JSON.parse(localStorage.getItem("users")) || {};
    }

    function register() {
      const username = document.getElementById("loginUsername").value.trim();
      const password = document.getElementById("loginPassword").value;

      if (!username || !password) {
        alert("Please enter both username and password.");
        return;
      }

      const users = getUsers();

      if (users[username]) {
        alert("Username already exists.");
        return;
      }

      users[username] = { password };
      saveUsers(users);

      alert("Registered! Please login now.");
    }

    function login() {
      const username = document.getElementById("loginUsername").value.trim();
      const password = document.getElementById("loginPassword").value;

      const users = getUsers();

      if (!users[username] || users[username].password !== password) {
        alert("Wrong username or password.");
        return;
      }

      currentUser = username;
      sessionStorage.setItem("currentUser", username);
      document.getElementById("userDisplay").innerText = username;

      showBudgetSection();
      loadData();
      updateTable();
    }

    function logout() {
      sessionStorage.removeItem("currentUser");
      location.reload();
    }

    function showBudgetSection() {
      document.getElementById("loginSection").classList.add("hidden");
      document.getElementById("budgetSection").classList.remove("hidden");
    }

    function loadData() {
      const saved = localStorage.getItem("budget_" + currentUser);
      data = saved ? JSON.parse(saved) : {};
    }

    function saveData() {
      localStorage.setItem("budget_" + currentUser, JSON.stringify(data));
    }

    function addExpense() {
      const month = document.getElementById("monthSelect").value;
      const day = document.getElementById("day").value;
      const amount = parseFloat(document.getElementById("amount").value);

      if (!day || isNaN(amount) || amount <= 0) {
        alert("Enter valid day and amount.");
        return;
      }

      if (!data[month]) data[month] = {};
      if (!data[month][day]) data[month][day] = 0;

      data[month][day] += amount;

      document.getElementById("amount").value = "";
      document.getElementById("day").value = "";

      saveData();
      updateTable();
    }

    function updateTable() {
      const month = document.getElementById("monthSelect").value;
      const tbody = document.getElementById("expenseTableBody");
      tbody.innerHTML = "";

      let total = 0;
      if (data[month]) {
        Object.keys(data[month]).sort((a, b) => a - b).forEach(day => {
          const amount = data[month][day];
          total += amount;
          tbody.innerHTML += `<tr><td>${month}</td><td>${day}</td><td>₹${amount.toFixed(2)}</td></tr>`;
        });
      }

      document.getElementById("monthlyTotal").textContent = total.toFixed(2);
    }

    // Load session
    window.onload = () => {
      const user = sessionStorage.getItem("currentUser");
      if (user) {
        currentUser = user;
        document.getElementById("userDisplay").innerText = user;
        showBudgetSection();
        loadData();
        updateTable();
      }
    };

    document.getElementById("monthSelect").addEventListener("change", updateTable);
  </script>
</body>
</html>



