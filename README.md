Part I: Basic Application (Mandatory)
1. Setup the Project:
a)Choose your tech stack:
b)Frontend: React (recommended for ease of use and flexibility)
c)Backend: Node.js with Express (scalable and suitable for a RESTful API)
d)Database: MongoDB (NoSQL database that pairs well with Node.js)
Initialize your frontend and backend projects:
For React: npx create-react-app rentify
For Node.js: npm init and install necessary packages (express, mongoose, etc.)
2. User Authentication:
Implement user registration and login functionality.
Use JWT (JSON Web Tokens) for authentication.
Create a user schema in MongoDB with fields: first name, last name, email, phone number, password, and role (buyer or seller).
3. Seller Flow:
Create a form for sellers to post property details.
Define essential property attributes (place, area, number of bedrooms, bathrooms, nearby hospitals & colleges, etc.).
Implement CRUD operations for property listings:
Create a new property listing.
View all properties posted by the seller.
Update and delete property listings.
4. Buyer Flow:
Display all rental properties to the buyers.
Implement a button "I'm Interested" for each property.
On clicking "I'm Interested," show the seller's contact details.
5. Filtering:
Implement filtering options for properties based on criteria like location, price, number of bedrooms, etc.
Part II: Add-On Features (Advanced)
1. Form Validation and Pagination:
Add form validation to ensure all required fields are correctly filled out.
Implement pagination for property listings to enhance performance and usability.
2. Authentication for Viewing Seller Details:
Ensure buyers must be logged in to view seller details.
Redirect unauthorized users to the login screen.
3. Like Button:
Add a Like button to each property listing.
Track the like count in real-time using web sockets or similar technology.
4. Email Notifications:
When a buyer clicks on "I'm Interested," send an email to the buyer with the seller's contact details.
Also, send an email to the seller with the buyer's details.
Part III: Bonus Points (Optional)
1. Deploy the Application:
Deploy the application on a cloud platform like Heroku, AWS, or Microsoft Azure.
Ensure both the frontend and backend are deployed and communicate correctly.
Project Structure
Frontend (React)
Components:
Login/Register
PropertyList
PropertyDetails
PropertyForm (for sellers)
Filters
Services:
AuthService (for handling login/register)
PropertyService (for CRUD operations on properties)
Routes:
Implement routing using react-router-dom
Backend (Node.js/Express)
Models:
User
Property
Controllers:
AuthController (for handling user authentication)
PropertyController (for handling CRUD operations)
Routes:
Auth routes (/api/auth/register, /api/auth/login)
Property routes (/api/properties)
Database (MongoDB)
Collections:
Users
Properties
Step-by-Step Implementation
Initialize Projects:

Set up the React app and Express server.
Connect to MongoDB.
Implement Authentication:

Create user registration and login endpoints.
Implement JWT authentication.
Create Seller and Buyer Flows:

Design and implement the property form for sellers.
Implement the property listing and filtering for buyers.
Add Features:

Implement form validation, pagination, and like button.
Set up email notifications using a service like SendGrid.
Deploy the Application:

Push the frontend to a cloud service.
Deploy the backend and connect it to the frontend.
Submission
Zip the source code and include a README with instructions.
Provide an online deployment link.
