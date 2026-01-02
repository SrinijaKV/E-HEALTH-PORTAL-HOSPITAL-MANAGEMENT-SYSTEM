# E-Health Portal - Setup and Running Instructions

## Step-by-Step Setup Guide

### Step 1: Navigate to Project Directory
```bash
cd "C:\Users\abhip\Desktop\16 Sep 2025\E-Health Portal – Hospital Management System"
```

### Step 2: Create and Activate a Virtual Environment
```bash
python -m venv .venv  (optional)
.venv\Scripts\activate        # Windows PowerShell

### Step 3: Install Dependencies
```bash
pip install --upgrade pip
pip install -r requirements.txt
```

### Step 3.5: Install and Configure Tesseract OCR (for prescription processing)
- Download the Windows build from https://github.com/UB-Mannheim/tesseract/wiki and install it to `C:\Program Files\Tesseract-OCR`.
- Confirm the OCR path in `ehealthPortal/settings.py`:
```python
TESSERACT_CMD = r'C:\Program Files\Tesseract-OCR\tesseract.exe'
```
- Optionally add `C:\Program Files\Tesseract-OCR` to your system `PATH` so other utilities can use Tesseract.

**Note:** If you encounter issues with `mysqlclient` on Windows (since the project uses SQLite), you can skip it or install it separately. The project works with SQLite by default.

### Step 4: Configure Settings

Open `ehealthPortal/settings.py` and update the following:

#### 4.1: Set SECRET_KEY
Replace line 22:
```python
SECRET_KEY = '#ADD_YOURS'
```
With a secure secret key. You can generate one using:
```python
# Run in Python shell:
from django.core.management.utils import get_random_secret_key
print(get_random_secret_key())
```

Or use this temporary one for development:
```python
SECRET_KEY = 'django-insecure-your-secret-key-here-change-in-production'
```

#### 4.2: Configure Email Settings (Optional but Recommended)
Update lines 135-137 in `settings.py`:
```python
EMAIL_FROM = 'your-email@gmail.com'
EMAIL_HOST_USER = 'your-email@gmail.com'
EMAIL_HOST_PASSWORD = 'your-app-password'  # Use App Password, not regular password
```

**Note:** For Gmail, you need to:
1. Enable 2-Factor Authentication
2. Generate an App Password: https://myaccount.google.com/apppasswords
3. Use the App Password in `EMAIL_HOST_PASSWORD`

**If you skip email configuration:**
- The app will run, but email verification and password reset features won't work
- Users can still register and login (you may need to manually activate accounts)

### Step 5: Create Media Directory (if it does not exist)
```bash
mkdir media
```

### Step 6: Run Database Migrations
```bash
python manage.py makemigrations

python manage.py migrate
```

### Step 7: Create Superuser (Optional - for Django Admin)
```bash
python manage.py createsuperuser
```
Follow the prompts to create an admin account.

### Step 8: Collect Static Files (Optional - for production)
```bash
python manage.py collectstatic
```
For development, this is usually not necessary as Django serves static files automatically.

### Step 9: Run the Development Server
```bash
python manage.py runserver
```

The server will start at: **http://127.0.0.1:8000/**

---

## Quick Start (Minimal Setup)

Short on time? Do the following:

1. Generate and set `SECRET_KEY` in `settings.py`.
2. Create the `media` directory.
3. Run `python manage.py migrate`.
4. Launch with `python manage.py runserver`.

Email/SMS/OCR/ML extras can be configured later.

## Accessing the Application

### Main Pages:
- **Home Page**: http://127.0.0.1:8000/
- **Login**: http://127.0.0.1:8000/login
- **Register**: http://127.0.0.1:8000/register
- **Admin Panel**: http://127.0.0.1:8000/admin (if superuser created)

### User Dashboards:
- **Patient Home**: http://127.0.0.1:8000/patient-home
- **Doctor Home**: http://127.0.0.1:8000/home-doctor
- **Hospital Admin Home**: http://127.0.0.1:8000/home-hospital

---

## Troubleshooting

### Issue: `mysqlclient` installation fails on Windows
**Solution:** The project defaults to SQLite, so you can safely comment out or remove `mysqlclient` from `requirements.txt`. Install it only if you switch to MySQL/MariaDB.

### Issue: Email not sending
**Solution:** 
- Check email settings in `settings.py`
- For Gmail, use App Password, not regular password
- Test email settings using Django shell:
```python
python manage.py shell
>>> from django.core.mail import send_mail
>>> send_mail('Test', 'Test message', 'from@email.com', ['to@email.com'])
```

### Issue: Static files not loading
**Solution:** 
- Make sure `DEBUG = True` in settings.py
- Run `python manage.py collectstatic` if needed
- Check browser console for 404 errors

### Issue: Media files not accessible
**Solution:**
- Ensure the `media` folder exists in the project root
- Check `MEDIA_URL` and `MEDIA_ROOT` in `settings.py`
- Confirm `urlpatterns` serve media during development (already configured in `ehealthPortal/urls.py`)

### Issue: Migration errors
**Solution:**
```bash
# Delete existing migrations (backup first!)
# Then recreate:
python manage.py makemigrations
python manage.py migrate
```

---

## Testing the Application

1. **Register a Patient:**
   - Go to http://127.0.0.1:8000/register
   - Fill in details, select "Patient" as user type
   - Check email for verification link (if email configured)

2. **Register a Hospital Admin:**
   - Register with "Hospital Admin" user type
   - Login and add hospitals

3. **Register a Doctor:**
   - Register with "Doctor" user type
   - Hospital admin can add doctors to hospitals

4. **Book Appointment:**
   - Login as Patient
   - Go to "New Appointment"
   - Follow the booking flow

---

## Production Deployment Notes

Before deploying to production:

1. Set `DEBUG = False` in settings.py
2. Set proper `SECRET_KEY` (never commit to version control)
3. Configure proper `ALLOWED_HOSTS`
4. Set up proper database (PostgreSQL recommended)
5. Configure proper email backend
6. Set up static file serving (nginx/Apache)
7. Use environment variables for sensitive data
8. Enable HTTPS
9. Set up proper logging
10. Configure backup strategy

---

## Additional Commands

```bash
# Create a new migration after model changes
python manage.py makemigrations

# Apply migrations
python manage.py migrate

# Open Django shell
python manage.py shell

# Check for issues
python manage.py check

# Create superuser
python manage.py createsuperuser

# Run tests (if any)
python manage.py test
```

---

## Project Structure Summary

```
E-Health Portal – Hospital Management System/
├── manage.py
├── db.sqlite3
├── requirements.txt
├── media/                    # User uploads (create this)
├── ehealthPortal/           # Main project settings
├── home/                    # Landing page app
├── userSystem/              # Authentication app
├── patient/                 # Patient app
├── doctor/                  # Doctor app
└── hospital/                # Hospital admin app
```

---

## Support

If you encounter any issues:
1. Check Django version: `python -m django --version`
2. Check Python version: `python --version`
3. Verify all dependencies are installed: `pip list`
4. Check for error messages in terminal
5. Review Django logs for detailed error information

