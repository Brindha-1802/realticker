System Architecture
React Frontend  →  FastAPI Backend  →  Stock Data (API or Mock)
                                  →  HuggingFace LLM API

2. BACKEND (FastAPI)
pip install fastapi uvicorn requests python-dotenv

backend/
 ├── main.py
 ├── services/
 │     ├── stock_service.py
 │     └── ai_service.py
 ├── data/mock_stocks.json
 └── .env

env

HF_API_KEY=your_huggingface_api_key
data/mock_stocks.json (sample)

[
  {
    "ticker": "AAPL",
    "company": "Apple Inc",
    "price": 185.4,
    "change_percent": 1.2,
    "volume": 78000000,
    "history": [170,172,168,175,178,180,182,185]
  },
  {
    "ticker": "MSFT",
    "company": "Microsoft",
    "price": 412.3,
    "change_percent": 0.8,
    "volume": 45000000,
    "history": [380,385,390,395,400,405,408,412]
  }
]

services/stock_service.py

import json

def load_stocks():
    with open("data/mock_stocks.json") as f:
        return json.load(f)

def get_top10():
    stocks = load_stocks()
    return sorted(stocks, key=lambda x: x["volume"], reverse=True)[:10]

def get_history(ticker):
    stocks = load_stocks()
    for s in stocks:
        if s["ticker"] == ticker:
            return s["history"]
    return []

services/ai_service.py

import os
import requests
from dotenv import load_dotenv

load_dotenv()

HF_API_KEY = os.getenv("HF_API_KEY")
API_URL = "https://api-inference.huggingface.co/models/google/flan-t5-large"

headers = {"Authorization": f"Bearer {HF_API_KEY}"}

def analyze_stock(history):
    prompt = f"""
    Analyze the following 6 months stock price data: {history}.
    Identify:
    - Trend (Upward / Downward / Sideways)
    - Risk Level (Low / Medium / High)
    - Suggested Action (Long-term investment / Short-term watch / Avoid)
    Explain in simple words for a beginner.
    """

    response = requests.post(API_URL, headers=headers, json={"inputs": prompt})
    return response.json()[0]["generated_text"]

main.py

from fastapi import FastAPI
from services.stock_service import get_top10, get_history
from services.ai_service import analyze_stock

app = FastAPI()

@app.get("/api/stocks/top10")
def top_stocks():
    return get_top10()

@app.get("/api/stocks/{ticker}/history")
def history(ticker: str):
    return get_history(ticker)

@app.post("/api/stocks/{ticker}/analyze")
def analyze(ticker: str):
    history = get_history(ticker)
    result = analyze_stock(history)
    return {
        "analysis": result,
        "disclaimer": "This is AI-generated analysis and not financial advice."
    }
Run:
uvicorn main:app --reload
3. FRONTEND (React)
npx create-react-app realticker-frontend
cd realticker-frontend
npm install axios @mui/material

import React, { useEffect, useState } from "react";
import axios from "axios";
import { Table, TableHead, TableRow, TableCell, TableBody, Button } from "@mui/material";

function App() {
  const [stocks, setStocks] = useState([]);
  const [analysis, setAnalysis] = useState("");

  useEffect(() => {
    axios.get("http://localhost:8000/api/stocks/top10")
      .then(res => setStocks(res.data));
  }, []);

  const analyzeStock = (ticker) => {
    axios.post(http://localhost:8000/api/stocks/${ticker}/analyze)
      .then(res => setAnalysis(res.data.analysis));
  };

  return (
    <div style={{ padding: 20 }}>
      <h2>RealTicker – Top 10 Stocks</h2>
      <Table>
        <TableHead>
          <TableRow>
            <TableCell>Ticker</TableCell>
            <TableCell>Company</TableCell>
            <TableCell>Price</TableCell>
            <TableCell>Change %</TableCell>
            <TableCell>Volume</TableCell>
            <TableCell>AI Insight</TableCell>
          </TableRow>
        </TableHead>
        <TableBody>
          {stocks.map(stock => (
            <TableRow key={stock.ticker}>
              <TableCell>{stock.ticker}</TableCell>
              <TableCell>{stock.c…
