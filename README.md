# E-Health Portal – Hospital Management

Modern multi-role hospital management system built with Django. Patients can schedule appointments and upload prescriptions, doctors manage their caseload, and hospital admins track operations with analytics, ML predictions, and automated notifications.

## Tech Stack
- Django 4.1, Django REST Framework, SimpleJWT
- PostgreSQL / SQLite (default)
- Bootstrap 5, jQuery, Chart.js
- Tesseract OCR, scikit-learn, pandas, numpy
- Twilio SMS, SMTP email

## Key Features
1. **End-to-end appointment flow** – patient self-service booking, doctor scheduling, hospital oversight.
2. **AI slot recommendations** – Random Forest model suggests optimal appointment times based on historical load.
3. **Analytics dashboard** – Chart.js visualizations for daily appointments, doctor activity, hospital stats.
4. **Prescription OCR** – upload scripts, auto-extract medicine names, link to appointments.
5. **Notification engine** – email/SMS confirmations, reminders, cancellations, follow-ups.
6. **Secure access** – JWT APIs plus browser sessions; hospital admin, doctor, patient, pharmacist, and lab tech roles.
7. **User experience niceties** – responsive UI, progress indicators, AJAX interactions, profile management.

## Getting Started
1. Clone or download the project.
2. Follow `SETUP_INSTRUCTIONS.md` for environment prep, secrets, and migrations.
3. Review `FEATURES_ADDED.md` for advanced capabilities (ML, analytics, OCR, notifications, JWT).

## Configuration Checklist
- Generate and set `SECRET_KEY` in `ehealthPortal/settings.py`.
- Provide email credentials (app password recommended) to enable verification/reset flows.
- Configure Twilio keys if SMS reminders are required.
- Install Tesseract OCR and confirm `TESSERACT_CMD`.
- Run `python manage.py migrate` then `python manage.py runserver`.

## Contributing
Pull requests are welcome! Please include:
- Descriptive commit messages.
- Tests or manual verification notes.
- Updates to docs when features/configuration change.

## Support
If something breaks:
1. Inspect terminal logs for stack traces.
2. Run `python manage.py check` for configuration issues.
3. Confirm dependencies with `pip list`.
4. Open an issue or reach out with details (OS, Python version, steps to reproduce).
