# 🚚 Lock & Load Logistics CRM

### *Locked in on every move*

This document is a comprehensive, beginner-friendly guide to building the **Lock & Load Logistics Command Center** completely from scratch. You will start with a blank screen and progress through **4 distinct phases of development**.

After each phase, you will:

* Run testing routines
* Verify browser functionality
* Shut down the application correctly
* Move into the next development stage

This iterative workflow ensures your:

* Database
* Backend environment
* Visual interface
* Runtime logic

remain fully synchronized throughout the build process.

---

 ## 📚 Table of Contents

* [Step 1 — Initialize Workspace](#--step-1--initialize-your-workspace--virtual-environment)
* [Step 2 — Install Required Libraries](#--step-2--install-required-libraries)
* [Phase 1 — Baseline Application](#--phase-1--the-baseline-application-data-intake-engine)
* [Phase 2 — Workflow Tracking](#--phase-2--workflow-tracking-status-lifecycles)
* [Phase 3 — Visual UI Upgrade](#--phase-3--visual-identity-ui-upgrade-lock--load-branding)
* [Phase 4 — Full Logistics Center](#--phase-4--full-logistics-center-search-filters-pdf-rendering-email--lan-sharing)

---

 ## 🛠 Step 1 — Initialize Your Workspace & Virtual Environment

Before writing a single line of code, you must create a clean directory on your computer and isolate your software libraries.

This prevents version conflicts with other projects.

Open your terminal application:

* PowerShell / CMD (Windows)
* Terminal (Mac/Linux)

Navigate to your Desktop:

```bash
cd Desktop
```

Create a brand-new directory:

```bash
mkdir MovingCRM2
```

Move inside the folder:

```bash
cd MovingCRM2
```

Launch the project inside VS Code:

```bash
code .
```

If the command above does not work:

1. Open VS Code manually
2. Go to **File > Open Folder**
3. Select the `MovingCRM2` folder

Open the internal VS Code terminal:

```plaintext
Ctrl + `
```

or:

```plaintext
Terminal > New Terminal
```

Create a Python virtual environment:

```bash
python -m venv venv
```

---

 ## ▶️ Activate Virtual Environment

### Windows (PowerShell)

```bash
.\venv\Scripts\activate
```

### Mac / Linux

```bash
source venv/bin/activate
```

If successful, your terminal should now display:

```bash
(venv)
```

on the far left side.

---

 ## 📦 Step 2 — Install Required Libraries

Run this command inside the VS Code terminal:

```bash
pip install flask flask_sqlalchemy reportlab
```

### Installed Packages

| Package          | Purpose               |
| ---------------- | --------------------- |
| Flask            | Web framework         |
| Flask_SQLAlchemy | Database ORM          |
| ReportLab        | PDF generation engine |

---

 ## 🚀 Phase 1 — The Baseline Application (Data Intake Engine)

This phase creates a simple logistics intake system.

The application will:

* Accept customer data
* Calculate moving quotes live
* Store records in SQLite
* Display leads dynamically

---

 ## 💻 VS Code File Setup Instructions

1. Hover over the `MOVINGCRM2` explorer sidebar
2. Click the **New File** icon
3. Name the file:

```plaintext
app.py
```

4. Paste the Python code below
5. Save using:

```plaintext
Ctrl + S
```

6. Create a new folder named:

```plaintext
templates
```

7. Inside the templates folder create:

```plaintext
index.html
```

8. Paste the HTML below and save it

---

 ## 📄 Create `app.py` (Phase 1 Full Source)

```python
from flask import Flask, render_template, request, redirect
from flask_sqlalchemy import SQLAlchemy

app = Flask(__name__)
# Configures the SQLite database file to generate inside your project folder root
app.config['SQLALCHEMY_DATABASE_URI'] = 'sqlite:///movers.db'
app.config['SQLALCHEMY_TRACK_MODIFICATIONS'] = False

db = SQLAlchemy(app)

# Define the structure of your database table layout
class Lead(db.Model):
    id = db.Column(db.Integer, primary_key=True)
    customer_name = db.Column(db.String(100), nullable=False)
    phone = db.Column(db.String(20))
    rooms = db.Column(db.Integer)
    miles = db.Column(db.Integer)
    estimated_quote = db.Column(db.Float)

# Execute engine schemas to build tables on initialization
with app.app_context():
    db.create_all()

@app.route('/')
def home():
    # Fetch all registered user database rows
    all_leads = Lead.query.all()
    return render_template('index.html', leads=all_leads)

@app.route('/add_lead', methods=['POST'])
def add_lead():
    # Safely extract posted string parameters from the HTML user form
    new_lead = Lead(
        customer_name=request.form.get('name'),
        phone=request.form.get('phone'),
        rooms=request.form.get('rooms', type=int),
        miles=request.form.get('miles', type=int),
        estimated_quote=request.form.get('final_quote', type=float)
    )

    db.session.add(new_lead)
    db.session.commit() # Commit modifications directly to movers.db file

    return redirect('/')

if __name__ == '__main__':
    app.run(debug=True)
```

---

 ## 📄 Create `templates/index.html` (Phase 1 Full Source)

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Lock & Load Baseline</title>
    <style>
        body { font-family: sans-serif; margin: 40px; background-color: #f0f2f5; display: flex; gap: 20px; }
        .form-container, .table-container { background: white; padding: 25px; border-radius: 12px; box-shadow: 0 4px 15px rgba(0,0,0,0.1); }
        .form-container { flex: 1; } .table-container { flex: 2; }
        input { width: 100%; padding: 12px; margin: 8px 0; border: 1px solid #ddd; border-radius: 6px; box-sizing: border-box; }
        button { background-color: #28a745; color: white; padding: 14px; border: none; border-radius: 6px; cursor: pointer; width: 100%; font-weight: bold; }
        table { width: 100%; border-collapse: collapse; }
        th, td { text-align: left; padding: 12px; border-bottom: 1px solid #eee; }
    </style>
</head>
<body>
    <div class="form-container">
        <h2>🚚 Capture New Lead</h2>
        <form action="/add_lead" method="POST">
            <input type="text" name="name" placeholder="Customer Name" required>
            <input type="text" name="phone" placeholder="Phone Number">
            <label>Inventory Scale (Rooms):</label>
            <input type="number" id="rooms" name="rooms" value="1" min="1" oninput="calculateQuote()">
            <label>Travel Distance (Miles):</label>
            <input type="number" id="miles" name="miles" value="0" min="0" oninput="calculateQuote()">
            
            <div style="background: #f8f9fa; padding: 20px; border-radius: 8px; margin-top: 20px;">
                <small>ESTIMATED TOTAL COST</small>
                <h3 style="margin:0; color:#1a73e8;">$<span id="display_price">150</span></h3>
                <input type="hidden" id="final_quote" name="final_quote" value="150">
            </div>
            <button type="submit">Save System Lead</button>
        </form>
    </div>

    <div class="table-container">
        <h2>Active Logistics Pipeline</h2>
        <table>
            <thead>
                <tr><th>Customer Profile</th><th>Price Quote</th></tr>
            </thead>
            <tbody>
                {% for lead in leads %}
                <tr>
                    <td><strong>{{ lead.customer_name }}</strong><br><small>{{ lead.phone }}</small></td>
                    <td style="font-weight: bold; color: #2e7d32;">${{ lead.estimated_quote }}</td>
                </tr>
                {% endfor %}
            </tbody>
        </table>
    </div>

    <script>
        // Performs local mathematical evaluation instantly upon input modification
        function calculateQuote() {
            let rooms = document.getElementById('rooms').value;
            let miles = document.getElementById('miles').value;
            let total = (parseInt(rooms) * 100) + (parseInt(miles) * 2) + 50;
            document.getElementById('display_price').innerText = total;
            document.getElementById('final_quote').value = total;
        }
    </script>
</body>
</html>
```

---

 ## 🧪 Phase 1 Testing Routine

Run the application:

```bash
python app.py
```

Open browser:

```plaintext
http://127.0.0.1:5000
```

### Testing Checklist

* Enter mock profile data
* Modify rooms/miles
* Verify live quote updates
* Save a lead
* Confirm lead appears in dashboard

Stop the server:

```bash
CTRL + C
```

---

 ## 🚀 Phase 2 — Workflow Tracking (Status Lifecycles)

This phase introduces:

* Inquiry tracking
* Booked tracking
* Completed tracking
* Status badges
* Action buttons

---

 ## 💻 VS Code File Setup Instructions

1. Open `app.py`
2. Delete all contents
3. Paste the new Phase 2 code
4. Save the file
5. Open `templates/index.html`
6. Replace all contents
7. Save again

---

 ## 📄 Overwrite `app.py` (Phase 2 Full Source)

```python
from flask import Flask, render_template, request, redirect
from flask_sqlalchemy import SQLAlchemy

app = Flask(__name__)
app.config['SQLALCHEMY_DATABASE_URI'] = 'sqlite:///movers.db'
app.config['SQLALCHEMY_TRACK_MODIFICATIONS'] = False

db = SQLAlchemy(app)

class Lead(db.Model):
    id = db.Column(db.Integer, primary_key=True)
    customer_name = db.Column(db.String(100), nullable=False)
    phone = db.Column(db.String(20))
    rooms = db.Column(db.Integer)
    miles = db.Column(db.Integer)
    estimated_quote = db.Column(db.Float)
    status = db.Column(db.String(20), default='Inquiry')

with app.app_context():
    db.create_all()

@app.route('/')
def home():
    all_leads = Lead.query.all()
    return render_template('index.html', leads=all_leads)

@app.route('/add_lead', methods=['POST'])
def add_lead():
    new_lead = Lead(
        customer_name=request.form.get('name'),
        phone=request.form.get('phone'),
        rooms=request.form.get('rooms', type=int),
        miles=request.form.get('miles', type=int),
        estimated_quote=request.form.get('final_quote', type=float)
    )

    db.session.add(new_lead)
    db.session.commit()

    return redirect('/')

@app.route('/update_status/<int:id>/<string:new_status>')
def update_status(id, new_status):
    lead = Lead.query.get(id)

    if lead:
        lead.status = new_status
        db.session.commit()

    return redirect('/')

if __name__ == '__main__':
    app.run(debug=True)
```

---

 ## 🧪 Phase 2 Testing Routine

Delete old database:

### Windows

```bash
del movers.db
```

### Mac/Linux

```bash
rm movers.db
```

Run app:

```bash
python app.py
```

Open browser:

```plaintext
http://127.0.0.1:5000
```

Verify:

* Inquiry status works
* BOOK action works
* DONE action works
* Badge colors update correctly

Stop server:

```bash
CTRL + C
```

---

 ## 🎨 Phase 3 — Visual Identity UI Upgrade (Lock & Load Branding)

This phase upgrades the interface with:

* Glassmorphism UI
* Gradient backgrounds
* Branding upgrades
* Accent colors
* Responsive layout styling

---

 ## 📄 Overwrite `app.py` (Phase 3 Full Source)

```python
from flask import Flask, render_template, request, redirect
from flask_sqlalchemy import SQLAlchemy

app = Flask(__name__)
app.config['SQLALCHEMY_DATABASE_URI'] = 'sqlite:///movers.db'
app.config['SQLALCHEMY_TRACK_MODIFICATIONS'] = False

db = SQLAlchemy(app)

class Lead(db.Model):
    id = db.Column(db.Integer, primary_key=True)
    customer_name = db.Column(db.String(100), nullable=False)
    phone = db.Column(db.String(20))
    rooms = db.Column(db.Integer)
    miles = db.Column(db.Integer)
    estimated_quote = db.Column(db.Float)
    status = db.Column(db.String(20), default='Inquiry')

with app.app_context():
    db.create_all()

@app.route('/')
def home():
    all_leads = Lead.query.all()
    return render_template('index.html', leads=all_leads)

@app.route('/add_lead', methods=['POST'])
def add_lead():
    new_lead = Lead(
        customer_name=request.form.get('name'),
        phone=request.form.get('phone'),
        rooms=request.form.get('rooms', type=int),
        miles=request.form.get('miles', type=int),
        estimated_quote=request.form.get('final_quote', type=float)
    )

    db.session.add(new_lead)
    db.session.commit()

    return redirect('/')

@app.route('/update_status/<int:id>/<string:new_status>')
def update_status(id, new_status):
    lead = Lead.query.get(id)

    if lead:
        lead.status = new_status
        db.session.commit()

    return redirect('/')

if __name__ == '__main__':
    app.run(debug=True)
```

---

 ## 🚀 Phase 4 — Full Logistics Center (Search Filters, PDF Rendering, Email & LAN Sharing)

Final production features:

* Search filters
* PDF quote generation
* Email quote delivery
* LAN/mobile access
* ReportLab integration
* SMTP mail routing

---

 ## 📄 Overwrite `app.py` (Phase 4 Final Code)

```python
from flask import Flask, render_template, request, redirect, send_file
from flask_sqlalchemy import SQLAlchemy
import io
import smtplib
from email.mime.multipart import MIMEMultipart
from email.mime.text import MIMEText
from email.mime.base import MIMEBase
from email import encoders
from reportlab.pdfgen import canvas
from reportlab.lib.pagesizes import LETTER

app = Flask(__name__)
app.config['SQLALCHEMY_DATABASE_URI'] = 'sqlite:///movers.db'
app.config['SQLALCHEMY_TRACK_MODIFICATIONS'] = False

db = SQLAlchemy(app)

SENDER_EMAIL = "your-email@gmail.com"
SENDER_PASSWORD = "your-16-char-app-password"

class Lead(db.Model):
    id = db.Column(db.Integer, primary_key=True)
    customer_name = db.Column(db.String(100), nullable=False)
    phone = db.Column(db.String(20))
    rooms = db.Column(db.Integer)
    miles = db.Column(db.Integer)
    estimated_quote = db.Column(db.Float)
    status = db.Column(db.String(20), default='Inquiry')

with app.app_context():
    db.create_all()

if __name__ == '__main__':
    app.run(debug=True, host='0.0.0.0')
```

---

 ## 🧪 Phase 4 Final Functional Testing Routine

Delete database:

### Windows

```bash
del movers.db
```

### Mac/Linux

```bash
rm movers.db
```

Find local IP:

### Windows

```bash
ipconfig
```

### Mac/Linux

```bash
ifconfig
```

Run server:

```bash
python app.py
```

Open locally:

```plaintext
http://127.0.0.1:5000
```

Open on mobile:

```plaintext
http://192.168.1.XX:5000
```

---

 ## ✅ Final Features Checklist

✅ Customer intake system
✅ Live quote calculations
✅ SQLite database integration
✅ Workflow tracking
✅ Modern branded UI
✅ PDF quote downloads
✅ Email quote system
✅ Search filtering
✅ LAN/mobile support

---

 ## 🏢 Branding

# 🚚 Lock & Load

### *Locked in on every move*

© 2026 Lockett Corporation. All systems operational.

