# G-Solution-Smart-Supply-Chains


The model used in this notebook is a Temporal Graph Neural Network (Temporal GNN) called TemporalSupplyChainGNN. It is designed to predict supply chain disruptions.

Here are its key characteristics:

Architecture: It uses SAGEConv layers for node encoding, a custom TemporalAttention mechanism to weigh past snapshots, and a Multi-Layer Perceptron (MLP) (edge_mlp) to predict edge disruption probabilities.
Node Features: It processes 5-dimensional node features (initially 1-dim, updated to 5-dim in Cell 20), which include aggregated port-level statistics like traffic_count, avg_speed, avg_dist, avg_eta, and disruption_rate.
Edge Features: It takes 3-dimensional edge features (SOG_kmh, dist_km, ETA_hours) as input.
Temporal Window: It considers a window of 3 past snapshots for its temporal attention mechanism.
Output: It outputs a disruption probability (a single scalar) for each edge.
