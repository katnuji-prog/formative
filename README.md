# User Profile Flask App

Flask application that lets people register, view, and update user profiles backed by SQLite (via SQLAlchemy) with WTForms validation.

## Local Setup
- Create and activate a virtual environment, then install dependencies:
  ```
  python -m venv .venv
  .\.venv\Scripts\activate
  pip install -r requirements.txt
  ```
- Set environment variables (use your own secret in production):
  ```
  set FLASK_APP=app:create_app
  set FLASK_ENV=development
  set FLASK_SECRET=dev-secret
  ```
- Initialize the SQLite database once:
  ```
  flask run --app app:create_app --debug
  ```
  SQLAlchemy creates `users.db` automatically the first time the app runs.

## Running
```
flask run --app app:create_app --debug
```
Visit http://127.0.0.1:5000 to register users, log in, and update profiles.

## Deployment (GitHub + Render)
1. **Push to GitHub**
   ```
   git init
   git add .
   git commit -m "Initial profile app"
   git remote add origin https://github.com/katnuji-prog/formative.git
   git push -u origin main
   ```
2. **Render Web Service**
   - Create a new Web Service from that GitHub repo.
   - Select a Python environment, set the build command to `pip install -r requirements.txt`.
- Set the start command to `gunicorn "app:create_app()"`.
   - Configure env vars: `FLASK_SECRET`, `DATABASE_URL` (defaults to SQLite but you can point to PostgreSQL) and any others you need.
   - Deploy; Render keeps the SQLite file on the persistent disk or, preferably, switch to a managed PostgreSQL database by setting `DATABASE_URL`.

## Project Structure
```
fummative/
├── app/
│   ├── __init__.py        # Application factory + extension setup
│   ├── forms.py
│   ├── models.py
│   ├── routes.py
│   ├── static/
│   │   └── style.css
│   └── templates/
│       ├── base.html
│       ├── edit.html
│       ├── login.html
│       ├── profile.html
│       ├── register.html
│       └── users.html
├── create_db.py
├── requirements.txt
└── README.md
```

## Notes
- The app uses Flask-Login to guard profile edits.
- Validation (presence, email format, password confirmation) is handled in WTForms; routes add additional duplication checks before writing to the DB.

