# HCL_Hackathon
# Healthcare Wellness and Preventive Care Portal - Design Document
##1. Summary
This document outlines the design for a Healthcare Wellness and Preventive Care Portal aimed at helping patients achieve wellness goals and maintain preventive care compliance.
--
##2. System Overview
### 2.1Purpose
The portal enables patients to track wellness goals, receive preventive care reminders, and manage health profiles.
Healthcare providers can monitor patient compliance and progress. The system emphasizes security, privacy, and basic HIPAA compliance measures.
### 2.2 Key Objectives
- Secure authentication with role-based access control
- Patient wellness goal tracking and progress monitoring
- Preventive care reminder system
- Healthcare provider dashboard for patient monitoring
- HIPAA-compliant data handling and privacy measures
  ## 3. Technology Stack
### 3.1 Frontend
- *Framework:* ReactS
- *Styling:* CSS Modules
- *HTTP Client:* Axios
- *Routing:* React Router
### 3.2 Backend
- *Runtime:* Node.js
- *Framework:* Express.js
- *Authentication:* JSON Web Tokens (JWT)
- *Password Hashing:* bcrypt
- *Environment Management:* dotenv
### 3.3 Database
- *RDBMS:* MySQL 8.0+
### 3.4 Cloud & DevOps
- *Frontend Hosting:* Netlify
- *Backend Hosting:* Netlify
- *CI/CD:* GitHub Actions
- *Version Control:* GitHub
---
## 4. System Architecture
### 4.1 Architecture Pattern
1. *Presentation Layer:* React frontend
2. *Application Layer:* Express.js REST API
3. *Data Layer:* MySQL database
### 4.2 Architecture Principles
- *Separation of Concerns:* Clear boundaries between frontend, backend, and database
- *Stateless API:* JWT-based authentication ensures stateless server architecture
-*RESTful Design:* Standard HTTP methods (GET, POST, PUT, DELETE)
- *Security-First Approach:* Data encryption, secure sessions, input validation
### 4.3 Data Flow
1. User interacts with React frontend
2. Frontend sends HTTP requests to Express API
3. API validates JWT tokens and user permissions
4. API processes business logic and queries MySQL database
5. Database returns data to API
6. API sends JSON response to frontend
7. Frontend updates Ul based on response ## 5. Database Design
### 5.1 MySQL Database Schema
#### 5.1.1 Users Table
***sql
CREATE TABLE users (
user_id INT PRIMARY KEY AUTO_INCREMENT, email VARCHAR(255) UNIQUE NOT NULL, password_hash VARCHAR(255) NOT NULL, role ENUM('patient, 'provider') NOT NULL, first_name VARCHAR(100) NOT NULL, last_name VARCHAR(100) NOT NULL, phone VARCHAR (20), created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP, updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP, last_login TIMESTAMP NULL, is_active BOOLEAN DEFAULT TRUE, consent given BOOLEAN DEFAULT FALSE, INDEX idx_email (email), INDEX idx role (role)
);
#拼## 5.1.2 Patient Profiles Table
***sql
CREATE TABLE patient_ profiles ( profile_ id INT PRIMARY KEY AUTO_INCREMENT, user_id INT UNIQUE NOT NULL, date_of_birth DATE, blood _group VARCHAR(10), allergies TEXT, current_medications TEXT, emergency_contact_name VARCHAR(100), emergency_contact_phone VARCHAR(20), created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP, updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP, FOREIGN KEY (user_id) REFERENCES users(user_id) ON DELETE CASCADE
);

#### 5.1.3 Provider Profiles Table
"'*sql
CREATE TABLE provider_profiles (
provider _profile_id INT PRIMARY KEY AUTO_INCREMENT, user_id INT UNIQUE NOT NULL, specialization VARCHAR(100), license_number VARCHAR(50), department VARCHAR(100), created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP, updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP, FOREIGN KEY (user_id) REFERENCES users(user_id) ON DELETE CASCADE
);
...
#### 5.1.4 Patient Provider Assignment Table
•**sql
FOREIGN KEY (patient_id) REFERENCES users(user_id) ON DELETE CASCADE, FOREIGN KEY (provider_id) REFERENCES users(user_id) ON DELETE CASCADE,
INDEX idx_ provider (provider_id)
CREATE TABLE patient_provider_assignments (
assignment_id INT PRIMARY KEY AUTO_INCREMENT,
patient_id INT NOT NULL,
provider_id INT NOT NULL,
assigned _date TIMESTAMP DEFAULT CURRENT_TIMES TAMP,
is_active BOOLEAN DEFAULT TRUE,
UNIQUE KEY unique_assignment (patient_id, provider_id),
INDEX idx_patient (patient_id),
### 5.1.5 Wellness Coals Table
CREATE TABLE wellness goals ( goal_id INT PRIMARY KEY AUTO_ INCREMENT, user_id INT NOT NULL,
goaL type ENUM('steps, 'water_ intake", 'sleep_hours', 'exercise_minutes', 'custom') NOT NULL, goal_name VARCHAR(100) NOT NUI target_value DECIMAL(10,2) NOT NULL, current_ value DECIMAL (10,2) DEFAULT 0, unit VARCHAR (20), start_date DATE NOT NULL, end_date DATE,
status ENUM('active, 'completed', 'missed') DEFAULT 'active', created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP, updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP, FOREIGN KEY (user_id) REFERENCES users(user_id) ON DELETE CASCADE,
);
INDEX idx user _goals (user_id, status)
#### 5.1.6 Daily Goal Logs Table
CREATE TABLE daily_goal_logs (
log_id INT PRIMARY KEY AUTO_INCREMENT, goal_id INT NOT NULL, user_id INT NOT NULL, log_date DATE NOT NULL, value_logged DECIMAL(10,2) NOT NULL, notes TEXT,
created _at TIMESTAMP DEFAULT CURRENT_TIMESTAMP, FOREIGN KEY (goal_id) REFERENCES wellness_goals(goal_id) ON DELETE CASCADE, FOREIGN KEY (user_id) REFERENCES users(user_id) ON DELETE CASCADE, UNIQUE KEY unique_daily_log (goal_id, log_date), INDEX idx_user_date (user_id, log _date)
);
#### 5.1.7 Preventive Care Reminders Table
CREATE TABLE preventive_care_reminders ( reminder_id INT PRIMARY KEY AUTO_INCREMENT, user_id INT NOT NULL, reminder_type VARCHAR(100) NOT NULL, reminder_name VARCHAR(200) NOT NULL, description TEXT,
scheduled_date DATE NOT NULL, is_completed BOOLEAN DEFAULT FALSE, completed_date DATE NULL,
status ENUM('upcoming", 'completed', 'missed' DEFAULT 'upcoming. created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP, FOREIGN KEY (user_id) REFERENCES users(user_id) ON DELETE CASCADE, INDEX id_user_reminders (user_id, scheduled_date)
);
#### 5.1.8 Health Tips Table
***sql
CREATE TABLE health_tips ( tip_id INT PRIMARY KEY AUTO_INCREMENT, tip_title VARCHAR(200) NOT NULL, tip_ content TEXT NOT NULL, category VARCHAR(50),
is_active BOOLEAN DEFAULT TRUE, display_date DATE,
created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP.
INDEX id_ display_date (display_date)
);
.. .
#### 5.1.9. Audit Logs Table
CREATE TABLE audit_logs ( log_id INT PRIMARY KEY AUTO_INCREMENT, user_ id INT, action VARCHAR(100) NOT NULL, resource_type VARCHAR(50), resource_id INT, ip_address VARCHAR (45), user_agent TEXT,
timestamp TIMESTAMP DEFAULT CURRENT_TIMESTAMP, details JSON,
FOREIGN KEY (user_id) REFERENCES users (user_id) ON DELETE SET NULL, INDEX idx_user_action (user_id, action),
INDEX id_ timestamp (timestamp)
);
### 5.2 Database Relationships
• *One-to-One:* Users → Patient Profiles / Provider Profiles
- *Many-to-Many:* Patients « Providers (via patient provider assignments)
- *One-to-Many:* Users → Wellness Goals → Daily Goal Logs
- *One-to-Many:* Users → Preventive Care Reminders
- *One-to-Many:* Users → Audit Log
POST
/api/auth/register
- Register new user (patient/provider)
POST
/api/auth/login
- User login, returns JWT
POST
Japi/auth/logout
- Logout user (client-side token removal)

POST /api/auth/refresh-token - Refresh expired JWT


GET /api/auth/verify
- Verify JWT validity
--
## 6. API Design
### 6.1 RESTful API Endpoints
#### 6.1.1 Authentication Endpoints
...
#### 6,1.2 Patient Profile Endpoints
GET /api/patients/profile
- Get current patient profile
PUT GET
/api/patients/profile
- Update patient profile
/api/patients/dashboard - Get dashboard data (goals, reminders, tips)
#### 6.1.3 Wellness Goals Endpoints
AAA
GET
/api/goals
- Get all goals for current user

POST /api/goals
- Create new wellness goal

GET /api/goals/:id
- Get specific goal details

PUT /api/goals/:id
- Update goal

DELETE /api/goals/ id
- Delete goal

POST /api/goals/id/log
- Log daily progress for a goal

GET /api/goals/id/logs
- Get all logs for a goal
  #### 6.1.4 Preventive Care Reminders Endpoints
...

GET /api/reminders
- Get all reminders for current user

POST /api/reminders
- Create new reminder

PUT /api/reminders/:id
- Update reminder (e.g., mark completed)

DELETE /api/reminders/:id
- Delete reminder

GET /api/reminders/upcoming
- Get upcoming reminders
#### 6.1.5 Provider Endpoints
GET GET GET
...
/api/providers/patients
-Get list of assigned patients
/api/providers/patients/:id - Get specific patient details /api/providers/compliance - Get patient compliance overview
#### 6.1.6 Public Endpoints
٠٠٠
GET
Japi/public/health-tips - Get health tips (no auth required)
GET
Japi/public/health-info - Get public health information
GET
...
Japi/public/privacy-policy - Get privacy policy
## 7. Authentication & Authorization
### 7.1 JWT-Based Authentication
- *Token Generation:* Upon successful login, generate JWT with user_id, role, and expiration
- *Token Expiration:* Access tokens expire in 24 hours
- *Token Storage:* Stored in HTTP-only cookies or local storage (with XSS protection)
- ###7.2 Role-Based Access Control (RBAC)
*Roles:*
1. Patient:* Access to own profile, goals, reminders, and dashboard
2. *Provider:* Access to assigned patients' data and compliance reports
*Middleware Implementation:
- authenticateToken*: Verify JWT validity
- authorizeRole(['patient'])*: Restrict access to patient-only routes
- 'authorizeRole(['provider'])*: Restrict access to provider-only routes
### 7.3 Password Security
- Passwords hashed using crypt with salt rounds = 10
- Minimum password requirements: 8 characters, 1 uppercase, 1 number
- Password validation on both client and server side
## 8. Frontend Design
### 8.1 Component Structure
#### 8.1.1 Public Components
- LandingPage: Homepage with login/register options
- 'LoginForm': User authentication form
- RegisterForm': New user registration with consent checkbox
- "HealthinfoPage': Static health information
- 'PrivacyPolicyPage': Privacy policy and HIPAA compliance info
#### 8.1.2 Patient Components
- 'PatientDashboard*: Main dashboard with goals, reminders, health tip
- ProfileManagement: View and edit patient profile
- 'Goal Tracker'; Create, view, and log wellness goals
- 'GoalsList*: Display all active goals with progress bars
- 'DailyLogForm*: Form to log daily goal progress
- 'RemindersList': Display upcoming preventive care reminders
#### 8.1.3 Provider Components
- 'ProviderDashboard: Overview of assigned patients
- PatientsList': Clickable list of patients
- 'PatientDetailView*: Individual patient's goals and compliance status
- 'ComplianceReport": Summary of patients' compliance status
- #### 8.1.4 Shared Components
- Navbar: Navigation with role-based menu items
- *PrivateRoute: Protected route wrapper
- LoadingSpinner': Loading indicator
- 'ErrorBoundary': Error handling component
thenticated users
### 8.2 State Management
- *Global State:* User authentication status, user profile, current role
- *Local State:* Form inputs, loading states, error messages
- *Context API:* AuthContext for user authentication state
## 9. Error Handling & Logging
### 9.1 Frontend Error Handling
- Try-catch blocks for API calls
- User-friendly error messages (avoid technical jargon)
- Toast notifications for success/error feedback
- Fallback Ul for failed component rendering
### 9.2 Backend Error Handling
- Global error handler middleware
- Specific error classes: ValidationError, AuthenticationError, NotFoundError
- Detailed server logs (not exposed to client)
- HTTP status codes: 200 (success), 400 (bad request), 401 (unauthorized), 403 (forbidden), 404 (not found), 500 (server error)
### 9.3 Logging Strategy
- *Audit Logs:* Stored in audit_Jogs table
  ## 10. CI/CD Pipeline
### 10.1 GitHub Actions Workflow
*Trigger:* Push to main branch or pull request
*Steps:*
1. *Checkout Code:* Clone repository
2. *Setup Node.js:* Install Node.js 18.x
3. *Install Dependencies:* Run 'npm install" for backend and frontend
4. *Lint Code:* Run ESLint to check code quality
5. *Run Tests:* Execute unit and integration tests
6. *Build Frontend:* Run 'npm run build'
## 11. Testing Strategy
### 11.1 Backend Testing
- *Unit Tests:* JWT generation, password hashing, validation functions
- *Integration Tests:* API endpoints with test database
- *Security Tests:* SQL injection, XSS attempts, unauthorized access ### 11.2 Frontend Testing
- *Component Tests:* Render tests for all components
- *Integration Tests:* User flows (login, create goal, log progress)
- *Accessibility Tests;* ARIA labels, keyboard navigation
### 11.3 Manual Testing Checklist
- Registration with and without consent
- Login with valid/invalid credentials
- Create, edit, delete wellness goals
- Log daily progress
- View and update profile
- Provider viewing patient list and details
- Token expiration and refresh
  ## 12. Conclusion
This design document outlines a comprehensive Healthcare Wellness and Preventive Care Portal that balances functionality, security, and compliance. The architecture leverages modern web technologies (React, Nodejs, Express, MySQL) and implements essential healthcare privacy measures including HIPAA compliance basics.
The system successfully addresses key requirements:
- *Security:* JWT authentication, password hashing, role-based access control
- *Personalization:* Custom wellness goals, preventive care reminders, health tips
- *Compliance:* Audit logging, data encryption, consent management
- *Usability:* Intuitive dashboards for patients and providers, responsive design
The modular design allows for easy extension and scalability, with a clear roadmap for future enhancements. The use of MySQL provides robust relational data management suitable for healthcare applications, while the RESTful API ensures clean separation between frontend and backend.
*End of Document*
