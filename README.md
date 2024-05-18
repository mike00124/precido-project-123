Backend: Node.js with Express and MongoDB
1. Initialize the Project:
 mkdir rentify-backend
cd rentify-backend
npm init -y
npm install express mongoose bcryptjs jsonwebtoken nodemailer cors
2. Create the Directory Structure:
3. rentify-backend
│   package.json
│   server.js
└───config
│       db.js
└───controllers
│       authController.js
│       propertyController.js
└───models
│       User.js
│       Property.js
└───routes
        auth.js
        properties.js
3. Database Configuration (config/db.js):
4. const mongoose = require('mongoose');

const connectDB = async () => {
  try {
    await mongoose.connect('your_mongodb_connection_string', {
      useNewUrlParser: true,
      useUnifiedTopology: true,
    });
    console.log('MongoDB connected');
  } catch (err) {
    console.error(err.message);
    process.exit(1);
  }
};

module.exports = connectDB;
##4. User Model (models/User.js):
const mongoose = require('mongoose');

const UserSchema = new mongoose.Schema({
  firstName: { type: String, required: true },
  lastName: { type: String, required: true },
  email: { type: String, required: true, unique: true },
  phoneNumber: { type: String, required: true },
  password: { type: String, required: true },
  role: { type: String, enum: ['buyer', 'seller'], required: true },
});

module.exports = mongoose.model('User', UserSchema);
##5. Property Model (models/Property.js):
const mongoose = require('mongoose');

const PropertySchema = new mongoose.Schema({
  place: { type: String, required: true },
  area: { type: Number, required: true },
  bedrooms: { type: Number, required: true },
  bathrooms: { type: Number, required: true },
  nearby: { type: String },
  likes: { type: Number, default: 0 },
  seller: { type: mongoose.Schema.Types.ObjectId, ref: 'User' },
});

module.exports = mongoose.model('Property', PropertySchema);
##6. Auth Controller (controllers/authController.js):
const bcrypt = require('bcryptjs');
const jwt = require('jsonwebtoken');
const User = require('../models/User');

exports.register = async (req, res) => {
  const { firstName, lastName, email, phoneNumber, password, role } = req.body;
  
  try {
    let user = await User.findOne({ email });
    if (user) return res.status(400).json({ msg: 'User already exists' });

    user = new User({ firstName, lastName, email, phoneNumber, password, role });
    const salt = await bcrypt.genSalt(10);
    user.password = await bcrypt.hash(password, salt);
    
    await user.save();

    const payload = { user: { id: user.id, role: user.role } };
    jwt.sign(payload, 'your_jwt_secret', { expiresIn: 3600 }, (err, token) => {
      if (err) throw err;
      res.json({ token });
    });
  } catch (err) {
    res.status(500).send('Server error');
  }
};

exports.login = async (req, res) => {
  const { email, password } = req.body;

  try {
    const user = await User.findOne({ email });
    if (!user) return res.status(400).json({ msg: 'Invalid credentials' });

    const isMatch = await bcrypt.compare(password, user.password);
    if (!isMatch) return res.status(400).json({ msg: 'Invalid credentials' });

    const payload = { user: { id: user.id, role: user.role } };
    jwt.sign(payload, 'your_jwt_secret', { expiresIn: 3600 }, (err, token) => {
      if (err) throw err;
      res.json({ token });
    });
  } catch (err) {
    res.status(500).send('Server error');
  }
};
##. Property Controller (controllers/propertyController.js):
const Property = require('../models/Property');

exports.createProperty = async (req, res) => {
  const { place, area, bedrooms, bathrooms, nearby } = req.body;
  
  try {
    const newProperty = new Property({
      place,
      area,
      bedrooms,
      bathrooms,
      nearby,
      seller: req.user.id,
    });

    const property = await newProperty.save();
    res.json(property);
  } catch (err) {
    res.status(500).send('Server error');
  }
};

exports.getProperties = async (req, res) => {
  try {
    const properties = await Property.find().populate('seller', ['firstName', 'lastName']);
    res.json(properties);
  } catch (err) {
    res.status(500).send('Server error');
  }
};

exports.updateProperty = async (req, res) => {
  const { place, area, bedrooms, bathrooms, nearby } = req.body;

  try {
    let property = await Property.findById(req.params.id);
    if (!property) return res.status(404).json({ msg: 'Property not found' });

    if (property.seller.toString() !== req.user.id) {
      return res.status(401).json({ msg: 'User not authorized' });
    }

    property = await Property.findByIdAndUpdate(
      req.params.id,
      { $set: { place, area, bedrooms, bathrooms, nearby } },
      { new: true }
    );

    res.json(property);
  } catch (err) {
    res.status(500).send('Server error');
  }
};

exports.deleteProperty = async (req, res) => {
  try {
    let property = await Property.findById(req.params.id);
    if (!property) return res.status(404).json({ msg: 'Property not found' });

    if (property.seller.toString() !== req.user.id) {
      return res.status(401).json({ msg: 'User not authorized' });
    }

    await Property.findByIdAndRemove(req.params.id);
    res.json({ msg: 'Property removed' });
  } catch (err) {
    res.status(500).send('Server error');
  }
};
##8. Authentication Middleware (middleware/auth.js):
const jwt = require('jsonwebtoken');

module.exports = function (req, res, next) {
  const token = req.header('x-auth-token');

  if (!token) return res.status(401).json({ msg: 'No token, authorization denied' });

  try {
    const decoded = jwt.verify(token, 'your_jwt_secret');
    req.user = decoded.user;
    next();
  } catch (err) {
    res.status(401).json({ msg: 'Token is not valid' });
  }
};

