# Mobile-app-final

from flask import Flask, render_template, jsonify, request, send_file
from charts.charting import generate_chart
from telegram.bot import send_alert
import os

app = Flask(__name__, template_folder="../frontend/templates", static_folder="../frontend/static")

# Simulated data
trades = [
    {"ticker": "AAPL", "action": "BUY", "price": 175.00},
    {"ticker": "TSLA", "action": "SELL", "price": 720.00}
]
pnl = 1250.75
win_rate = "67%"

@app.route("/")
def dashboard():
    return render_template("dashboard.html", trades=trades)

@app.route("/api/trades")
def get_trades():
    return jsonify(trades)

@app.route("/api/pnl")
def get_pnl():
    return jsonify({"pnl": pnl, "win_rate": win_rate, "total_trades": len(trades)})

@app.route("/api/scan", methods=["POST"])
def run_scan():
    return jsonify({"status": "Scan triggered successfully."})

@app.route("/api/simulate", methods=["POST"])
def run_simulation():
    send_alert("Simulating trades from your AI Trading Bot.")
    chart_path = generate_chart("AAPL")
    return jsonify({"status": "Simulation started. Chart created.", "chart": chart_path})

@app.route("/charts/<filename>")
def get_chart(filename):
    return send_file(f"charts/{filename}", mimetype='image/png')

if __name__ == "__main__":
    os.makedirs("charts", exist_ok=True)
    app.run(debug=True)
