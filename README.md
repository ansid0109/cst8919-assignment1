# CST8919 Assignment 1

**Student Name**: Anoop Sidhu
**Student ID**: 040984994
**Course**: CST8919 DevOps - Security and Compliance
**Semester**: Summer 2026

---
## Demo Video

🎥 [Watch Demo Video](https://youtu.be/75pZk6TIFUw)

## Description

This is a small Flask application that uses Auth0 for login, profile access, and logout. Instructions for the complete application can be found [here](https://auth0.com/docs/quickstart/webapp/python).

## KQL Query

```
AppServiceConsoleLogs
| where TimeGenerated > ago(1h)
| where ResultDescription has "Protected route accessed by"
| extend UserId = extract(@"user_id=(\S+)", 1, ResultDescription)
| extend Email = extract(@"email=(\S+)", 1, ResultDescription)
| where isnotempty(UserId)
| summarize AccessCount = count(), Emails = make_set(Email) by UserId, bin(TimeGenerated, 15m)
| where AccessCount > 10
| order by AccessCount desc
```

## Prerequisites

- Python 3.10 or newer
- An Auth0 account and application

## Setup

1. Create and activate a virtual environment:

```bash
python3 -m venv venv
source venv/bin/activate
```

2. Install dependencies:

```bash
pip install -r requirements.txt
```

3. Create a `.env` file in the project root with your Auth0 values.

## Auth0 Configuration

Create a new Auth0 application and use the following settings:

- Application type: Regular Web Application
- Allowed Callback URLs: `http://localhost:5000/callback`
- Allowed Logout URLs: `http://localhost:5000/`
- Allowed Web Origins: `http://localhost:5000`

Copy the following values from your Auth0 application into `.env`:

- Domain
- Client ID
- Client Secret
- Secret

The app also supports an optional `AUTH0_AUDIENCE` value if you want to request an API audience.

## Environment Variables

Example `.env` file:

```env
AUTH0_DOMAIN=your-tenant.us.auth0.com
AUTH0_CLIENT_ID=your-client-id
AUTH0_CLIENT_SECRET=your-client-secret
AUTH0_SECRET=replace-with-a-long-random-string
AUTH0_REDIRECT_URI=http://localhost:5000/callback
```

If you prefer not to use a `.env` file, export the same variables in your shell before starting the app.

## Run Locally

Start the Flask app with:

```bash
python app.py
```

Then open `http://localhost:5000` in your browser.

## App Routes

- `/` - Home page
- `/login` - Redirects to Auth0 login
- `/callback` - Handles the Auth0 login callback
- `/profile` - Protected user profile page
- `/protected` - Protected text response
- `/logout` - Logs the user out through Auth0

## Notes

- The app uses `python-dotenv` to load environment variables from `.env`.
- Session cookies are configured for local development in `app.py`; set `SESSION_COOKIE_SECURE=True` when deploying behind HTTPS.
