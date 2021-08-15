# X CRM
A djagno & vue.js application to manage customer.

:exclamation: **Don't use it in production!!!** :exclamation:

## Setup
#### Backend
```bash
cd backend
python3 -m venv venv
source venv/bin/activate
pip install -r requirements.txt
python manage.py migrate
python manage.py runserver
```
#### Frontend
```bash
cd frontend
npm install
# Compiles and hot-reloads for development
npm run serve
# Compiles and minifies for production
npm run build
```
