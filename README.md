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

- **AI/ML:** Google Cloud AutoML
- **Frontend:** React
- **Backend:** Python and FastAPI
- **Database:** PostgreSQL
- **Data processing and analysis:** SAS, Microsoft Excel, and CSV
- **Deployment platform:** Render
  - Hosted on Render: React frontend, FastAPI backend web server, and PostgreSQL database server
- **Development tools:** IntelliJ IDEA, PyCharm, and Claude Code

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

> This repository is the **frontend** of Hoteling and serves as the **main documentation for the overall project**. For backend (FastAPI) source code, see the [Backend repository](https://github.com/seoyoon1209/HotelB).

---

## Project Files

The following materials are included in the Devpost submission:

- **Main Service Screen** — A representative screen showing Hoteling's main functionality and interface
- **Reservation Prediction Screen** — A screen showing the reservation details, cancellation probability, and model inputs
- **AI Model Evaluation Results** — A table or chart showing the final model's Accuracy, PR AUC, ROC AUC, F1, and Recall
- **System Architecture Diagram** — A diagram showing the React frontend, FastAPI backend, AutoML model, PostgreSQL database, and Render deployment
- **Demo Video** — A video explaining the problem, service workflow, AI model, prediction results, and user value

---

## Acknowledgments

This AI Service Platform was developed as part of the [17th QI AI Entrepreneurship Program – Summer 2026 (full content)](https://www.kaggle.com/code/QualcommInstituteAI/17th-qi-ai-entrepreneurship-program-summer-2026) & [(summary record)](https://github.com/Qualcomm-Institute-AI/QI-AI-Programs/tree/main/2026/Summer/17th%20QI%20AI%20Entrepreneurship%20Program), hosted by the Qualcomm Institute (QI), University of California, San Diego (UC San Diego).

We would like to express our sincere gratitude to [Dr. Seokheon Cho](https://www.linkedin.com/in/justin-seokheon-cho-ph-d-91253343a/) of the Qualcomm Institute for his extensive guidance, supervision, and support throughout the development of this platform.

<!--We also acknowledge the following sources of research support:

This research was supported by the MSIT (Ministry of Science and ICT), Korea, under the National Program for Excellence in SW (2021-0-01393), supervised by the IITP (Institute of Information & Communications Technology Planning & Evaluation).-->

## Dataset, AI Model & References

### Dataset

This project uses the following dataset:

* **[Hotel Booking Demand](https://www.kaggle.com/datasets/jessemostipak/hotel-booking-demand)** * A publicly available dataset containing 119,390 booking records from a resort hotel and a city hotel in Portugal, with scheduled arrival dates from July 2015 to August 2017. It includes reservation characteristics and cancellation outcomes for research in hotel demand and revenue management. Hoteling uses the City Hotel subset, which originally contained 79,330 records. After removing invalid or implausible entries, 78,669 records were retained for modeling. [Antonio et al. (2019)]

### AI Model

This project uses the following models and AI tools for prediction, experimental evaluation, and service development:

* **Google Cloud AutoML** *— Selected for the final deployed hotel booking cancellation prediction model. The team compared a baseline model using 15 reservation features with a reduced model using 10 features. The final 10-feature model reduces the required inputs while slightly improving the reported major evaluation metrics. It provides cancellation probabilities and predicted classes to help hotel staff prioritize reservation reviews. [Google Cloud]

* **LLMs: GPT-5.6 Sol and Claude** * — Used to assist with coding and documentation, and to support the service’s AI insight functionality, including explanations and suggested marketing actions. Booking cancellation probabilities are generated by the AutoML prediction model. Hotel staff retain responsibility for reviewing suggestions and deciding on operational actions.

### References

1. Antonio, N., de Almeida, A., and Nunes, L., “Hotel Booking Demand Datasets,” *Data in Brief*, vol. 22, pp. 41–49, 2019. [[DOI](https://doi.org/10.1016/j.dib.2018.11.126)]

2. Google Cloud, “Classification and regression overview,” *Google Cloud Documentation*. [[Documentation](https://cloud.google.com/vertex-ai/docs/tabular-data/classification-regression/overview)]
