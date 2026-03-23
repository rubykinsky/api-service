// api-service.js
const express = require('express');
const rateLimit = require('express-rate-limit');
const helmet = require('helmet');
const morgan = require('morgan');
const cors = require('cors');
const mongoose = require('mongoose');
const jwt = require('jsonwebtoken');
const redis = require('redis');

const app = express();
const port = 3000;

// Connect to MongoDB
mongoose.connect(process.env.MONGO_URI, { useNewUrlParser: true, useUnifiedTopology: true });

// Connect to Redis
const redisClient = redis.createClient({
  host: process.env.REDIS_HOST,
  port: process.env.REDIS_PORT
});

// Rate limiting
const limiter = rateLimit({
  windowMs: 15 * 60 * 1000, // 15 minutes
  max: 100 // limit each IP to 100 requests per windowMs
});

// Middleware
app.use(helmet());
app.use(morgan('combined'));
app.use(cors());
app.use(express.json());
app.use(limiter);

// Authentication middleware
const authenticate = async (req, res, next) => {
  const token = req.header('Authorization');
  if (!token) return res.status(401).send('Access denied. No token provided.');

  try {
    const decoded = jwt.verify(token, process.env.JWT_SECRET);
    req.user = decoded;
    next();
  } catch (ex) {
    res.status(400).send('Invalid token.');
  }
};

// Routes
app.get('/api/health', (req, res) => {
  res.send('API is running');
});

app.get('/api/users', authenticate, async (req, res) => {
  try {
    const users = await mongoose.model('User').find();
    res.send(users);
  } catch (ex) {
    res.status(500).send('Error fetching users');
  }
});

// Error handling
app.use((err, req, res, next) => {
  console.error(err);
  res.status(500).send('Internal Server Error');
});

// Start the service
app.listen(port, () => {
  console.log(`API service listening on port ${port}`);
});
```

```javascript
// index.js
const apiService = require('./api-service');

const start = async () => {
  try {
    await apiService.start();
    console.log('API service started');
  } catch (ex) {
    console.error(ex);
  }
};

start();
```

```javascript
// package.json
{
  "name": "api-service",
  "version": "1.0.0",
  "scripts": {
    "start": "node index.js",
    "dev": "nodemon index.js"
  },
  "dependencies": {
    "express": "^4.17.1",
    "express-rate-limit": "^5.1.3",
    "helmet": "^5.0.0",
    "mongoose": "^6.0.1",
    "morgan": "^1.10.0",
    "cors": "^2.8.5",
    "jsonwebtoken": "^8.5.1",
    "redis": "^3.0.2"
  }
}
```

```javascript
//.env
MONGO_URI=mongodb://localhost:27017/
REDIS_HOST=localhost
REDIS_PORT=6379
JWT_SECRET=your-jwt-secret
```

```bash
//.gitignore
node_modules/
.env
```

```javascript
// docs/api.md
# API Documentation

## Endpoints

### GET /api/health
Check if the API service is running.

### GET /api/users
Get a list of all users. Requires authentication.

## Authentication

*   Use the `Authorization` header with a JSON Web Token (JWT) to authenticate requests.
*   The JWT should be generated using the `JWT_SECRET` environment variable.