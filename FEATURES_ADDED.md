# New Features Added to E-Health Portal
## Features Overview

### 1. AI-Based Appointment Prediction
- **Location**: `hospital/ml_utils.py`
- **Description**: Uses a Random Forest model to predict the best appointment slots from recent history.
- **Features**:
  - Learns from patient load, doctor availability, and temporal patterns.
  - Returns top-ranked time slots with confidence scores.
  - Can auto-train from completed appointments or via a manual trigger.
- **API Endpoints**:
  - `POST /api/predict-slots` - Get ML-based slot recommendations
  - `POST /api/train-model` - Manually train the ML model
- **Usage**: Call the API with hospital_id, doctor_id, department_id, and target_date

### 2. Analytics Dashboard
- **Location**: `hospital/views.py` - `analytics_dashboard()`
- **Description**: Comprehensive analytics dashboard with Chart.js integration
- **Features**:
  - Daily appointment statistics (completed, pending, cancelled)
  - Doctor activity tracking
  - Hospital-wise statistics
  - Interactive charts and graphs (Chart.js)
- **URL**: `/analytics`
- **Access**: Hospital Admin only

### 3. Prescription OCR Uploads
- **Location**: `patient/ocr_utils.py`, `patient/views.py`
- **Description**: Upload prescription images and extract medicine names using OCR
- **Features**:
  - Image preprocessing for better OCR accuracy
  - Automatic text extraction from prescription images
  - Medicine name extraction and storage
  - Links prescriptions to appointments
- **URLs**:
  - `/patient/prescriptions` - Upload and view prescriptions
  - `/patient/prescriptions/<id>` - View prescription details
  - `/api/process-prescription-ocr` - Process OCR via API
- **Requirements**: Tesseract OCR must be installed on the system

### 4. Automated Notifications
- **Location**: `hospital/notification_utils.py`
- **Description**: Automated email and SMS notifications for appointments
- **Features**:
  - Appointment reminders (24 hours before)
  - Appointment confirmations
  - Appointment cancellations
  - Follow-up reminders
  - Email and SMS support via Twilio
- **Notification Types**:
  - APPOINTMENT_REMINDER
  - APPOINTMENT_CONFIRMED
  - APPOINTMENT_CANCELLED
  - PRESCRIPTION_READY
  - FOLLOW_UP
- **URLs**:
  - `/notifications` - View all notifications
  - `/api/appointments/<id>/reminder` - Send reminder manually
  - `/api/notifications/<id>/read` - Mark as read

- **JWT Authentication**: `userSystem/jwt_views.py`
- **New User Roles**: Pharmacist, Lab Technician
- **Features**:
  - JWT-based API authentication
  - Token refresh mechanism
  - Secure API endpoints
  - Role-based access control
- **API Endpoints**:
  - `POST /api/jwt/login` - Get JWT tokens
  - `POST /api/jwt/logout` - Logout and blacklist token
  - `POST /api/jwt/refresh` - Refresh access token
  - `GET /api/jwt/profile` - Get user profile (authenticated)

## New Dependencies

All new dependencies have been added to `requirements.txt`:

- **ML Libraries**: scikit-learn, pandas, numpy
- **OCR**: pytesseract, opencv-python
- **JWT**: djangorestframework, djangorestframework-simplejwt
- **SMS**: twilio
- **Utilities**: python-dateutil, celery, redis

## Configuration Required

### 1. Tesseract OCR
- **Windows**: Download from https://github.com/UB-Mannheim/tesseract/wiki
- **Linux**: `sudo apt-get install tesseract-ocr`
- **Mac**: `brew install tesseract`
- Update path in `settings.py`: `TESSERACT_CMD`
  ```python
  TESSERACT_CMD = r'C:\Program Files\Tesseract-OCR\tesseract.exe'
  ```

### 2. Twilio (for SMS)
Update in `settings.py`:
```python
TWILIO_ACCOUNT_SID = 'your_account_sid'
TWILIO_AUTH_TOKEN = 'your_auth_token'
TWILIO_PHONE_NUMBER = 'your_twilio_number'
```

### 3. Email Settings
Already configured in `settings.py` - update with your email credentials

### 4. ML Model Path
The ML model will be saved automatically at: `ml_models/appointment_predictor.pkl`

## Database Models Added

### 1. Prescription Model (`patient/models.py`)
- Stores prescription images
- OCR extracted text
- Extracted medicine names
- Links to appointments

### 2. AppointmentAnalytics Model (`patient/models.py`)
- Tracks daily appointment statistics
- Hospital and doctor-wise analytics
- Used for ML training and reporting

### 3. Notification Model (`hospital/models.py`)
- Stores all notifications
- Tracks email/SMS delivery status
- Links to appointments and users

## Setup Instructions

1. **Install Dependencies**:
   ```bash
   pip install -r requirements.txt
   ```

2. **Install Tesseract OCR** (for prescription OCR):
   - Follow platform-specific installation instructions
   - Update `TESSERACT_CMD` in settings.py

3. **Configure Twilio** (optional, for SMS):
   - Sign up at https://www.twilio.com
   - Get Account SID, Auth Token, and Phone Number
   - Update settings.py

4. **Run Migrations**:
   ```bash
   python manage.py makemigrations
   python manage.py migrate
   ```

5. **Train ML Model** (optional):
   - Visit `/api/train-model` as Hospital Admin
   - Or let it train automatically with historical data

6. **Start Server**:
   ```bash
   python manage.py runserver
   ```

## API Usage Examples

### JWT Login
```bash
POST /api/jwt/login
{
    "username": "user@example.com",
    "password": "password123"
}
```

### Get Appointment Predictions
```bash
POST /api/predict-slots
{
    "hospital_id": 1,
    "doctor_id": 1,
    "department_id": 1,
    "target_date": "2025-01-15"
}
```

### Upload Prescription
```bash
POST /patient/prescriptions
Form Data:
- prescription_image: [file]
- appointment_id: [optional]
```

## Frontend Integration

### Chart.js for Analytics
The analytics dashboard uses Chart.js. Include in your template:
```html
<script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
```

### Example Chart Usage
```javascript
const ctx = document.getElementById('appointmentsChart').getContext('2d');
const chart = new Chart(ctx, {
    type: 'line',
    data: {
        labels: dates,
        datasets: [{
            label: 'Appointments',
            data: appointmentCounts
        }]
    }
});
```

## Security Notes

- JWT tokens expire after 1 hour (configurable)
- Refresh tokens expire after 24 hours
- All API endpoints require authentication except login/register
- SMS and Email credentials should be stored securely (use environment variables in production)

##  User Roles

- **HOSADMIN**: Hospital Admin (existing)
- **DOCTOR**: Doctor (existing)
- **PATIENT**: Patient (existing)
- **PHARMACIST**: Pharmacist (new)
- **LABTECH**: Lab Technician (new)

## Troubleshooting

### OCR Not Working
- Ensure Tesseract is installed and path is correct
- Check image quality (higher resolution = better results)
- Verify TESSERACT_CMD path in settings.py
  (Windows default: `C:\Program Files\Tesseract-OCR\tesseract.exe`)

### ML Predictions Not Accurate
- Train model with more historical data (minimum 10+ appointments)
- Visit `/api/train-model` to retrain
- Check that appointment data exists in database

### SMS Not Sending
- Verify Twilio credentials in settings.py
- Check phone number format (include country code)
- Ensure Twilio account has sufficient credits

### JWT Authentication Failing
- Verify token hasn't expired
- Check that user is active
- Ensure correct username/password

## Additional Resources

- [Chart.js Documentation](https://www.chartjs.org/docs/)
- [Tesseract OCR](https://github.com/tesseract-ocr/tesseract)
- [Twilio API](https://www.twilio.com/docs)
- [Django REST Framework JWT](https://django-rest-framework-simplejwt.readthedocs.io/)



---


