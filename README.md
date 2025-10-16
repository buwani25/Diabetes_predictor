# FDM Diabetes Risk Prediction System

## 📋 Project Overview

The FDM Diabetes Risk Prediction System is a comprehensive web application that uses machine learning to predict diabetes risk with gender-specific models. The system features dual Random Forest models - one general model for all users and a specialized model for women that includes gestational history factors.

### 🎯 Key Features

- **Dual Model Architecture**: Gender-specific predictions for enhanced accuracy
  - General Model: 97.0% ROC-AUC (excludes gestational features)
  - Women-Specific Model: 97.1% ROC-AUC (includes gestational history)
- **Real-time Predictions**: Instant diabetes risk assessment
- **User Authentication**: Secure JWT-based authentication system
- **Admin Dashboard**: User management and system monitoring
- **Responsive UI**: Modern React frontend with Tailwind CSS
- **API Documentation**: Interactive Swagger/OpenAPI documentation

### 🏗️ Architecture

```
Frontend (React + Vite)  ←→  Backend (FastAPI)  ←→  Database (MongoDB Atlas)
                                      ↓
                            ML Models (Random Forest)
```

---

## 🚀 Quick Start

1. Clone the repository
2. Set up the backend (FastAPI + MongoDB)
3. Set up the frontend (React + Vite)
4. Run both servers

---

## 🛠️ Backend Setup (FastAPI + MongoDB)

### Prerequisites
- **Python 3.8+**
- **MongoDB Atlas account** (or local MongoDB)

### 1. Create Virtual Environment

```bash
cd services
python -m venv .venv
```

### 2. Activate Virtual Environment

**Windows (PowerShell):**
```bash
.venv\Scripts\Activate.ps1
```

**Windows (Command Prompt):**
```bash
.venv\Scripts\activate.bat
```

**Mac/Linux:**
```bash
source .venv/bin/activate
```

### 3. Install Dependencies

```bash
pip install --upgrade pip
pip install -r requirements.txt
```

### 4. Environment Configuration

1. **Create `.env` file** in the `services` directory:
   ```bash
   cp .env.example .env
   ```

2. **Configure your `.env` file:**
   ```env
   # MongoDB Configuration
  
   # JWT Configuration
   SECRET_KEY=your-super-secret-jwt-key-here
   ALGORITHM=HS256
   ACCESS_TOKEN_EXPIRE_MINUTES=30
   
   # Application Settings
   DEBUG=True
   ENVIRONMENT=development
   ```

3. **Generate JWT Secret Key:**
   ```bash
   python -c "import secrets; print(secrets.token_hex(32))"
   ```

### 5. Start Backend Server

```bash
# Make sure you're in the services directory
cd services

# Start the FastAPI server
uvicorn main:app --reload --host 0.0.0.0 --port 8000
```

**Alternative start methods:**
```bash
# Using the batch file (Windows)
start_server.bat

# Using Python directly
python main.py
```

### 6. Verify Backend is Running

- **API Documentation**: [http://localhost:8000/docs](http://localhost:8000/docs)
- **Health Check**: [http://localhost:8000/health](http://localhost:8000/health)
- **Model Status**: [http://localhost:8000/api/model-status](http://localhost:8000/api/model-status)

---

## 🌐 Frontend Setup (React + Vite)

### Prerequisites
- **Node.js 16+**
- **npm** or **yarn**

### 1. Install Dependencies

```bash
cd frontend
npm install
```

### 2. Start Development Server

```bash
npm run dev
```

### 3. Build for Production

```bash
npm run build
npm run preview
```

### 4. Verify Frontend is Running

- **Development Server**: [http://localhost:5173](http://localhost:5173)
- **Production Preview**: [http://localhost:4173](http://localhost:4173)

---

## 🤖 Machine Learning Models

### Model Training

The system uses pre-trained Random Forest models located in `services/models/`:

- `diabetes_general_model.pkl` - General model for all users
- `diabetes_women_model.pkl` - Women-specific model with gestational features
- `general_model_features.json` - Feature list for general model
- `women_model_features.json` - Feature list for women model

### Retraining Models

To retrain the models with new data:

```bash
cd services/agents
jupyter notebook enhanced_eda_preprocess.ipynb
```

**Or run the Python script:**
```bash
python enhanced_eda_preprocess.py
```

### Model Performance

| Model | ROC-AUC | Accuracy | Precision | Recall |
|-------|---------|----------|-----------|--------|
| General | 97.0% | 95.2% | 94.8% | 95.6% |
| Women-Specific | 97.1% | 95.4% | 95.0% | 95.8% |

---

## 🔧 API Endpoints

### Authentication
- `POST /auth/signup` - User registration
- `POST /auth/login` - User login
- `GET /auth/me` - Get current user
- `POST /auth/logout` - User logout

### Predictions
- `POST /api/predict-public` - Public diabetes risk prediction
- `POST /api/predict` - Authenticated diabetes risk prediction
- `GET /api/model-status` - Check model availability

### Admin
- `GET /admin/users` - List all users (admin only)
- `DELETE /admin/users/{user_id}` - Delete user (admin only)

### Health & Monitoring
- `GET /health` - API health check
- `GET /api/stats` - System statistics

---

## 🧪 Testing the System

### 1. Test Model Predictions

**Test General Model (Male):**
```bash
curl -X POST "http://localhost:8000/api/predict-public" \
  -H "Content-Type: application/json" \
  -d '{
    "age": 45,
    "gender": "male",
    "height": 180,
    "weight": 80,
    "familyHistory": "yes",
    "physicalActivity": "low",
    "smoking": "yes",
    "bloodPressure": "high",
    "cholesterol": "high"
  }'
```

**Test Women-Specific Model:**
```bash
curl -X POST "http://localhost:8000/api/predict-public" \
  -H "Content-Type: application/json" \
  -d '{
    "age": 35,
    "gender": "female",
    "height": 165,
    "weight": 65,
    "familyHistory": "no",
    "physicalActivity": "high",
    "smoking": "no",
    "bloodPressure": "normal",
    "cholesterol": "normal",
    "gestationalHistory": true
  }'
```

### 2. Test Authentication

**Sign Up:**
```bash
curl -X POST "http://localhost:8000/auth/signup" \
  -H "Content-Type: application/json" \
  -d '{
    "email": "test@example.com",
    "password": "password123",
    "full_name": "Test User"
  }'
```

**Login:**
```bash
curl -X POST "http://localhost:8000/auth/login" \
  -H "Content-Type: application/x-www-form-urlencoded" \
  -d "username=test@example.com&password=password123"
```

---

## 📁 Project Structure

```
DiabetesPredictor/
├── frontend/                    # React frontend application
│   ├── src/
│   │   ├── components/         # Reusable UI components
│   │   ├── pages/             # Page components
│   │   ├── contexts/          # React contexts (Auth, etc.)
│   │   ├── services/          # API service functions
│   │   └── admin/             # Admin-specific components
│   ├── public/                # Static assets
│   └── package.json           # Frontend dependencies
│
├── services/                   # FastAPI backend application
│   ├── app/
│   │   ├── models/            # Database models
│   │   ├── routes/            # API route handlers
│   │   ├── schemas/           # Pydantic schemas
│   │   ├── services/          # Business logic services
│   │   └── utils/             # Utility functions
│   ├── agents/                # ML training scripts
│   │   ├── enhanced_eda_preprocess.ipynb  # Model training notebook
│   │   └── enhanced_eda_preprocess.py     # Model training script
│   ├── models/                # Trained ML models
│   │   ├── diabetes_general_model.pkl     # General Random Forest model
│   │   ├── diabetes_women_model.pkl       # Women-specific model
│   │   └── *.json             # Feature configurations
│   ├── data/                  # Dataset storage
│   │   ├── raw/               # Original datasets
│   │   ├── processed/         # Preprocessed data
│   │   └── processed_enhanced/# Enhanced preprocessing results
│   ├── requirements.txt       # Python dependencies
│   ├── main.py               # FastAPI application entry point
│   └── .env.example          # Environment template
│
├── README.md                 # This file
└── IMPLEMENTATION_GUIDE.md   # Detailed implementation notes
```

---

## 🔍 Troubleshooting

### Common Issues

#### 1. Backend Issues

**MongoDB Connection Error:**
```bash
# Check your MongoDB Atlas connection string
# Ensure IP whitelist includes your current IP
# Verify username/password in connection string
```

**Model Loading Error:**
```bash
# Verify model files exist in services/models/
ls services/models/*.pkl

# Retrain models if necessary
cd services/agents
python enhanced_eda_preprocess.py
```

**Port Already in Use:**
```bash
# Kill existing process on port 8000
netstat -ano | findstr :8000
taskkill /PID <process_id> /F

# Or use a different port
uvicorn main:app --reload --port 8001
```

#### 2. Frontend Issues

**Node Module Issues:**
```bash
# Clear cache and reinstall
rm -rf node_modules package-lock.json
npm install
```

**Build Errors:**
```bash
# Check for TypeScript/ESLint errors
npm run build
```

#### 3. CORS Issues

If you encounter CORS errors, verify the frontend URL is in the backend CORS origins:
```python
# In services/app/main.py
app.add_middleware(
    CORSMiddleware,
    allow_origins=["http://localhost:5173"],  # Add your frontend URL
    allow_credentials=True,
    allow_methods=["*"],
    allow_headers=["*"],
)
```

---

## 🚀 Deployment

### Backend Deployment (Production)

1. **Set production environment variables**
2. **Use production MongoDB cluster**
3. **Deploy with WSGI server:**
   ```bash
   gunicorn -w 4 -k uvicorn.workers.UvicornWorker main:app
   ```

### Frontend Deployment

1. **Build for production:**
   ```bash
   npm run build
   ```
2. **Deploy `dist` folder to your hosting service**

---

## 🤝 Contributing

1. Fork the repository
2. Create a feature branch: `git checkout -b feature-name`
3. Commit changes: `git commit -am 'Add feature'`
4. Push to branch: `git push origin feature-name`
5. Submit a pull request

---

## 👥 Team

- **Developers**: [SSaabir],[duwaragie-work],[buwani25],[sakuni-2002]
- **Project**: FDM Diabetes Risk Prediction System
- **Year**: 2025
---

**🎉 Happy Predicting! 🎉**
