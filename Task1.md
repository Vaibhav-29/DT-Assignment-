# DT-Assignment-
Assignment for DT Backend Developer Task 1

// Import required dependencies
const express = require('express');
const mongoose = require('mongoose');

// Set up Express app
const app = express();
app.use(express.json());

// Connect to MongoDB database
mongoose.connect('mongodb://localhost/myapp', { useNewUrlParser: true });

// Define event schema using Mongoose
const eventSchema = new mongoose.Schema({
  type: String,
  uid: Number,
  name: String,
  tagline: String,
  schedule: Date,
  description: String,
  files: {
    image: String,
  },
  moderator: Number,
  category: String,
  sub_category: String,
  rigor_rank: Number,
  attendees: [Number],
});

// Define event model using event schema
const Event = mongoose.model('Event', eventSchema);

// Define API endpoints for events
// GET /api/v3/app/events?id=:event_id
app.get('/api/v3/app/events', async (req, res) => {
  try {
    const event = await Event.findById(req.query.id);
    res.json(event);
  } catch (err) {
    res.status(500).json({ message: err.message });
  }
});

// GET /api/v3/app/events?type=latest&limit=5&page=1
app.get('/api/v3/app/events', async (req, res) => {
  const { type, limit, page } = req.query;
  try {
    const events = await Event.find()
      .sort({ schedule: -1 })
      .skip((page - 1) * limit)
      .limit(Number(limit));
    res.json(events);
  } catch (err) {
    res.status(500).json({ message: err.message });
  }
});

// POST /api/v3/app/events
app.post('/api/v3/app/events', async (req, res) => {
  const event = new Event(req.body);
  try {
    const newEvent = await event.save();
    res.status(201).json(newEvent._id);
  } catch (err) {
    res.status(400).json({ message: err.message });
  }
});

// PUT /api/v3/app/events/:id
app.put('/api/v3/app/events/:id', async (req, res) => {
  try {
    const event = await Event.findById(req.params.id);
    event.set(req.body);
    const updatedEvent = await event.save();
    res.json(updatedEvent);
  } catch (err) {
    res.status(400).json({ message: err.message });
  }
});

// DELETE /api/v3/app/events/:id
app.delete('/api/v3/app/events/:id', async (req, res) => {
  try {
    const event = await Event.findById(req.params.id);
    await event.remove();
    res.json({ message: 'Event deleted successfully' });
  } catch (err) {
    res.status(500).
