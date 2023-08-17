Creating a user authentication service in Python involves handling user registration, login, and session management. Below, I'll provide a basic example of how you can implement a user authentication service using Flask, a popular web framework in Python. We'll use Flask's built-in session management for simplicity.

1. **Install Flask**:
   Make sure you have Flask installed. You can install it using `pip`:
   ```
   pip install Flask
   ```

2. **Create a Flask App**:
   Create a Python file (e.g., `app.py`) and set up your Flask app.

```python
from flask import Flask, render_template, request, redirect, session, url_for
import hashlib

app = Flask(__name__)
app.secret_key = "your_secret_key"  # Change this to a random secret key

# Simulated database of users (replace this with a real database)
users = {
    "user1": {
        "password": hashlib.sha256("password1".encode()).hexdigest()
    },
    "user2": {
        "password": hashlib.sha256("password2".encode()).hexdigest()
    }
}

# Helper function to hash passwords
def hash_password(password):
    return hashlib.sha256(password.encode()).hexdigest()

# Route for user registration
@app.route('/register', methods=['GET', 'POST'])
def register():
    if request.method == 'POST':
        username = request.form['username']
        password = request.form['password']
        users[username] = {
            "password": hash_password(password)
        }
        return redirect(url_for('login'))
    return render_template('register.html')

# Route for user login
@app.route('/login', methods=['GET', 'POST'])
def login():
    if request.method == 'POST':
        username = request.form['username']
        password = request.form['password']
        if username in users and users[username]["password"] == hash_password(password):
            session['username'] = username
            return redirect(url_for('profile'))
        else:
            return "Invalid credentials"
    return render_template('login.html')

# Route for user profile
@app.route('/profile')
def profile():
    if 'username' in session:
        return f"Welcome, {session['username']}!"
    return "You are not logged in."

# Route for user logout
@app.route('/logout')
def logout():
    session.pop('username', None)
    return redirect(url_for('login'))

if __name__ == '__main__':
    app.run(debug=True)
```

3. **HTML Templates**:
   Create two HTML templates: `register.html` and `login.html` in a folder called `templates`. These templates will be rendered by Flask.

`register.html`:
```html
<!DOCTYPE html>
<html>
<head>
    <title>User Registration</title>
</head>
<body>
    <h1>Register</h1>
    <form method="POST">
        <input type="text" name="username" placeholder="Username" required><br>
        <input type="password" name="password" placeholder="Password" required><br>
        <input type="submit" value="Register">
    </form>
</body>
</html>
```

`login.html`:
```html
<!DOCTYPE html>
<html>
<head>
    <title>User Login</title>
</head>
<body>
    <h1>Login</h1>
    <form method="POST">
        <input type="text" name="username" placeholder="Username" required><br>
        <input type="password" name="password" placeholder="Password" required><br>
        <input type="submit" value="Login">
    </form>
</body>
</html>
```

4. **Run the App**:
   Run your Flask app using the command:
   ```
   python app.py
   ```

Now, when you access `http://127.0.0.1:5000/register`, you can register new users. After registration, you can log in using `http://127.0.0.1:5000/login`, and upon successful login, you'll be redirected to your profile page. Don't use this example as-is for a production environment; it's a simplified demonstration. In a real-world scenario, you would need to integrate proper security practices, such as password hashing, user data validation, and database management.
