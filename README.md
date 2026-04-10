# G-Solution-Smart-Supply-Chains


The model used in this notebook is a Temporal Graph Neural Network (Temporal GNN) called TemporalSupplyChainGNN. It is designed to predict supply chain disruptions.

Here are its key characteristics:

Architecture: It uses SAGEConv layers for node encoding, a custom TemporalAttention mechanism to weigh past snapshots, and a Multi-Layer Perceptron (MLP) (edge_mlp) to predict edge disruption probabilities.
Node Features: It processes 5-dimensional node features (initially 1-dim, updated to 5-dim in Cell 20), which include aggregated port-level statistics like traffic_count, avg_speed, avg_dist, avg_eta, and disruption_rate.
Edge Features: It takes 3-dimensional edge features (SOG_kmh, dist_km, ETA_hours) as input.
Temporal Window: It considers a window of 3 past snapshots for its temporal attention mechanism.
Output: It outputs a disruption probability (a single scalar) for each edge.

BigQuery plays several crucial roles in handling and analyzing the large datasets, leveraging its serverless and scalable nature:

Data Storage and Management (BQ-B): The processed AIS ship tracking data (df_ais), port metadata (port_registry), and the engineered edge features (edges_df) are uploaded and stored as tables in BigQuery (within the ship_supply_chain dataset). This allows for efficient storage and querying of large datasets that might exceed Colab's memory.
Historical Inference Logging (BQ-1, BQ-2, BQ-3): A separate BigQuery dataset (ship_logistics) and table (inference_history) are set up to log the results of the model's live inference loop. This creates a historical record of snapshot statistics and mean risk scores over time, enabling long-term monitoring and analysis.
Serverless Feature Engineering (BQ-C, BQ-D): BigQuery is used to perform complex data aggregations and joins directly on the data stored within it. Specifically, a temporal join aggregates ship congestion and average speed per port per hour, leveraging BigQuery's SQL capabilities without pulling large amounts of raw data into Colab. BigFrames is then used to efficiently retrieve these aggregated features back into the notebook.
Machine Learning Baseline (BQ-E): BigQuery ML is utilized to train and evaluate a Logistic Regression model as a baseline for disruption prediction. This demonstrates BigQuery's built-in machine learning capabilities, allowing for model training and evaluation directly within the database using SQL.
