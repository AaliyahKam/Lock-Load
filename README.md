# Lock-Load

Lock & Load | Step-by-Step Logistics CRM Build Manual
This document is a comprehensive, beginner-friendly guide to building the Lock & Load Logistics Command Center completely from scratch. You will start with a blank screen and progress through 4 distinct phases of development.

After each phase, you will pause, run specific testing steps, verify your features in the browser, and gracefully shut down the application before advancing. This iterative approach ensures your database, backend environment, and visual interfaces remain fully synchronized.

🛠️ Step 1: Initialize Your Workspace & Virtual Environment
Before writing a single line of code, you must create a clean directory on your computer and isolate your software libraries. This prevents version conflicts with other projects.

Open your terminal application (PowerShell or Command Prompt on Windows, Terminal on Mac).

Navigate to your computer's Desktop:

Bash
cd Desktop
Create a brand-new directory for your project:

Bash
mkdir MovingCRM2
Move inside your newly created project folder:

Bash
cd MovingCRM2
Create an isolated Python virtual environment named venv:

Bash
python -m venv venv
Activate the virtual environment:

Windows (PowerShell):

Bash
.\venv\Scripts\activate
Mac / Linux:

Bash
source venv/bin/activate
(You will know this worked when you see (venv) appear at the far left side of your terminal line).

📦 Step 2: Install Required Libraries
Your system relies on external software modules to handle web routing, structured database storage, and dynamic PDF design layout generation.

Run the installation command inside your activated terminal:

Bash
pip install flask flask_sqlalchemy reportlab
Wait for the success confirmation. This installs:

Flask: The micro-framework that runs your local server and URL routing.

Flask-SQLAlchemy: The Object Relational Mapper that lets Python talk to databases using clean object syntax.

ReportLab: The programmatic engine used to draw text, lines, and geometries onto PDF documents.

🚀 Phase 1: The Baseline Application (Data Intake Engine)
In this phase, you will create a simple data intake tool. It will accept a client's name, phone number, inventory scale (rooms), and travel distance (miles), calculate a base quote price using local JavaScript math routines, and record it directly into a local file-based database.

📄 Create app.py (Phase 1 Full Source)
Create a file named app.py in your main MovingCRM2 folder and save it with this code:

Python
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
📄 Create templates/index.html (Phase 1 Full Source)
Create a brand new folder inside MovingCRM2 named templates. Inside that folder, create a file named index.html and save it with this code:

HTML
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
🧪 Phase 1 Testing Routine
In your terminal, run the execution command:

Bash
python app.py
Open your web browser and navigate to: http://127.0.0.1:5000

Enter a mock profile name and a test number like (229) 296-2069.

Modify the rooms and miles fields, verifying that the pricing number transforms live on screen.

Click Save System Lead. The page will reload and show the record inside the right-hand container.

Return to your terminal window and press Ctrl + C to safely kill the execution loop.

🔄 Phase 2: Workflow Tracking (Status Lifecycles)
Now, we upgrade the database architecture to support structural pipelines. We will track if a customer profile is currently categorized as an Inquiry, Booked on the log books, or completely finished (Completed).

📄 Overwrite app.py (Phase 2 Full Source)
Replace everything currently inside your app.py file with this update:

Python
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
    status = db.Column(db.String(20), default='Inquiry') # <-- NEW COLUMN TRACKING DATA SHAPE

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

# <-- NEW ROUTE: Alters operational statuses dynamically matching item identities
@app.route('/update_status/<int:id>/<string:new_status>')
def update_status(id, new_status):
    lead = Lead.query.get(id)
    if lead:
        lead.status = new_status
        db.session.commit()
    return redirect('/')

if __name__ == '__main__':
    app.run(debug=True)
📄 Overwrite templates/index.html (Phase 2 Full Source)
Replace everything currently inside your templates/index.html file with this updated framework containing status loops and pipeline modification actions:

HTML
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Movers Workflow CRM</title>
    <style>
        body { font-family: 'Segoe UI', sans-serif; margin: 40px; background-color: #f0f2f5; display: flex; gap: 20px; }
        .form-container { flex: 1; background: white; padding: 25px; border-radius: 12px; box-shadow: 0 4px 15px rgba(0,0,0,0.1); height: fit-content; }
        .table-container { flex: 2; background: white; padding: 25px; border-radius: 12px; box-shadow: 0 4px 15px rgba(0,0,0,0.1); }
        h2 { color: #1a73e8; margin-top: 0; }
        label { display: block; margin-top: 10px; font-weight: bold; color: #555; }
        input { width: 100%; padding: 12px; margin: 8px 0; border: 1px solid #ddd; border-radius: 6px; box-sizing: border-box; font-size: 16px; }
        button { background-color: #28a745; color: white; padding: 14px; border: none; border-radius: 6px; cursor: pointer; width: 100%; font-weight: bold; font-size: 16px; margin-top: 15px; }
        table { width: 100%; border-collapse: collapse; margin-top: 10px; }
        th, td { text-align: left; padding: 12px; border-bottom: 1px solid #eee; }
        
        /* Visual Pipeline Badge Formats */
        .status-badge { padding: 4px 10px; border-radius: 20px; font-size: 12px; font-weight: bold; text-transform: uppercase; }
        .Inquiry { background: #e3f2fd; color: #1976d2; }
        .Booked { background: #fff3e0; color: #ef6c00; }
        .Completed { background: #e8f5e9; color: #2e7d32; }
        
        .action-link { text-decoration: none; font-size: 11px; font-weight: bold; margin-right: 5px; color: #1a73e8; }
        .action-link:hover { text-decoration: underline; }
    </style>
</head>
<body>

    <div class="form-container">
        <h2>🚚 New Lead</h2>
        <form action="/add_lead" method="POST">
            <input type="text" name="name" placeholder="Customer Name" required>
            <input type="text" name="phone" placeholder="Phone Number">
            <label>Rooms:</label>
            <input type="number" id="rooms" name="rooms" value="1" min="1" oninput="calculateQuote()">
            <label>Miles:</label>
            <input type="number" id="miles" name="miles" value="0" min="0" oninput="calculateQuote()">
            
            <div style="background: #f8f9fa; padding: 20px; border-radius: 8px; margin-top: 20px;">
                <small>ESTIMATED TOTAL</small>
                <h3 style="margin:0; color:#1a73e8;">$<span id="display_price">150</span></h3>
                <input type="hidden" id="final_quote" name="final_quote" value="150">
            </div>
            <button type="submit">Save Lead</button>
        </form>
    </div>

    <div class="table-container">
        <h2>Active Pipeline</h2>
        <table>
            <thead>
                <tr>
                    <th>Customer</th>
                    <th>Quote</th>
                    <th>Status</th>
                    <th>Actions</th>
                </tr>
            </thead>
            <tbody>
                {% for lead in leads %}
                <tr>
                    <td><strong>{{ lead.customer_name }}</strong><br><small>{{ lead.phone }}</small></td>
                    <td style="font-weight: bold; color: #2e7d32;">${{ lead.estimated_quote }}</td>
                    <td><span class="status-badge {{ lead.status }}">{{ lead.status }}</span></td>
                    <td>
                        <a href="/update_status/{{ lead.id }}/Booked" class="action-link">BOOK</a>
                        <a href="/update_status/{{ lead.id }}/Completed" class="action-link" style="color: #2e7d32;">DONE</a>
                    </td>
                </tr>
                {% endfor %}
            </tbody>
        </table>
    </div>

    <script>
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
🧪 Phase 2 Testing Routine
CRITICAL: Since you updated the backend Python model structure to include a new database row category (status), the existing, old database file layout on your computer will break your server with an OperationalError because it lacks the new structure. You must clear out the old file layout to start clean.

Wipe out the outdated database storage tracking file inside your terminal:

Bash
# Windows command:
del movers.db

# Mac or Linux command:
rm movers.db
Start the updated framework server script:

Bash
python app.py
Open your browser to http://127.0.0.1:5000.

Enter a fresh user entry. It will load into the tracking table sporting a light-blue INQUIRY status tag.

Click the blue BOOK link text on the row. The page will reload, and the status tag will change to an orange BOOKED badge.

Click the green DONE link text. The badge will transform to a green COMPLETED state.

Return to the terminal command display and press Ctrl + C to kill execution before continuing.

🎨 Phase 3: Visual Identity UI Upgrade (Lock & Load Branding)
We will now polish the user interface. We'll implement a modern theme featuring a Soft Sky Blue gradient backdrop, Glassmorphism translucent interface card structures, high-visibility Deep Blue typography accents, and sleek Accent Pink interaction highlights. We will also add the Lockett Corporation operational footer and the brand-specific "Locked in on every move" business slogan.

📄 Overwrite app.py (Phase 3 Full Source)
The operational database tables remain consistent with Phase 2, but we update the engine code template logic to match the new look:

Python
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
📄 Overwrite templates/index.html (Phase 3 Full Source)
Replace everything currently inside your templates/index.html template layout file with this stylized version:

HTML
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Lock & Load | Dispatch Command</title>
    <style>
        :root {
            --sky-blue: #e3f2fd;
            --deep-blue: #1565c0;
            --soft-pink: #fce4ec;
            --accent-pink: #f06292;
            --glass: rgba(255, 255, 255, 0.9);
        }

        body { 
            font-family: 'Segoe UI', sans-serif; 
            margin: 0; 
            background: linear-gradient(135deg, var(--sky-blue) 0%, #ffffff 100%);
            min-height: 100vh;
            color: #333;
        }

        header {
            background: white;
            padding: 20px 40px;
            box-shadow: 0 2px 10px rgba(0,0,0,0.05);
            display: flex;
            justify-content: space-between;
            align-items: center;
        }

        .brand h1 { margin: 0; color: var(--deep-blue); letter-spacing: -1px; }
        .brand p { margin: 0; font-size: 12px; color: var(--accent-pink); font-weight: bold; text-transform: uppercase; }

        .main-container { display: flex; gap: 30px; padding: 40px; max-width: 1400px; margin: 0 auto; }

        .card { 
            background: var(--glass); 
            backdrop-filter: blur(10px);
            padding: 30px; 
            border-radius: 20px; 
            box-shadow: 0 10px 30px rgba(0,0,0,0.04); 
            border: 1px solid rgba(255,255,255,0.8);
        }

        .form-container { flex: 1; height: fit-content; }
        .table-container { flex: 2; }

        input { 
            width: 100%; padding: 14px; margin: 10px 0; 
            border: 1.5px solid #eee; border-radius: 10px; 
            background: #fdfdfd; transition: 0.3s;
        }
        input:focus { border-color: var(--deep-blue); outline: none; box-shadow: 0 0 0 4px rgba(21, 101, 192, 0.1); }

        button { 
            background: var(--deep-blue); color: white; 
            padding: 16px; border: none; border-radius: 10px; 
            width: 100%; font-weight: 700; cursor: pointer; 
            margin-top: 20px; transition: 0.3s;
        }
        button:hover { transform: translateY(-2px); box-shadow: 0 5px 15px rgba(21, 101, 192, 0.3); }

        table { width: 100%; border-collapse: separate; border-spacing: 0 10px; }
        th { text-align: left; padding: 15px; color: #888; font-size: 13px; }
        td { background: white; padding: 20px; }
        td:first-child { border-radius: 15px 0 0 15px; }
        td:last-child { border-radius: 0 15px 15px 0; }

        .status-badge { padding: 6px 12px; border-radius: 8px; font-size: 11px; font-weight: 800; }
        .Inquiry { background: var(--soft-pink); color: var(--accent-pink); }
        .Booked { background: #e3f2fd; color: #1e88e5; }
        .Completed { background: #e8f5e9; color: #2e7d32; }

        .btn-action {
            text-decoration: none; padding: 5px 10px; border-radius: 5px;
            font-size: 10px; font-weight: bold; border: 1px solid #eee; color: #666;
            transition: 0.2s;
        }
        .btn-action:hover { background: #eee; }

        footer { text-align: center; padding: 40px; color: #aaa; font-size: 12px; }
    </style>
</head>
<body>

    <header>
        <div class="brand">
            <h1>Lock & Load</h1>
            <p>Locked in on every move</p>
        </div>
        <div style="text-align: right;">
            <span style="font-weight: bold; color: #555;">Dispatch Dashboard</span><br>
            <small id="date-display"></small>
        </div>
    </header>

    <div class="main-container">
        <div class="card form-container">
            <h3 style="margin-top:0;">Create New Quote</h3>
            <form action="/add_lead" method="POST">
                <input type="text" name="name" placeholder="Customer Full Name" required>
                <input type="text" name="phone" placeholder="Contact Number">
                
                <div style="display: flex; gap: 10px;">
                    <div style="flex:1">
                        <label style="font-size: 12px; color: #888;">Rooms</label>
                        <input type="number" id="rooms" name="rooms" value="1" min="1" oninput="calculateQuote()">
                    </div>
                    <div style="flex:1">
                        <label style="font-size: 12px; color: #888;">Miles</label>
                        <input type="number" id="miles" name="miles" value="0" min="0" oninput="calculateQuote()">
                    </div>
                </div>
                
                <div style="background: #f8f9fa; padding: 20px; border-radius: 12px; margin-top: 20px; border: 1px dashed #ddd;">
                    <small style="color: #888; font-weight: bold;">LIVE ESTIMATE</small>
                    <h2 style="margin: 5px 0; color: var(--deep-blue);">$<span id="display_price">150</span></h2>
                    <input type="hidden" id="final_quote" name="final_quote" value="150">
                </div>

                <button type="submit">Deploy Lead</button>
            </form>
        </div>

        <div class="table-container">
            <div class="card">
                <h3 style="margin-top:0;">Active Logistics Pipeline</h3>
                <table>
                    <thead>
                        <tr>
                            <th>CLIENT</th>
                            <th>ESTIMATE</th>
                            <th>STATUS</th>
                            <th>MANAGE</th>
                        </tr>
                    </thead>
                    <tbody>
                        {% for lead in leads %}
                        <tr>
                            <td>
                                <strong>{{ lead.customer_name }}</strong><br>
                                <small style="color: #999;">{{ lead.phone }}</small>
                            </td>
                            <td>
                                <span style="font-weight: bold;">${{ lead.estimated_quote }}</span><br>
                                <small style="color: #aaa;">{{ lead.rooms }} Rms / {{ lead.miles }} Mi</small>
                            </td>
                            <td><span class="status-badge {{ lead.status }}">{{ lead.status }}</span></td>
                            <td>
                                <a href="/update_status/{{ lead.id }}/Booked" class="btn-action">BOOK</a>
                                <a href="/update_status/{{ lead.id }}/Completed" class="btn-action">FINISH</a>
                            </td>
                        </tr>
                        {% endfor %}
                    </tbody>
                </table>
            </div>
        </div>
    </div>

    <footer>
        &copy; 2026 Lockett Corporation. All systems operational.
    </footer>

    <script>
        // Set dynamic date formatting
        document.getElementById('date-display').innerText = new Date().toLocaleDateString('en-US', { weekday: 'long', year: 'numeric', month: 'long', day: 'numeric' });

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
🧪 Phase 3 Testing Routine
Since the underlying data schema hasn't changed from Phase 2, you don't need to delete the database file this time.

Start up your runtime environment link engine inside your terminal window:

Bash
python app.py
Refresh your browser window at http://127.0.0.1:5000.

Verify that the background gradient matches the soft sky blue color space and that the tracking cards present an elegant translucent border panel structure.

Add a sample client entry to ensure your submission forms are operational.

Exit using Ctrl + C inside your command terminal interface.

🏆 Phase 4: Full Logistics Center (Search Filters, PDF Rendering, Email, & LAN Sharing)
This is the final deployment setup. It integrates an algorithmic search parsing filter directly into the home view, automated ReportLab canvas drawing systems to compile dynamic PDF files in memory, secure SMTPlib mail block attachment routines, and network listening arguments to broadcast the application locally to external mobile screens.

📄 Overwrite app.py (Phase 4 Final Code)
Replace your entire workspace script inside app.py with this final production assembly.

Security Setup Note: To test the email button feature, look for the SENDER_EMAIL and SENDER_PASSWORD variables below. Change them to your personal email account and a secure 16-character Google App Password (found in your Google Account security panel).

Python
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

# --- SECURE OUTBOUND EMAIL ROUTING SYSTEM ---
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

@app.route('/')
def home():
    # Capture search inputs from the header field filter input
    search_query = request.args.get('search')
    if search_query:
        # Algorithmic string matching matching profiles or telephone data sequences
        all_leads = Lead.query.filter(
            (Lead.customer_name.contains(search_query)) | 
            (Lead.phone.contains(search_query))
        ).all()
    else:
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

# Helper: System drawing engine mapping client data items into canvas primitives
def generate_pdf_buffer(lead):
    buffer = io.BytesIO()
    p = canvas.Canvas(buffer, pagesize=LETTER)
    
    # Render corporate title structures and text branding elements
    p.setFont("Helvetica-Bold", 24)
    p.drawString(100, 750, "Lock & Load")
    p.setFont("Helvetica", 12)
    p.drawString(100, 735, "Locked in on every move | Lockett Corporation")
    p.line(100, 720, 500, 720)
    
    # Inject variable data properties harvested dynamically out of database file entries
    p.setFont("Helvetica-Bold", 14)
    p.drawString(100, 680, f"Moving Quote for: {lead.customer_name}")
    p.setFont("Helvetica", 12)
    p.drawString(100, 660, f"Phone Contact: {lead.phone}")
    p.drawString(100, 640, f"Inventory Volume: {lead.rooms} Rooms")
    p.drawString(100, 620, f"Transit Distance: {lead.miles} Miles")
    
    p.setFont("Helvetica-Bold", 18)
    p.drawString(100, 580, f"ESTIMATED TOTAL: ${lead.estimated_quote}")
    
    p.setFont("Helvetica-Oblique", 10)
    p.drawString(100, 500, "Thank you for choosing Lock & Load. We are locked in on your move.")
    
    p.showPage()
    p.save()
    buffer.seek(0)
    return buffer

@app.route('/download_pdf/<int:id>')
def download_pdf(id):
    lead = Lead.query.get(id)
    buffer = generate_pdf_buffer(lead)
    return send_file(buffer, as_attachment=True, download_name=f"Quote_{lead.customer_name}.pdf", mimetype='application/pdf')

@app.route('/email_quote/<int:id>')
def email_quote(id):
    lead = Lead.query.get(id)
    buffer = generate_pdf_buffer(lead)
    pdf_data = buffer.getvalue()

    # Construct standard multi-part internet transmission body wrappers
    msg = MIMEMultipart()
    msg['From'] = SENDER_EMAIL
    msg['To'] = "recipient-test@example.com" # Put your own personal target address here for system validation tests
    msg['Subject'] = f"Moving Quote Confirmation: {lead.customer_name} - Lock & Load"
    msg.attach(MIMEText(f"Hello {lead.customer_name},\n\nPlease review your attached professional contract quote request document.\n\nLocked in,\nLockett Corporation Logistics Command", 'plain'))

    # Encode raw data array sequences into discrete Base64 document payloads
    part = MIMEBase('application', 'octet-stream')
    part.set_payload(pdf_data)
    encoders.encode_base64(part)
    part.add_header('Content-Disposition', f"attachment; filename=Quote_{lead.customer_name}.pdf")
    msg.attach(part)

    try:
        server = smtplib.SMTP('smtp.gmail.com', 587)
        server.starttls()
        server.login(SENDER_EMAIL, SENDER_PASSWORD)
        server.send_message(msg)
        server.quit()
        print("Mailing data array sequence transported securely.")
    except Exception as e:
        print(f"Network Email Transport Failure: {e}")
    
    return redirect('/')

if __name__ == '__main__':
    # host='0.0.0.0' commands your system to unlock listening ports to any active home Wi-Fi device
    app.run(debug=True, host='0.0.0.0')
📄 Overwrite templates/index.html (Phase 4 Final Code)
Replace your entire templates/index.html UI structure code to introduce the header search elements and new programmatic table actions:

HTML
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Lock & Load | Logistics Command</title>
    <style>
        :root { --sky-blue: #e3f2fd; --deep-blue: #1565c0; --soft-pink: #fce4ec; --accent-pink: #f06292; }
        body { font-family: 'Segoe UI', sans-serif; margin: 0; background: linear-gradient(135deg, var(--sky-blue) 0%, #ffffff 100%); min-height: 100vh; }
        header { background: white; padding: 15px 30px; box-shadow: 0 2px 10px rgba(0,0,0,0.05); display: flex; flex-wrap: wrap; justify-content: space-between; align-items: center; }
        .brand h1 { margin: 0; color: var(--deep-blue); font-size: 24px; letter-spacing: -1px; }
        .brand p { margin: 0; font-size: 11px; color: var(--accent-pink); font-weight: bold; text-transform: uppercase; }
        .main-container { display: flex; flex-direction: row; gap: 20px; padding: 20px; max-width: 1400px; margin: 0 auto; }
        @media (max-width: 900px) { .main-container { flex-direction: column; } }
        .card { background: rgba(255, 255, 255, 0.95); padding: 25px; border-radius: 15px; box-shadow: 0 8px 25px rgba(0,0,0,0.05); border: 1px solid white; }
        .form-container { flex: 1; }
        .table-container { flex: 2; overflow-x: auto; }
        input { width: 100%; padding: 12px; margin: 8px 0; border: 1px solid #ddd; border-radius: 8px; box-sizing: border-box; }
        button { background: var(--deep-blue); color: white; padding: 15px; border: none; border-radius: 8px; width: 100%; font-weight: bold; cursor: pointer; }
        table { width: 100%; border-collapse: collapse; margin-top: 15px; }
        th, td { padding: 12px; text-align: left; border-bottom: 1px solid #eee; }
        .status-badge { padding: 4px 8px; border-radius: 6px; font-size: 10px; font-weight: bold; text-transform: uppercase; }
        .Inquiry { background: var(--soft-pink); color: var(--accent-pink); }
        .Booked { background: #e3f2fd; color: #1e88e5; }
        .Completed { background: #e8f5e9; color: #2e7d32; }
        .btn-action { text-decoration: none; padding: 6px 10px; border-radius: 5px; font-size: 10px; font-weight: bold; border: 1px solid #ddd; color: #555; display: inline-block; margin: 2px; transition: 0.2s; }
        .btn-action:hover { background: #eee; }
        .btn-pdf { background: var(--accent-pink); color: white; border: none; }
        .btn-pdf:hover { background: #e91e63; opacity: 0.9; }
        .search-bar { padding: 10px; border-radius: 20px; border: 1px solid #ddd; min-width: 250px; }
    </style>
</head>
<body>
    <header>
        <div class="brand">
            <h1>Lock & Load</h1>
            <p>Locked in on every move</p>
        </div>
        <form action="/" method="GET">
            <input type="text" name="search" class="search-bar" placeholder="Search Logistics...">
        </form>
    </header>

    <div class="main-container">
        <div class="card form-container">
            <h3>Dispatch New Quote</h3>
            <form action="/add_lead" method="POST">
                <input type="text" name="name" placeholder="Full Name" required>
                <input type="text" name="phone" placeholder="Contact #">
                <label style="font-size: 12px; color: #666;">Inventory (Rooms)</label>
                <input type="number" id="rooms" name="rooms" value="1" min="1" oninput="calc()">
                <label style="font-size: 12px; color: #666;">Travel (Miles)</label>
                <input type="number" id="miles" name="miles" value="0" min="0" oninput="calc()">
                <div style="background: #f8f9fa; padding: 15px; border-radius: 10px; text-align: center; margin: 15px 0;">
                    <h2 style="margin:0; color: var(--deep-blue);">$<span id="price">150</span></h2>
                    <input type="hidden" id="final_quote" name="final_quote" value="150">
                </div>
                <button type="submit">Deploy to Pipeline</button>
            </form>
        </div>

        <div class="card table-container">
            <h3>Active Logistics Pipeline</h3>
            <table>
                <thead>
                    <tr><th>Client</th><th>Quote</th><th>Status</th><th>Manage</th></tr>
                </thead>
                <tbody>
                    {% for lead in leads %}
                    <tr>
                        <td><strong>{{ lead.customer_name }}</strong><br><small>{{ lead.phone }}</small></td>
                        <td><strong>${{ lead.estimated_quote }}</strong></td>
                        <td><span class="status-badge {{ lead.status }}">{{ lead.status }}</span></td>
                        <td>
                            <a href="/update_status/{{ lead.id }}/Booked" class="btn-action">BOOK</a>
                            <a href="/download_pdf/{{ lead.id }}" class="btn-action btn-pdf">PDF</a>
                            <a href="/email_quote/{{ lead.id }}" class="btn-action" style="background:#e3f2fd; color:var(--deep-blue);">EMAIL</a>
                        </td>
                    </tr>
                    {% endfor %}
                </tbody>
            </table>
        </div>
    </div>
    <script>
        function calc() {
            let r = document.getElementById('rooms').value || 0;
            let m = document.getElementById('miles').value || 0;
            let total = (parseInt(r) * 100) + (parseInt(m) * 2) + 50;
            document.getElementById('price').innerText = total;
            document.getElementById('final_quote').value = total;
        }
    </script>
</body>
</html>
🧪 Phase 4 Final Functional Testing Routine
Wipe the old database structure out one last time via terminal command to prevent alignment structural mismatch errors:

Bash
# Windows:
del movers.db

# Mac/Linux:
rm movers.db
Open a separate terminal split window and determine your computer's local network hardware location using configuration tools:

Bash
# Windows command:
ipconfig

# Mac or Linux command:
ifconfig
(Locate the property tracking string designated IPv4 Address. It typically presents itself formatted like: 192.168.1.XX)

Execute your final master code server wrapper:

Bash
python app.py
On your desktop, verify access at http://127.0.0.1:5000. Enter a complete profile test like Aaliyah Lockett alongside a phone value of 2292962069.

Click the high-contrast PDF action button row container element. Your internet browser will instantly download a dynamic contract printout copy including the Lockett Corporation footers and calculated totals.

Test your filter tools: type a customer name or phone number into the header search bar and hit enter. The interface pipeline list will immediately isolate matching data profiles.

Connect with a mobile device: Ensure your phone is connected to the same home Wi-Fi network link as your computer workstation. Open the browser on your mobile device and type your computer's exact internal IP address followed by the port definition: http://192.168.1.XX:5000. The Lock & Load Command Center will render beautifully on your mobile screen for on-site client interactions!
