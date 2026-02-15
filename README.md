# 🏦 NOVA – ESP32 RFID-Based Cashless Banking System

## 📌 Project Overview

**NOVA** is an ESP32-based RFID-enabled cashless banking system designed to facilitate **smooth, transparent, and real-time transactions during large-scale events and competitions**.
The system replaces manual cash handling with RFID authentication, persistent balance storage, and cloud-based transaction logging, enabling faster money flow and reduced human error throughout the event.

This project was developed to support **~60 participants** in a competition environment, ensuring efficient balance updates and transaction traceability.

---

## 🎯 Objectives

* Enable **cashless transactions** using RFID cards
* Maintain **secure UID-based authentication**
* Persist user balances locally using **EEPROM**
* Log all transactions **in real time** to Google Sheets via REST API
* Display transaction details instantly on an LCD for transparency

---

## 🧠 System Architecture

```
[Banker Input]
      ↓
[ESP32 Controller]
      ↓
[RFID UID Verification]
      ↓
[EEPROM Balance Update]
      ↓
[LCD Display: Old → New Balance]
      ↓
[Google Sheets Logging via REST API]
```

---

## 🔧 Hardware Components

* ESP32 (Wi-Fi enabled microcontroller)
* RFID Reader (MFRC522)
* RFID Cards / Tags
* 16×2 LCD Display
* Push buttons / Serial input (Banker amount entry)
* Power supply

---

## 💻 Software & Technologies

* **ESP32 (Arduino Framework)**
* **EEPROM** for local balance storage
* **HTTP / REST API** for cloud communication
* **Google Sheets API** for transaction logging
* **Embedded C/C++**

---

## 🔐 Transaction Flow

1. Banker enters the transaction amount
2. LCD prompts the user to scan their RFID card
3. RFID UID is verified against stored records
4. User balance is fetched from EEPROM
5. Balance is updated (credited/debited)
6. LCD displays **old balance → new balance**
7. Transaction is logged to Google Sheets via REST API

---

## 📡 Cloud Integration

* Transactions are logged using **HTTP POST requests**
* Each entry includes:

  * RFID UID
  * Transaction amount
  * Previous balance
  * Updated balance
  * Timestamp
* Enables **remote monitoring and auditability**

---

## ⚙️ Key Features

* UID-based secure access
* Persistent storage (power-loss safe)
* Real-time transaction visibility
* Scalable user handling (designed for 60 participants)
* Competition-friendly, low-latency operation

---

## 🚀 Applications

* College technical festivals
* Hackathons and competitions
* Closed-campus cashless systems
* Temporary digital wallets for events

---

## 📈 Project Impact

NOVA significantly reduced transaction delays and manual accounting overhead during the event.
It ensured **faster transactions**, **accurate balance tracking**, and **transparent money flow**, improving participant experience and operational efficiency.

---

## ⚠️ Notes

* This repository focuses on **system design, architecture, and integration logic**
* Code files demonstrate **implementation logic and workflow understanding**
* The project emphasizes **embedded systems integration rather than UI design**

---

## 👤 Author

**Anagha NG**
Embedded Systems & IoT Enthusiast

---

If you want, next I can:

* Rewrite this in a **more research / academic tone**
* Simplify it for **non-technical reviewers**
* Add a **“Future Improvements”** section (very good for admissions 👀)
* Align it **exactly with your Europass CV project line**

Just tell me 😄
