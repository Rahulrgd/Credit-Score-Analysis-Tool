# Credit Score Analysis Tool

### Disclaimer
This document uses fictional names for the project and client for illustrative and educational use only. Any resemblance to real entities is coincidental.

## 1. Introduction
Credit Score Analysis Tool is a project. The goal of this tool is to automate the process of credit score evaluation by integrating data from various sources including credit bureaus and banking transaction records. This tool provides real-time credit scoring, predictive analytics based on customer behavior, and detailed reporting features for bank staff. It was designed using a microservices architecture, ensuring scalability and efficient management of different service components.

## 2. Project Architecture

### ii) API Gateway
The API Gateway acts as the single entry point for all client requests to the backend services. It routes requests to the appropriate microservices and provides common functionalities like authentication and logging. Spring Cloud Gateway is used for this purpose, integrating easily with other Spring Boot applications.

### iii) Microservices
The application is split into microservices, each handling different functionalities. This setup simplifies management, debugging, updating, and scaling. The microservices in this project include:
- **Data Collection Service:** Manages the ingestion and preprocessing of financial data from various sources.
- **Credit Scoring Service:** Calculates credit scores using a predefined algorithm, processing input from the Data Collection Service.
- **User Management Service:** Handles authentication and user account management.
- **Report Service:** Generates detailed credit reports and analytics based on the calculated credit scores.

### iv) Database
MySQL is used for each microservice, although different databases can be utilized for each microservice.

### v) Cache Mechanism
Redis is used as an in-memory cache solution for storing frequently accessed data, such as credit score calculations and external data retrieved from APIs.

### vi) Logging Tools
Log4j2 provides powerful logging capabilities for Java applications. Splunk is integrated for searching, analyzing, and visualizing machine-generated data, including logs, in real-time.

### vii) Messaging System
Kafka is used for real-time updating operations, such as calculating credit scores in real time and handling asynchronous communications.

### viii) Security
OAuth is implemented for secure access control, allowing users to sign into the application using accounts from different places.

### ix) Deployment and Operations
- **Containerization:** Docker is used to containerize the microservices, making them easy to deploy and scale.
- **Orchestration:** Kubernetes manages these containers, handling deployment, scaling, and operation of application containers across clusters of hosts.
- **CI/CD:** Jenkins automates the build, test, and deployment processes.
- **Git Collaboration Tool:** GitLab is used for version control and collaboration.

## 3. Project Flow

### Step 1: User Interaction and Authentication
- **Action:** A bank officer logs into the system through the User Interface.
- **Process:**
  - The User Management Service handles the login request, authenticating the user credentials against the database.
  - Upon successful authentication, an access token is generated using OAuth for secure sessions.
  - For any user request, the access token is sent to the server for authorization.

### Step 2: Data Collection and Processing
- **Action:** The bank officer initiates a credit score check for a client.
- **Process:**
  - The Data Collection Service gathers financial data from internal databases and external credit bureaus.
  - This service validates and preprocesses the data to prepare it for analysis.

### Step 3: Credit Scoring Calculation
- **Action:** Processed data is forwarded to the Credit Scoring Service.
- **Process:**
  - The Credit Scoring Service receives the cleansed data and applies a scoring model to evaluate credit risk based on historical data.
  - The resulting credit score and detailed analytics are sent to the Report Service.

### Step 4: Report Generation and Delivery
- **Action:** Generation of a credit report based on the calculated score.
- **Process:**
  - The Report Service fetches the score and analysis from the Credit Scoring Service.
  - It compiles a detailed credit report, which may include recommendations or flags for high-risk factors.
  - The report is made available to the bank officer via the UI and can also be sent to other stakeholders via email.

### Common Activities
- Throughout all steps, data transactions and user actions are logged and can be seen on Splunk.
- All interactions between the client and microservices go through the Spring Cloud Gateway.
- Kafka is used for operations that require real-time data updating.

## 4. Use of Kafka
Kafka is used for real-time credit scoring updates, with the topic name “credit-score-updates” in this project:
- **Description:** Kafka streams trigger credit scoring calculations as soon as new data is available.
- **Implementation:** New financial data is published to a specific Kafka topic. The Credit Scoring Service listens to this topic and calculates or updates credit scores in real-time.

## 5. Use of Log4j2
Log4j2 provides powerful logging capabilities for Java applications. It is configured to send logs directly to Splunk using Splunk Universal Forwarder. 

## 6. Integration with Splunk
Splunk is used for searching, analyzing, and visualizing machine-generated data in real-time. Log4j2 is configured to send logs to Splunk, including additional metadata for log analysis.

## 7. Use of Redis Cache
Redis is used for storing frequently accessed data, such as credit score calculations and external data retrieved from APIs. This optimizes application performance and reduces database load.

## 8. Use of CI/CD Pipeline
- **Version Control System:** Git is used, hosted on GitHub or GitLab, to manage and track code changes.
- **Continuous Integration Tools:** Jenkins manages the process of automatically checking and merging code submissions.
- **Build Automation:** Jenkins compiles code, runs tests, and analyzes potential errors using tools like Maven or Gradle.
- **Artifact Repository:** Built files are stored in an artifact repository like Nexus or Artifactory.
- **Continuous Deployment:**
  - **Containerization:** Docker packages the application into containers.
  - **Orchestration:** Kubernetes manages these containers.
  - **Deployment Strategies:** Techniques like blue-green deployments or canary releases are used for minimal disruption during updates.

## 9. Data Layer / Schemas
MySQL is used for the microservices. Major tables include:

### Users Table
- `user_id` (Primary Key)
- `username`
- `password_hash`
- `email`
- `role`
- `created_at`
- `updated_at`
- `last_login`
- `status` (e.g., active, inactive)

### Credit Scores Table
- `credit_score_id` (Primary Key)
- `user_id` (Foreign Key)
- `score`
- `date`
- `score_type` (e.g., FICO, VantageScore)
- `score_history` (JSON or array)
- `algorithm_used`

### Financial Records Table
- `record_id` (Primary Key)
- `user_id` (Foreign Key)
- `transaction_date`
- `amount`
- `transaction_type` (e.g., credit, debit)
- `transaction_description`
- `category` (e.g., utilities, groceries, entertainment)

### Reports Table
- `report_id` (Primary Key)
- `user_id` (Foreign Key)
- `credit_score_id` (Foreign Key)
- `generated_date`
- `report_data` (JSON or XML)
- `report_type` (e.g., standard, detailed)

### Audit Logs Table
- `log_id` (Primary Key)
- `user_action`
- `timestamp`
- `user_id` (Foreign Key)
- `details` (description of the action taken)

## 10. REST APIs

### Data Collection Service
1. `GET /data/{userId}` - Retrieves all financial data for a specific user.
2. `GET /data/{userId}/transactions` - Retrieves transaction history for a user.
3. `POST /data` - Submits new financial data.
4. `PUT /data/{userId}` - Updates existing financial data for a user.
5. `DELETE /data/{userId}` - Removes financial data for a user.
6. `GET /data/{userId}/loans` - Fetches loan data for a user.
7. `POST /data/batch` - Submits financial data for multiple users.
8. `GET /data/stats` - Provides statistics about collected data.

### Credit Scoring Service
1. `GET /score/{userId}` - Fetches the credit score for a user.
2. `POST /score/calculate` - Calculates the credit score based on provided data.
3. `PUT /score/{userId}` - Updates the credit score for a user.
4. `DELETE /score/{userId}` - Deletes the credit score data for a user.
5. `GET /score/history/{userId}` - Retrieves the score history of a user.
6. `POST /score/batch` - Calculates scores for multiple users.
7. `GET /score/average` - Provides the average score across all users.
8. `PUT /score/refresh` - Refreshes and recalculates scores based on latest data.

### User Management Service
1. `GET /users/{userId}` - Retrieves user details.
2. `POST /users` - Registers a new user.
3. `PUT /users/{userId}` - Updates user details.
4. `DELETE /users/{userId}` - Deletes a user.
5. `GET /users/all` - Retrieves all users.
6. `POST /users/authenticate` - Authenticates