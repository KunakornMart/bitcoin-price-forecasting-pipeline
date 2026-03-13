# Real-Time Bitcoin Price Forecasting with Prophet

<p>
  <b>Time Series Forecasting</b> · <b>Prophet</b> · <b>Apache Airflow</b> · <b>Amazon EC2</b> · <b>Python</b> · <b>Binance API</b>
</p>

---

## Overview

This project builds an automated pipeline for **Bitcoin price forecasting every 5 minutes** using live market data. The workflow begins with a model selection stage, where five forecasting approaches were tested:

- Linear Regression
- Prophet
- XGBoost
- Random Forest
- LSTM

Among these, **Prophet** produced the lowest average **MAPE (0.290467437)** and was selected as the final model for the live forecasting pipeline.

The production workflow was then automated with **Apache Airflow on Amazon EC2**, using market data from **Binance** and periodic error tracking to monitor forecasting quality over time.

---

## Project Goal

The main goal was to build a system that does more than train a forecasting model once. Instead, the project was designed to:

- generate BTC forecasts every **5 minutes**
- compare forecasts against actual market prices
- calculate rolling **hourly MAPE**
- track how forecasting performance changes during periods of different market behavior

---

## Model Selection

Before deploying the live pipeline, five models were tested on Bitcoin price data from **20 December 2024 (14:00–17:00)**.

### Average MAPE by Model

| Model | Average MAPE |
|------|--------------:|
| Prophet | **0.290467437** |
| XGBoost | 0.310918966 |
| Linear Regression | 0.344504473 |
| Random Forest | 0.425594191 |
| LSTM | 477,512,879.55 |

Based on this comparison, **Prophet** was selected as the forecasting model for the live system.

---

## Why Prophet

Prophet was chosen because it handled this short BTC forecasting setup better than the other tested models. It was also a practical fit for the project because it:

- works naturally with time-series data
- handles trend and seasonality well
- is relatively robust to missing data
- is easy to train and reuse in an automated workflow

For a short-interval forecasting task with highly volatile crypto prices, that combination made it the most usable choice in this experiment.

---

## Pipeline

```text
Binance / MongoDB JSON
        ↓
Data transformation to Prophet format (ds, y)
        ↓
Model training / loading
        ↓
5-minute BTC forecast generation
        ↓
Comparison with actual BTC prices
        ↓
Hourly MAPE tracking
        ↓
Apache Airflow orchestration on Amazon EC2
```

---

## How the System Works

The forecasting pipeline follows these steps:

1. **Data ingestion**
   - BTC price data is pulled from a MongoDB JSON source tied to Binance market data

2. **Transformation**
   - The raw data is converted into Prophet’s expected format:
     - `ds` = timestamp converted to datetime
     - `y` = BTC price (`lastPrice`)

3. **Model training**
   - Prophet is fitted using the transformed dataframe
   - The trained model is saved as `model_prophet.pkl`

4. **Forecast generation**
   - Airflow runs the forecasting workflow every **5 minutes**
   - The saved Prophet model is loaded and used to generate the next prediction

5. **Evaluation**
   - Forecasted values are compared with actual BTC prices
   - **MAPE** is calculated continuously
   - Every 12 runs (1 hour), the average MAPE is logged for monitoring

---

## Deployment

The full workflow was automated with **Apache Airflow** and deployed on **Amazon EC2**.

This setup handled:
- task scheduling
- model execution
- forecast generation
- result logging
- repeatable hourly evaluation

The goal was to make the process run continuously with minimal manual intervention.

---

## Key Features

- End-to-end **5-minute Bitcoin forecasting pipeline**
- Benchmarking across **5 forecasting models**
- Final deployment using **Prophet**
- Automated orchestration with **Apache Airflow**
- Cloud execution on **Amazon EC2**
- Hourly **MAPE tracking** for live accuracy monitoring

---

## What the Results Showed

A few patterns stood out during the live runs:

- **Prophet performed best overall** among the five tested models in the initial benchmark
- Forecast accuracy was generally better during more stable periods
- MAPE increased when the market moved sharply in a short time
- The pipeline was useful not just for generating forecasts, but for tracking how model quality changed under different market conditions

---

## Setup

### Prerequisites

- Python
- Apache Airflow
- Amazon EC2 (or a similar Linux-based cloud VM)
- Access to market data / stored BTC data
- Required Python libraries for forecasting and scheduling

### Example project flow

```bash
git clone https://github.com/KunakornMart/bitcoin-price-forecasting-pipeline.git
cd bitcoin-price-forecasting-pipeline
```

Train or load the Prophet model, then run the Airflow DAGs for scheduled forecasting and evaluation.

---

## Repository Structure

```text
bitcoin-price-forecasting-pipeline/
├── dags/
├── models/
│   └── model_prophet.pkl
├── data/
├── notebooks/
├── scripts/
├── README.md
└── requirements.txt
```

> Update the structure above to match your actual repository layout if needed.

---

## Dashboard / Output Artifacts

This project focuses more on the forecasting pipeline and logged outputs than on a full visualization dashboard. The main outputs include:

- generated BTC price forecasts
- hourly MAPE summaries
- model comparison results
- saved Prophet model artifact

If needed, this pipeline could be extended later with a dashboard layer such as Streamlit, Grafana, or Superset.

---

## What I’d Improve Next

- **Longer validation window** — the experiment covered only a short time span
- **More market features** — order book signals, volatility features, or external macro data could improve forecasting quality
- **Hybrid forecasting models** — combining Prophet with other models may help during sudden price swings
- **Visualization layer** — adding a dashboard would make it easier to monitor forecasts and error trends in real time
- **More robust evaluation** — expanding beyond MAPE and adding additional error metrics would provide a fuller picture

---

## Skills Demonstrated

- Time series forecasting
- Model selection and evaluation
- MAPE-based performance tracking
- Workflow orchestration with Apache Airflow
- Cloud deployment with Amazon EC2
- Data transformation for forecasting pipelines
- Near-real-time model monitoring

---

## Suggested Repository Name

**bitcoin-price-forecasting-pipeline**

Other good options:
- `real-time-bitcoin-forecasting`
- `bitcoin-forecasting-with-prophet`
- `airflow-bitcoin-price-prediction`

---

## Author

**Kunakorn Pruksakorn**  
M.Sc. in Data Analytics and Data Science, NIDA

---

<p align="center">
  Built as part of the DADS6005 Final Project at NIDA.
</p>
