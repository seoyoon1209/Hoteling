# Hoteling

## Hotel Cancellation Prediction

### Executive Summary

Hoteling developed an AI service platform that predicts whether a hotel reservation is likely to be canceled. For hotel operators, reservation cancellations are a major source of uncertainty that directly affects occupancy and revenue. As soon as a new reservation is received, the platform analyzes a wide range of historical and external data to estimate the probability of cancellation. These predictions enable hotel managers to optimize overbooking strategies based on expected cancellation rates. Ultimately, the platform helps maximize occupancy, improve operational efficiency, and increase overall profitability.

Hotel booking cancellations create uncertainty in room inventory management, customer service, room resale planning, and expected revenue. When hotel staff manage many reservations at the same time, it can be difficult to consistently determine which bookings require attention first.

If a possible cancellation is identified too late, staff have less time to confirm the reservation with the guest or prepare the room for resale. Manually reviewing every reservation also takes considerable time and can lead to inconsistent decisions among staff members.

### Solution Overview

Hoteling is an AI-powered decision-support service that predicts the cancellation probability of hotel reservations. A final model trained with Google Cloud AutoML analyzes 10 reservation features and provides a cancellation probability and predicted class for each booking.

The service organizes bookings based on their predicted cancellation probability, helping hotel staff identify reservations that may require earlier attention. Staff can review the prediction together with the reservation details before deciding whether customer confirmation or room resale preparation is appropriate.

Hoteling supports human judgment rather than replacing it. It does not automatically cancel reservations, contact guests, or change room prices.

---

## Key Features

- **Booking cancellation prediction:** Uses 10 reservation features to provide a cancellation probability and predicted class.
- **Prioritized reservation list:** Sorts bookings by cancellation probability so staff can review relevant reservations first.
- **Reservation details:** Displays the prediction together with the reservation information used by the model.
- **Input data validation:** Verifies that uploaded reservation data contains the required fields and valid data formats.
- **Prediction history:** Stores prediction results, model versions, timestamps, and staff actions.
- **CSV workflow:** Supports structured reservation data uploads and prediction-result exports.
- **Human-in-the-loop design:** Keeps final decisions and customer communication under the control of hotel staff.

---

## Model Input Features

The final AutoML model uses the following 10 features:

| Feature | Description |
| --- | --- |
| `lead_time` | Time between the booking date and arrival date |
| `meal_type` | Meal plan selected for the reservation |
| `market_segment` | Market segment associated with the booking |
| `reserved_room_type` | Type of room originally reserved |
| `deposit_type` | Deposit requirement for the reservation |
| `customer_type` | Customer category |
| `average_daily_rate` | Average daily room rate |
| `num_guest` | Number of guests |
| `country_code` | Guest country code |
| `arrival_month` | Scheduled arrival month |

---

## Model Performance

We trained and compared two Google Cloud AutoML models. The baseline model used 15 features, while the final model removed the five least important features and used 10 features.

The final model reduced the number of required inputs while slightly improving the major evaluation metrics.

| Metric | Score |
| --- | --- |
| Accuracy | 74.3% |
| PR AUC | 0.862 |
| ROC AUC | 0.851 |
| Macro F1 | 0.711 |
| Cancellation-class F1 | 0.613 |
| Cancellation-class Recall | 50% |

A cancellation-class recall of 50% means that the model identifies approximately half of the actual canceled bookings. For this reason, Hoteling uses predictions as prioritization signals for staff review rather than as automatic decisions.

---

## Technologies Used

- **AI/ML:** Google Cloud AutoML (final model served as a TensorFlow SavedModel)
- **Frontend:** React 19, Vite, React Router, Tailwind CSS, Axios
- **Backend:** Python and FastAPI (Uvicorn, asyncpg)
- **Database:** PostgreSQL
- **Data processing and analysis:** SAS, Microsoft Excel, and CSV
- **Deployment platform:** Render
  - Hosted on Render: React frontend, FastAPI backend web server, and PostgreSQL database server
- **Development tools:** IntelliJ IDEA, PyCharm, and Claude Code

---

## Project Structure

This repository combines the Hoteling frontend and backend into a single project.

```
Hoteling/
├── front/
│   └── hotel_f/              # React + Vite app
│       ├── index.html
│       ├── vite.config.js
│       ├── tailwind.config.cjs
│       ├── render.yaml       # frontend deployment config
│       ├── public/
│       └── src/              # components, pages, API clients
│
└── back/                     # FastAPI + ML API
    ├── main.py               # app, CORS, router registration
    ├── requirements.txt
    ├── runtime.txt
    ├── .env.example          # environment variable template
    ├── router/               # API routers by domain
    ├── db/                   # dbpool, schema.sql, migrations
    ├── ml/                   # features, predictor (inference logic)
    ├── ml_model/             # trained AutoML / TensorFlow model
    ├── ai/                   # insight.py (OpenAI insights)
    └── settings/Settings.py  # configuration (.env loader)
```

---

## Getting Started

### 1. Clone

```bash
git clone https://github.com/seoyoon1209/Hoteling.git
cd Hoteling
```

### 2. Backend

```bash
cd back
python -m venv .venv && source .venv/bin/activate
pip install -r requirements.txt

# configure environment variables
cp .env.example .env          # fill in the values (see table below)

uvicorn main:app --reload     # http://localhost:8000, API docs at /docs
```

### 3. Frontend

```bash
cd front/hotel_f
npm install

# point the frontend at the backend
echo "VITE_API_BASE=http://localhost:8000" > .env

npm run dev                   # http://localhost:5173
```

### Database initialization

```bash
psql -f back/db/schema.sql
psql -f back/db/migration_002_reservation_action.sql   # if needed
```

---

## Environment Variables

### Backend (`back/.env`)

Copy `back/.env.example`. The real `.env` is excluded from version control by `.gitignore`.

| Variable | Description |
| --- | --- |
| `DB_HOST` | Database host |
| `DB_PORT` | Database port |
| `DB_SERVICE_NAME` | Database (service) name |
| `DB_USER` | Database user |
| `DB_PASSWORD` | Database password |
| `OPENAI_API_KEY` | OpenAI API key (AI insight feature) |
| `OPENAI_MODEL` | Model name (default `gpt-4o`) |

### Frontend (`front/hotel_f/.env`)

| Variable | Description | Example |
| --- | --- | --- |
| `VITE_API_BASE` | Backend API base URL | `http://localhost:8000` |

---

## API Overview

All endpoints are served under the `/api` prefix (e.g. `/api/reservations`).

| Domain | Prefix |
| --- | --- |
| Hotels | `/hotels` |
| Customers | `/customers` |
| Room types | `/room-types` |
| Reservations | `/reservations`, `/reservations/{id}` |
| Predictions | `/reservations/{id}/predictions` |
| AI insight | `/reservations/{id}/ai-insight` |
| Reservation actions | `/reservations/{id}/actions` |
| Action reports | `/actions/report`, `/actions/export` |
| Overbooking | `/overbooking/summary` |
| Model info | `/model-info` |

> The full specification is available at `/docs` (Swagger UI) once the backend is running.

---

## Target Users

Hoteling is designed for:

- Hotel operations teams
- Revenue management teams
- Reservation and customer service teams
- Hotel managers responsible for room inventory and cancellation planning

These users can use Hoteling to prioritize reservation reviews more consistently and prepare earlier responses for bookings with a higher predicted cancellation probability.

---

## Current Implementation

The team cleaned and prepared the hotel reservation dataset, defined the model input schema, and trained and evaluated the cancellation prediction models with Google Cloud AutoML. We removed the five least important inputs and selected a final model that uses 10 reservation features.

We developed a React frontend, a FastAPI backend, and a PostgreSQL database and deployed all three components on Render. The final AutoML model is integrated with the FastAPI backend, enabling the complete workflow from reservation-data input to cancellation-prediction results.

The following features are implemented:

- Reservation-data input and validation
- AutoML model inference
- Cancellation probability and predicted-class output
- Reservation sorting by cancellation probability
- Reservation detail view
- Prediction and staff-action history
- CSV result export
- Integration among the React frontend, FastAPI backend, AutoML model, and PostgreSQL database

---

## Project Links and Repositories

- **Live Demo:** https://smsf-0pzo.onrender.com/
- **Demo Video:** https://youtu.be/gfzcn6mdDqg
- **This repository (combined frontend + backend):** https://github.com/seoyoon1209/Hoteling
- **Original Frontend repository:** https://github.com/seoyoon1209/HotelF
- **Original Backend repository:** https://github.com/seoyoon1209/HotelB

> This repository combines the Hoteling **frontend (React)** and **backend (FastAPI)** into a single project and serves as the **main documentation for the overall project**. The original separate repositories (HotelF, HotelB) remain available for reference.

---

## Project Files

The following materials are included in the Devpost submission:

- **Main Service Screen** — A representative screen showing Hoteling's main functionality and interface
- **Reservation Prediction Screen** — A screen showing the reservation details, cancellation probability, and model inputs
- **AI Model Evaluation Results** — A table or chart showing the final model's Accuracy, PR AUC, ROC AUC, F1, and Recall
- **System Architecture Diagram** — A diagram showing the React frontend, FastAPI backend, AutoML model, PostgreSQL database, and Render deployment
- **Demo Video** — A video explaining the problem, service workflow, AI model, prediction results, and user value

---

## Team Details

**Jungeun Lee — Project Manager and Team Lead**
- Managed the project and coordinated the development schedule
- Contributed to AutoML training and evaluation
- Contributed to service planning and PRD writing
- Produced the demo video

**Minae Kim — AI Model Training and Evaluation**
- Trained and evaluated the Google Cloud AutoML models
- Compared model performance and helped select the final model
- Contributed to service planning and PRD writing
- Prepared presentation materials and presented the project
- Produced the demo video

**Yunsong Kim — Data Preprocessing**
- Organized and preprocessed the hotel reservation dataset
- Produced the preprocessed CSV files
- Used SAS for data preparation

**Yoonah Huh — Data Preparation and Exploratory Data Analysis**
- Organized and cleaned the hotel reservation dataset
- Conducted exploratory data analysis
- Produced processed CSV files and EDA results
- Contributed to UI/UX design

**Seoyoon Kim — Full-Stack Service Developer**
- Developed the React frontend
- Developed the FastAPI backend
- Integrated the AutoML model with the backend
- Integrated the PostgreSQL database
- Deployed the frontend, backend, and database on Render
- Contributed to UI/UX design
