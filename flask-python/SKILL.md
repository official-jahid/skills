# Flask Development for Python

Skill for Flask web application development with authentication and API endpoints.

## Installation

```bash
pip install Flask
```

## Basic Application Structure

```python
from flask import Flask, jsonify, render_template, request, redirect, session

app = Flask(__name__, template_folder='templates', static_folder='static')
```

## Route Handlers

### GET Routes
```python
@app.get('/')
def home():
    return render_template('page.html')

@app.get('/protected')
def protected():
    if session.get('user'):
        return render_template('dashboard.html')
    return redirect('/')
```

### POST Routes with JSON
```python
@app.post('/api/endpoint')
def apiEndpoint():
    data = request.get_json()
    if request.method == "POST":
        # Process data
        return jsonify(status=200, message="Success")
    return jsonify(status=400, message="Bad Request")
```

## Authentication Check Pattern

```python
@app.post('/auth-check')
def authCheck():
    if not user:
        return jsonify(status=302)  # Not authenticated
    return jsonify(status=200)  # Authenticated
```

## Session Management

```python
# Store user data
session['username'] = username
user['data'] = value

# Check authentication
if keyauthapp.user_data.username:
    # User is logged in
```

## JSON Responses

```python
return jsonify(
    status=200,
    message="Success",
    data=value
)
```

## Threading for Background Tasks

```python
import threading

def run_flask():
    app.run(debug=False, host='0.0.0.0', port=4070, threaded=True)

if __name__ == "__main__":
    flask_thread = threading.Thread(target=run_flask)
    monitor_thread = threading.Thread(target=process_monitor)
    flask_thread.start()
    monitor_thread.start()
```

## Template Rendering

```python
# Pass variables to templates
return render_template('dashboard.html', user=user, version=version)
```

## CORS Setup (if needed)

```bash
pip install flask-cors
```

```python
from flask_cors import CORS
CORS(app)
```

## Production Server

```bash
pip install waitress

from waitress import serve
serve(app, host='0.0.0.0', port=4070)
```

## Error Handling

```python
@app.errorhandler(404)
def not_found(e):
    return render_template('404.html'), 404

@app.errorhandler(500)
def server_error(e):
    return jsonify(status=500, message="Server error"), 500
```