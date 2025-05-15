<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <title>Aarya's Family Budget</title>
  <style>
    body {
      font-family: Arial, sans-serif;
      margin: 0;
      padding: 40px;
      background-image: url('https://images.unsplash.com/photo-1600585154340-be6161a56a0c');
      background-size: cover;
      background-repeat: no-repeat;
      background-attachment: fixed;
      background-position: center;
    }

    .container {
      max-width: 800px;
      margin: auto;
      padding: 20px;
      background: rgba(255, 255, 255, 0.95);
      border-radius: 10px;
      box-shadow: 0 0 15px rgba(0,0,0,0.2);
    }

    h1, h2 {
      text-align: center;
      color: #333;
    }

    label, input, select, button {
      display: block;
      width: 100%;
      margin: 10px 0;
      padding: 10px;
      font-size: 16px;
    }

    table {
      width: 100%;
      border-collapse: collapse;
      margin-top: 20px;
    }

    th, td {
      border: 1px solid #ccc;
      padding: 10px;
      text-align: center;
      background-color: #fff;
    }

    th {
      background-color: #f0f0f0;
    }

    .total {
      margin-top: 15px;
      font-size: 18px;
      text-align: right;
      color: #444;
    }

    button {
      background-color: #4CAF50;
      color: white;
      border: none;
      cursor: pointer;
    }

    button:hover {
      background-color: #45a049;
    }

    .hidden {
      display: none;
    }

    #logoutBtn {
      float: right;
      background-color: #e74c3c;
    }
  </style>
</head>
<body>
  <div class="container">
    <!-- Login/Register Section -->
    <div id="loginSection">
      <h1>Aarya's Family Budget</h1>
      <h3>Login or Register</h3>

      <label for="loginUsername">Username:</label>
      <input type="text" id="loginUsername" placeholder="e.g. Rahul" />

      <label for="loginPassword">Password:</label>
      <input type="password" id="loginPassword" placeholder="Enter password" />

      <button onclick="login()">Login</button>
      <button onclick="register()">Register</button>
    </div>

    <!-- Budget Tracker Section -->
    <div id="budgetSection" class="hidden">
      <h2>Welcome, <span id="userDisplay"></span></h2>
      <button id="logoutBtn" onclick="logout()">Logout</button>

      <label for="monthSelect">Select Month:</label>
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

      <label for="day">Enter Day (1–31):</label>
      <input type="number" id="day" min="1" max="31" placeholder="e.g. 15" />

      <label for="amount">Enter Expense Amount (₹):</label>
      <input type="number" id="amount" placeholder="e.g. 200" />

      <button onclick="addExpense()">Add Expense</button>

      <div class="total">
        <strong>Total for Month:</strong> ₹<span id="monthlyTotal">0</span>
      </div>

      <table>
        <thead>
          <tr>
            <th>Month</th>
            <th>Day</th>
            <th>Expenses (₹)</th>
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
        alert("Username already exists. Try logging in.");
        return;
      }

      users[username] = { password };
      saveUsers(users);

      alert("Registration successful! You can now log in.");
    }

    function login() {
      const username = document.getElementById("loginUsername").value.trim();
      const password = document.getElementById("loginPassword").value;

      const users = getUsers();

      if (!users[username] || users[username].password !== password) {
        alert("Invalid username or password.");
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
      if (saved) {
        data = JSON.parse(saved);
      } else {
        data = {};
      }
    }

    function saveData() {
      localStorage.setItem("budget_" + currentUser, JSON.stringify(data));
    }

    function addExpense() {
      const month = document.getElementById("monthSelect").value;
      const day = document.getElementById("day").value;
      const amount = parseFloat(document.getElementById("amount").value);

      if (!day || isNaN(amount) || amount <= 0) {
        alert("Please enter a valid day and amount.");
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

    // Check session
    window.onload = function () {
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


