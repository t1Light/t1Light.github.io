<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <title>Aarya's Family - Monthly Budget Calculator</title>
  <style>
    body {
      font-family: Arial, sans-serif;
      margin: 40px;
      background-color: #f8f8f8;
    }

    .container {
      max-width: 700px;
      margin: auto;
      padding: 20px;
      background: white;
      border-radius: 10px;
      box-shadow: 0 0 10px rgba(0,0,0,0.1);
    }

    h1, h2 {
      text-align: center;
      color: #333;
    }

    label, select, input, button {
      display: block;
      width: 100%;
      margin: 10px 0;
      padding: 10px;
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
  </style>
</head>
<body>
  <div class="container">
    <h1>Aarya's Family</h1>
    <h2>Monthly Budget Tracker</h2>

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
          <th>Day</th>
          <th>Expenses (₹)</th>
        </tr>
      </thead>
      <tbody id="expenseTableBody"></tbody>
    </table>
  </div>

  <script>
    let data = {};

    // Load saved data from localStorage
    function loadData() {
      const savedData = localStorage.getItem("budgetData");
      if (savedData) {
        data = JSON.parse(savedData);
      }
    }

    function saveData() {
      localStorage.setItem("budgetData", JSON.stringify(data));
    }

    function addExpense() {
      const month = document.getElementById('monthSelect').value;
      const day = document.getElementById('day').value;
      const amount = parseFloat(document.getElementById('amount').value);

      if (!day || isNaN(amount) || amount <= 0) {
        alert("Please enter a valid day and amount.");
        return;
      }

      if (!data[month]) data[month] = {};
      if (!data[month][day]) data[month][day] = 0;

      data[month][day] += amount;

      // Clear inputs
      document.getElementById('amount').value = '';
      document.getElementById('day').value = '';

      saveData();
      updateTable();
    }

    function updateTable() {
      const month = document.getElementById('monthSelect').value;
      const tbody = document.getElementById('expenseTableBody');
      tbody.innerHTML = '';

      let total = 0;
      if (data[month]) {
        const days = Object.keys(data[month]).sort((a,b) => a - b);
        days.forEach(day => {
          const amount = data[month][day];
          total += amount;
          const row = `<tr><td>${day}</td><td>₹${amount.toFixed(2)}</td></tr>`;
          tbody.innerHTML += row;
        });
      }

      document.getElementById('monthlyTotal').textContent = total.toFixed(2);
    }

    document.getElementById('monthSelect').addEventListener('change', updateTable);

    // Initialize on page load
    loadData();
    updateTable();
  </script>
</body>
</html>
