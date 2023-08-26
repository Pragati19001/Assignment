# Assignment
Use case round assignment

# Step1: Frontend code of registration and login page

# Registration.js

import React, { useState } from 'react';
import './RegistrationPage.css';
import axios from 'axios';  
function RegistrationPage() {
  const [formData, setFormData] = useState({
    firstName: '',
    lastName: '',
    mobileNumber: '',
    password: '',
    profilePicture: null,
  });

  const handleInputChange = event => {
    const { name, value } = event.target;
    setFormData({
      ...formData,
      [name]: value,
    });
  };

  const handleFileChange = event => {
    setFormData({
      ...formData,
      profilePicture: event.target.files[0],
    });
  };

  const handleSubmit = async event => {
    event.preventDefault();

    const formDataToSend = new FormData();
    formDataToSend.append('firstName', formData.firstName);
    formDataToSend.append('lastName', formData.lastName);
    formDataToSend.append('mobileNumber', formData.mobileNumber);
    formDataToSend.append('password', formData.password);
    formDataToSend.append('profilePicture', formData.profilePicture);

    try {
      await axios.post('http://localhost:5000/api/register', formDataToSend, {
        headers: {
          'Content-Type': 'multipart/form-data',
        },
      });
      console.log('Registration successful');
    } catch (error) {
      console.error('Registration failed:', error);
    }
  };

  return (
    <div className="registration-container">
      <p>Registration Page</p>
      <form onSubmit={handleSubmit}>
        <div className="form-group">
          <label htmlFor="firstName">First Name:</label>
          <input type="text" id="firstName" name="firstName" onChange={handleInputChange} required />
        </div>
        <div className="form-group">
          <label htmlFor="lastName">Last Name:</label>
          <input type="text" id="lastName" name="lastName" onChange={handleInputChange} required />
        </div>
        <div className="form-group">
          <label htmlFor="mobileNumber">Mobile Number:</label>
          <input type="text" id="mobileNumber" name="mobileNumber" onChange={handleInputChange} required />
        </div>
        <div className="form-group">
          <label htmlFor="password">Password:</label>
          <input type="password" id="password" name="password" onChange={handleInputChange} required />
        </div>
        <div className="form-group">
          <label htmlFor="profilePicture">Profile Picture:</label>
          <input type="file" id="profilePicture" name="profilePicture" accept="image/*" onChange={handleFileChange} />
        </div>
        <button type="submit">Register</button>
      </form>
    </div>
  );
}

export default RegistrationPage;

# Login.js:-
import React, { useState } from 'react';
import './LoginPage.css';

function LoginPage() {
  const [mobileNumber, setMobileNumber] = useState('');
  const [password, setPassword] = useState('');

  const handleLogin = () => {
    window.location.href = `/landing/${user.id}`;
  };

  return (
    <div className="login-container">
      <p>Login</p>
      <input
        type="text"
        placeholder="Mobile Number"
        value={mobileNumber}
        onChange={(e) => setMobileNumber(e.target.value)}
      />
      <input
        type="password"
        placeholder="Password"
        value={password}
        onChange={(e) => setPassword(e.target.value)}
      />
      <button onClick={handleLogin}>Login</button>
    </div>
  );
}

export default LoginPage;


# step 2:- Creating a database

CREATE TABLE users (
    id INT AUTO_INCREMENT PRIMARY KEY,
    firstname VARCHAR(50),
    lastname VARCHAR(50),
    mobileno VARCHAR(15),
    password VARCHAR(100),
    profilepicture VARCHAR(255),
    createdby INT,
    createddate TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updatedby INT,
    updateddate TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP
);

# step 3:- connecting database with API

const express = require('express');
const mysql = require('mysql2'); // Use mysql2 instead of mysql
const cors = require('cors');
const bodyParser = require('body-parser');

const app = express();

app.use(cors());
app.use(bodyParser.json());

const db = mysql.createConnection({
  host: 'localhost',
  user: 'root',        // Your MySQL username
  password: '',        // Your MySQL password
  database: 'user',
});

db.connect(err => {
  if (err) {
    console.error('Database connection error:', err);
  } else {
    console.log('Database connected successfully');
  }
});

app.post('/api/register', (req, res) => {
  const { firstName, lastName, mobileNumber, password, profilePicture } = req.body;

  if (!firstName || !lastName || !mobileNumber || !password) {
    return res.status(400).json({ success: false, message: 'All fields are required' });
  }

  const query = 'INSERT INTO users (firstName, lastName, mobileNumber, password, profilePicture) VALUES (?, ?, ?, ?, ?)';
  db.query(query, [firstName, lastName, mobileNumber, password, profilePicture], (err, result) => {
    if (err) {
      console.error('Registration error:', err);
      return res.status(500).json({ success: false, message: 'Registration failed' });
    }
    console.log('User registered successfully');
    return res.status(200).json({ success: true, message: 'Registration successful' });
  });
});

const PORT = process.env.PORT || 5000;
app.listen(PORT, () => {
  console.log(`Server is running on port ${PORT}`);
});
