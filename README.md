# MERN-Stack-Coding-Challenge
npm init -y
npm install express mongoose axios body-parser
const express = require('express');
const mongoose = require('mongoose');
const axios = require('axios');
const bodyParser = require('body-parser');

const app = express();
app.use(bodyParser.json());

// Connect to MongoDB
mongoose.connect('mongodb://localhost:27017/roxiler', { useNewUrlParser: true, useUnifiedTopology: true })
  .then(() => console.log('MongoDB connected'))
  .catch(err => console.log(err));

// Define ProductTransaction Schema
const ProductTransactionSchema = new mongoose.Schema({
  product_id: String,
  title: String,
  description: String,
  price: Number,
  category: String,
  dateOfSale: Date,
  sold: Boolean,
});

const ProductTransaction = mongoose.model('ProductTransaction', ProductTransactionSchema);

// API to initialize the database
app.get('/api/init-db', async (req, res) => {
  try {
    const response = await axios.get('https://s3.amazonaws.com/roxiler.com/product_transaction.json');
    const transactions = response.data;

    // Clean the collection and insert data
    await ProductTransaction.deleteMany({});
    await ProductTransaction.insertMany(transactions);

    res.json({ message: 'Database initialized with seed data' });
  } catch (error) {
    res.status(500).json({ error: 'Failed to initialize the database' });
  }
});
