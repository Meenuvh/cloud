# BACKEND CODE
// Import necessary modules
const express = require('express');
const cors = require('cors');

// Create an instance of Express
const app = express();

// Enable CORS for cross-origin requests and JSON parsing
app.use(cors());
app.use(express.json());

// Sample in-memory storage for analysis results
const analysisHistory = [];

// Endpoint to analyze a message
app.post('/analyze', (req, res) => {
    const { user_id, message } = req.body;

    if (!message) {
        return res.status(400).json({ error: 'Message is required' });
    }

    // Perform basic analysis
    const messageLength = message.length;
    const timestamp = new Date().toISOString();

    // Send analysis result back to the user
    res.json({
        message_length: messageLength,
        timestamp: timestamp,
        original_message: message
    });
});

// Endpoint to save the analysis result
app.post('/save-analysis', (req, res) => {
    const { user_id, message, message_length, timestamp } = req.body;

    if (!user_id || !message || !message_length || !timestamp) {
        return res.status(400).json({ error: 'All fields are required to save analysis' });
    }

    // Save analysis in the history
    const analysis = { user_id, message, message_length, timestamp };
    analysisHistory.push(analysis);

    res.json({ success: true, message: 'Analysis saved successfully' });
});

// Endpoint to get the analysis history for a specific user
app.get('/get-history', (req, res) => {
    const user_id = req.query.user_id;

    if (!user_id) {
        return res.status(400).json({ error: 'User ID is required to retrieve history' });
    }

    // Filter analysis history by user_id
    const userHistory = analysisHistory.filter(item => item.user_id === user_id);

    res.json({ history: userHistory });
});

// Start the server on Replit’s default port
const port = process.env.PORT || 3000;
app.listen(port, () => {
    console.log(`Server is running on port ${port}`);
});
