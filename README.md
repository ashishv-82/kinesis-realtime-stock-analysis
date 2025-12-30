# Real-Time Stock Market Data Analytics Pipeline

## Overview

This project builds a real-time stock market data analytics pipeline using AWS serverless technologies and event-driven architecture. The system ingests, processes, stores, and analyzes stock market data while minimizing operational costs.

## Architecture & Workflow

The pipeline performs the following key operations:

- **Stream real-time stock data** from sources like yfinance using Amazon Kinesis Data Streams
- **Process data and detect anomalies** with AWS Lambda
- **Store processed stock data** in Amazon DynamoDB for low-latency querying
- **Archive raw stock data** in Amazon S3 for long-term analytics and historical analysis
- **Query historical data** using Amazon Athena
- **Send real-time alerts** via AWS Lambda & Amazon SNS (Email/SMS)

## Architecture Diagram

![Stock Market Analytics Architecture](images/architecture-diagram.png)

## Implementation Steps 👩‍💻

1. Setting Up Data Streaming with Amazon Kinesis
2. Processing Data with AWS Lambda
3. Query Historical Stock Data using Amazon Athena
4. Stock Trend Alerts using SNS

## AWS Services Used 🛠

| Service | Purpose |
|---------|---------|
| **Amazon Kinesis Data Streams** | Ingests stock data in real-time |
| **AWS Lambda** | Processes stock data and detects stock trends |
| **Amazon DynamoDB** | Stores structured stock data for quick lookups |
| **Amazon S3** | Stores raw stock data for historical analysis |
| **Amazon Athena** | Queries stock data directly from S3 |
| **Amazon SNS** | Sends stock trend alerts via Email/SMS |
| **IAM Roles & Policies** | Manages permissions securely |

## Lambda Functions 🔧

This project includes three Lambda functions that form the core of the data pipeline:

### 1. `stream_stock_data.py` - Data Producer
- **Purpose**: Fetches real-time stock data from yfinance and streams it to Kinesis
- **Trigger**: Scheduled execution (runs continuously)
- **Output**: Sends stock data to Amazon Kinesis Data Stream
- **Data Enrichment**: Converts NumPy types to native Python floats for JSON compatibility

### 2. `process_stock_data.py` - Data Processor & Enricher
- **Purpose**: Processes Kinesis stream events and enriches data with metrics
- **Trigger**: Amazon Kinesis Data Stream (batch size: 2 records)
- **Operations**:
  - Decodes base64-encoded Kinesis data
  - Calculates price change and percentage change
  - Detects anomalies (flags >5% price movements)
  - Computes 4-period moving average
- **Outputs**: 
  - Raw data → Amazon S3 (for historical analysis)
  - Processed data → Amazon DynamoDB (for real-time queries)

### 3. `stock_trend_detector.py` - Trend Analyzer & Alerter
- **Purpose**: Analyzes stock trends using moving averages and sends alerts
- **Trigger**: Scheduled execution (e.g., every 5-10 minutes)
- **Algorithms**:
  - Calculates 5-period and 20-period Simple Moving Averages (SMA)
  - Detects **Golden Cross** (SMA-5 > SMA-20) → Buy Signal
  - Detects **Death Cross** (SMA-5 < SMA-20) → Sell Signal
- **Output**: Sends trend alerts via Amazon SNS (Email/SMS)

## Getting Started

### Prerequisites

- AWS Account with appropriate permissions
- Python 3.13+
- AWS CLI configured
- `boto3` and `yfinance` libraries

### Running the Pipeline

Start the stock data streaming script:
```bash
python stream_stock_data.py
```

The script continuously fetches stock data and pushes it to Kinesis, which triggers Lambda for processing and storage.

## Final Result

A fully functional near real-time stock analytics pipeline built using AWS services, featuring:

- Event-driven architecture with Amazon Kinesis for real-time data ingestion
- Lambda-based anomaly detection and stock trend evaluation
- Low-latency storage in DynamoDB for fast access to processed data
- Historical data archiving in Amazon S3 and querying via Athena
- Real-time alerts via Amazon SNS (Email/SMS) for significant stock movements
- Secure and cost-optimized design using IAM and serverless technologies

## Notes

- Modify `STOCK_SYMBOL` in `stream_stock_data.py` to track different stocks
- Adjust `DELAY_TIME` to change the data ingestion interval
- Configure IAM permissions for Lambda to access Kinesis, DynamoDB, and S3
- Lambda batch size is set to 2 records (adjust based on ingestion rate)
