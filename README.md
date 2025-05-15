<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0"/>
  <title>Aarya's Family - Monthly Budget Calculator</title>
  <style>
    body {
      font-family: Arial, sans-serif;
      margin: 40px;
      background-color: #f4f4f4;
    }
    h1 {
      color: #333;
      text-align: center;
    }
    .container {
      max-width: 600px;
      margin: auto;
      padding: 20px;
      background: #fff;
      border-radius: 10px;
      box-shadow: 0 2px 8px rgba(0,0,0,0.1);
    }
    input, button {
      padding: 10px;
      margin: 10px 0;
      width: 100%;
    }
    .totals {
      margin-top: 20px;
      font-size: 18px;
      color: #555;
    }
    ul {
      list-style: none;
      padding-left: 0;
    }
  </style>
</head>
<body>
  <div class="container">
    <h1>Aarya's Family</h1>
    <h2>Daily Expense Tracker</h2>

    <label for="amount">Enter Expense Amount (₹):</label>
    <input type="number" id="amount" placeholder="e.g., 200" />

    <button onclick="addExpense()">Add Expense</button>

    <div class="totals">
      <p><strong>Total Today:</strong> ₹<span id="dailyTotal">0</span></p>
      <p><strong>Total This Month:</strong> ₹<span id="monthlyTotal">0</span></p>
    </div>

    <h3>Today's Expenses:</h3>
    <ul id="expenseList"></ul>
  </div>

  <script>
    let dailyExpenses = [];
    let monthlyTotal = 0;

    function addExpense() {
      const amountInput = document.getElementById("amount");
      const amount = parseFloat(amountInput.value);

      if (!isNaN(amount) && amount > 0) {
        dailyExpenses.push(amount);
        monthlyTotal += amount;
        updateTotals();
        displayExpenses();
        amountInput.value = "";
      } else {
        alert("Please enter a valid amount.");
      }
    }

    function updateTotals() {
      const dailyTotal = dailyExpenses.reduce((a, b) => a + b, 0);
      document.getElementById("dailyTotal").innerText = dailyTotal.toFixed(2);
      document.getElementById("monthlyTotal").innerText = monthlyTotal.toFixed(2);
    }

    function displayExpenses() {
      const list = document.getElementById("expenseList");
      list.innerHTML = "";
      dailyExpenses.forEach((amount, index) => {
        const item = document.createElement("li");
        item.textContent = `₹${amount.toFixed(2)}`;
        list.appendChild(item);
      });
    }

    // You can add a "Reset Day" button if you want to clear dailyExpenses for the next day.
  </script>
</body>
</html>
