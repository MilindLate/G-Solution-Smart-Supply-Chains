# G-Solution-Smart-Supply-Chains


This notebook is all about predicting disruptions in supply chains using ship tracking data. Here's a simple breakdown of what we've done:

Loaded and Prepared Data: We started by loading ship movement data (AIS), port information, and weather data. We then cleaned and processed the ship movement data to get it ready for analysis.
Created a Graph of Routes: We transformed this data into a 'graph' format, where locations (like port zones) are 'nodes' and ship movements between them are 'edges'. We also identified movements that might be disrupted, usually due to significantly slow speeds.
Built a Smart Prediction Model: We designed a special type of AI model called a Temporal Graph Neural Network (GNN). This model is good at understanding patterns in connected data over time, like ship routes, and uses a 'memory' to learn from past movements.
Trained the Model: We fed our prepared graphs into the GNN so it could learn to predict which routes are likely to experience disruptions.
Checked How Well It Works: After training, we tested the model to see how accurately it predicted disruptions and visualized its performance.
Created a 'Memory' for Routes: We developed a system that remembers the historical risk of disruption for each route, making our predictions more robust.
Simulated Real-time Risk Assessment: We showed how the model could be used to give an instant risk score for a specific ship route, blending its current prediction with the historical memory.
Performed 'What-If' Scenarios: We explored how changes, like a ship slowing down, would impact the predicted risk for different routes.
Saved the Brains of the Model: Finally, we saved the trained model so it can be used later without needing to be retrained.
