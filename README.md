# jod-appliction-selection-prediction-
Job Applicant Selection Prediction System – A React + Vite web application that uses a Random Forest Machine Learning model to evaluate job applicants based on experience, education, skills, interview performance, aptitude scores, certifications, internships, and projects. The model runs entirely in the browser with no backend required.
# Job Applicant Selection Prediction System

A Machine Learning-powered web application that predicts whether a job applicant is likely to be selected based on their qualifications and performance metrics. The application is built with React, Vite, Tailwind CSS, and a browser-based Random Forest model.

## Features

- Predict applicant selection probability in real time
- Browser-based Machine Learning model (No backend required)
- Interactive applicant evaluation dashboard
- Dataset visualization and exploration
- Model performance analysis
- Responsive and user-friendly interface
- Random Forest Classification algorithm

##  Tech Stack

### Frontend
- React.js
- Vite
- Tailwind CSS
- React Router DOM
- Recharts

### Machine Learning
- Scikit-learn
- Random Forest Classifier
- OneHotEncoder
- ColumnTransformer

##  Input Features

The model evaluates applicants using:

- Experience Years
- Education Level
- Skills Score
- Interview Score
- Aptitude Score
- Certifications Count
- Internship Experience
- Projects Completed

## Machine Learning Model

The project uses a **Random Forest Classifier** trained with:

- 200 Decision Trees
- Balanced Class Weights
- Stratified Train-Test Split
- One-Hot Encoding for Categorical Features

The trained model is exported to JSON and executed directly in the browser for fast predictions.

##  Project Structure

```
job-applicant-selection/
│
├── ml/
│   ├── train.py
│   └── job_applicants.csv
│
├── public/
│   └── job_applicants.csv
│
├── src/
│   ├── components/
│   ├── pages/
│   │   ├── Dashboard.jsx
│   │   ├── Evaluation.jsx
│   │   ├── Dataset.jsx
│   │   └── Performance.jsx
│   ├── ml/
│   │   ├── model.js
│   │   └── model.json
│   └── App.jsx
│
└── package.json
```

## Installation

### Clone the Repository

```bash
git clone https://github.com/your-username/job-applicant-selection.git
cd job-applicant-selection
```

### Install Dependencies

```bash
npm install
```

### Run the Development Server

```bash
npm run dev
```

### Build for Production

```bash
npm run build
```

## Model Evaluation

The model is evaluated using:

- Accuracy
- Precision
- Recall
- F1 Score
- Confusion Matrix

Performance metrics are displayed within the application.

##  Use Cases

- HR Candidate Screening
- Recruitment Automation
- Talent Evaluation
- Educational Projects
- Machine Learning Demonstrations

## Key Highlights

- Fully client-side ML inference
- No backend or database required
- Fast and lightweight deployment
- Easy to customize and extend
- Interactive visual analytics

## License

This project is intended for educational and learning purposes.

## Author

Developed as a Machine Learning and Web Development project to demonstrate applicant selection prediction using Random Forest Classification.
