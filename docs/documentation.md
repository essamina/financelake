# Stock-Spark-DL Pipeline Documentation

## Overview

This document describes the **modules of the real-time stock prediction pipeline** used in the **Stock-Spark-DL** project. These modules are organized to create a complete pipeline: Kafka → Spark → GRU → Dashboard.

---

## `01_train_gru.py`

> **Trains a GRU (Gated Recurrent Unit) model** on synthetic stock data.

- **File**: `01_train_gru.py`
- **Type**: Batch training script

### Main Parameters

- `sequence_length`: Length of time sequences 
- `n_features`: Number of technical features 
- `epochs`: Number of training epochs 
- `batch_size`: Batch size 
### Features

1. **Synthetic data generation** with realistic patterns
2. **Technical feature calculation**:
   - Price, volume, variations
   - Moving averages (5, 10 periods)
   - Volatility, returns
3. **GRU model construction**:
   - 2 GRU layers (64 → 32 neurons)
   - Dense layers with dropout
   - Sigmoid output (binary classification)
4. **Training and saving**:
   - Model: `models/gru_model.h5`
   - Scaler: `models/scaler.pkl`

---

## `02_kafka_producer.py`

> **Generates and sends real-time stock data to Kafka.**

- **File**: `02_kafka_producer.py`
- **Type**: Real-time Kafka producer

### Main Parameters

- `bootstrap_servers`: Kafka broker addresses *(default: 'localhost:9092')*
- `topic`: Kafka topic for stock data *(default: 'stock_data')*
- `interval`: Data generation interval in seconds *(default: 2)*

### Features

1. **Real-time data generation**:
   - Synthetic ticker symbols (AAPL, GOOGL, TSLA, MSFT, AMZN)
   - Realistic price variations with trends and volatility
2. **Kafka integration**:
   - JSON format data serialization
   - Configurable publishing frequency
   - Error handling and reconnection
3. **Simulation control**:
   - Adjustable trend periods
   - Volatility simulation
   - Configurable trading hours

---

## `03_spark_gru_streaming.py`

> **Spark Streaming processing with GRU integration for real-time predictions.**

- **File**: `03_spark_gru_streaming.py`
- **Type**: Spark Structured Streaming application

### Features

1. **Stream processing pipeline**:
   - Kafka source integration
   - JSON parsing and schema validation
   - Window-based aggregations
2. **Real-time predictions**:
   - GRU model loading and inference
   - Sequence construction from streaming data
   - Prediction batching
3. **Output handling**:
   - Console output for debugging
   - File sink for predictions
   - State management for sequences

---

## `04_dashboard.py`

> **Real-time web dashboard for prediction visualization.**

- **File**: `04_dashboard.py`
- **Type**: Flask application with WebSocket

### Web Architecture

- **Backend**: Flask + Flask-SocketIO
- **Frontend**: HTML/CSS/JS vanilla
- **Communication**: WebSocket for real-time updates
- **Refresh**: Auto (5 seconds)

### Features

1. **Real-time data display**:
   - Latest predictions table
   - Price trend charts
   - Prediction confidence indicators
2. **Interactive elements**:
   - Symbol filtering
   - Time range selection
   - Manual refresh option
3. **Visualization**:
   - Color-coded predictions (up/down)
   - Historical trend graphs
   - Performance metrics

---

## Dependencies

### Core Libraries
- **TensorFlow/Keras**: For GRU model
- **PySpark**: For stream processing
- **Kafka-Python**: For data streaming
- **Flask/SocketIO**: For dashboard

### Data Processing
- **Pandas/NumPy**: For data manipulation
- **Scikit-learn**: For feature scaling
- **Matplotlib/Plotly**: For visualization (optional)

---

## Usage Flow

1. **Train model**: Run `01_train_gru.py` to generate and save the model
2. **Start producer**: Run `02_kafka_producer.py` to generate streaming data
3. **Launch streaming**: Run `03_spark_gru_streaming.py` to process and predict
4. **Open dashboard**: Run `04_dashboard.py` and navigate to `http://localhost:5000`

---

## Configuration Notes

- Ensure Kafka is running on `localhost:9092`
- Spark must be properly configured with Kafka libraries
- TensorFlow model requires compatible CUDA for GPU acceleration
- Dashboard uses port 5000 by default (modify if needed)