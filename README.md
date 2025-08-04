# These is the Python (Flask)Backend code

from flask import Flask, render_template, request, jsonify
import json
import os

app = Flask(__name__)

DATA_FILE = 'orders.json'

# Load orders from file
def load_orders():
    if os.path.exists(DATA_FILE):
        with open(DATA_FILE, 'r') as f:
            return json.load(f)
    return []

# Save orders to file
def save_orders(orders):
    with open(DATA_FILE, 'w') as f:
        json.dump(orders, f, indent=4)

@app.route('/')
def index():
    return render_template('index.html')

@app.route('/orders', methods=['GET', 'POST'])
def orders():
    if request.method == 'GET':
        return jsonify(load_orders())
    elif request.method == 'POST':
        data = request.get_json()
        orders = load_orders()
        data['id'] = len(orders) + 1
        orders.append(data)
        save_orders(orders)
        return jsonify({"message": "Order added successfully", "order": data}), 201

if __name__ == '__main__':
    app.run(debug=True)


# These is the Frontend HTML code for structue
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Customer Order Dashboard</title>
    <link rel="stylesheet" href="/static/style.css">
</head>
<body>
    <div class="container">
        <h1>Customer Order Dashboard</h1>
        <form id="orderForm">
            <input type="text" id="name" placeholder="Customer Name" required>
            <input type="text" id="item" placeholder="Order Item" required>
            <input type="number" id="quantity" placeholder="Quantity" required>
            <button type="submit">Submit Order</button>
        </form>
        <h2>Orders List</h2>
        <table id="ordersTable">
            <thead>
                <tr>
                    <th>ID</th>
                    <th>Customer</th>
                    <th>Item</th>
                    <th>Qty</th>
                </tr>
            </thead>
            <tbody></tbody>
        </table>
    </div>
    <script src="/static/script.js"></script>
</body>
</html>


# These is the Frontend CSS File for Styling code


body {
    font-family: Arial, sans-serif;
    background: #f4f4f4;
    margin: 0;
    padding: 20px;
}
.container {
    max-width: 600px;
    margin: auto;
    background: white;
    padding: 20px;
    border-radius: 8px;
}
input, button {
    display: block;
    width: 100%;
    margin: 10px 0;
    padding: 10px;
}
button {
    background-color: #28a745;
    color: white;
    border: none;
    cursor: pointer;
}
table {
    width: 100%;
    margin-top: 20px;
    border-collapse: collapse;
}
th, td {
    padding: 8px;
    border: 1px solid #ccc;
    text-align: center;
}


# These is the Java Script code for Fronted
document.getElementById('orderForm').addEventListener('submit', async function(e) {
    e.preventDefault();
    const name = document.getElementById('name').value;
    const item = document.getElementById('item').value;
    const quantity = document.getElementById('quantity').value;

    const response = await fetch('/orders', {
        method: 'POST',
        headers: { 'Content-Type': 'application/json' },
        body: JSON.stringify({ name, item, quantity })
    });

    if (response.ok) {
        document.getElementById('orderForm').reset();
        loadOrders();
    } else {
        alert('Failed to add order');
    }
});

async function loadOrders() {
    const res = await fetch('/orders');
    const orders = await res.json();
    const tbody = document.querySelector('#ordersTable tbody');
    tbody.innerHTML = '';
    orders.forEach(order => {
        const row = `<tr>
            <td>${order.id}</td>
            <td>${order.name}</td>
            <td>${order.item}</td>
            <td>${order.quantity}</td>
        </tr>`;
        tbody.innerHTML += row;
    });
}

loadOrders();


# these is the JSON file for Data Base
[]
