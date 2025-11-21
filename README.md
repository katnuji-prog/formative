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

2. **Render Web Service (recommended production setup)**

- Build command: `pip install -r requirements.txt`
- Start command: `gunicorn "app:create_app()" -b 0.0.0.0:$PORT`

Environment variables to set in the Render Web Service settings (Dashboard → Your Service → Environment):

- **FLASK_SECRET** (required): a long random secret used for `SECRET_KEY`. Example: use the value in your `.env` file or generate a secure token. Mark this value as a secret in Render.
- **DATABASE_URL** (recommended): point to a managed PostgreSQL database for production. Example: `postgres://username:password@host:5432/databasename`.
  - If `DATABASE_URL` is not set, the app falls back to SQLite and will create `users.db` in the working directory. Using PostgreSQL is more robust for production.

How to add a managed Postgres on Render and wire `DATABASE_URL`:

1. Create a new Managed Database (Postgres) in Render: Dashboard → Databases → New Database.
2. After the DB is created, copy the provided connection string (Render calls this `DATABASE_URL`).
3. Open your Web Service settings → Environment, and add an environment variable:
  - Name: `DATABASE_URL`
  - Value: (the connection string from the database)
  - Mark as Secret
4. Also add `FLASK_SECRET` as an environment variable and mark it secret.
5. Trigger a deploy/redeploy of the Web Service.

Notes when using SQLite on Render (not recommended for multi-instance or high-traffic production):

- If you choose to keep using SQLite, ensure the app can write the database file to a persistent and writable path. One way is to place the DB in the Flask `instance/` folder. The app will fall back to `sqlite:///users.db` if `DATABASE_URL` is not set, but file path and permissions may vary per host.
- Check Render service logs if deployment fails — common errors are permission issues or inability to create/open the SQLite file.

Local quick test (create DB locally before deploying):

```powershell
.\.venv\Scripts\activate
python create_db.py
# verify tables were created and users.db exists
``` 

Example environment file (do NOT commit `.env` — use `.env.example` as a template):

```
FLASK_SECRET=replace-with-a-64-char-secret
DATABASE_URL=postgres://username:password@host:5432/databasename
```


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

