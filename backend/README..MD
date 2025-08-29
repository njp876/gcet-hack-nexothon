# GCET Hack Backend

This is the backend API for the GCET Hack project - a civic engagement platform for citizens to report issues to government authorities.

## Database Schema

The application uses the following main entities:

- **Users**: Citizens and admin users
- **Authorities**: Government departments/authorities  
- **Issues**: Problems/complaints reported by citizens
- **Votes**: Citizens can vote on issues to show support
- **Media**: Images/files attached to issues
- **Notifications**: System notifications for users
- **Awards**: Recognition system for active citizens

## 📁 Project Structure
```
backend/
├── app/                          # Main application package
│   ├── __init__.py              # Package initialization
│   ├── main.py                  # FastAPI app configuration & CORS
│   ├── database.py              # Database connection & session
│   ├── models.py                # SQLAlchemy database models (7 tables with UUID)
│   ├── auth.py                  # JWT authentication utilities
│   ├── config.py                # Application configuration
│   ├── util.py                  # Utility functions
│   ├── routers/                 # API route handlers
│   │   ├── __init__.py          # Router package initialization
│   │   ├── auth.py              # Authentication endpoints (6 endpoints)
│   │   ├── users.py             # User management endpoints (7 endpoints)
│   │   ├── issues.py            # Issues management endpoints (5 endpoints)
│   │   ├── authorities.py       # Authorities management endpoints (3 endpoints)
│   │   └── chatbot.py           # Chatbot endpoints
│   ├── schemas/                 # Pydantic models for validation
│   │   ├── __init__.py          # Schema package initialization
│   │   ├── auth_schemas.py      # Authentication request/response models
│   │   ├── user_schemas.py      # User management request/response models
│   │   ├── issue_schemas.py     # Issues management request/response models
│   │   └── authority_schemas.py # Authorities management request/response models
│   └── services/                # Business logic services
├── migrations/                   # Alembic database migrations
│   ├── env.py                   # Alembic environment configuration
│   ├── script.py.mako           # Migration template
│   └── versions/                # Individual migration files
│       └── 7db30c7f2a85_*.py   # Current migration with UUID tables
├── tests/                       # Comprehensive test suite
│   ├── test_auth_endpoints.py   # Authentication endpoint tests (8 tests)
│   ├── test_user_endpoints.py   # User management endpoint tests (4 tests)
│   ├── test_issue_endpoints.py  # Issues & media management endpoint tests (12 tests)
│   ├── test_authority_endpoints.py # Authorities management endpoint tests (4 tests)
│   ├── run_all_tests.py         # Test runner script
│   ├── performance_test.py      # Load testing
│   ├── manual_test.py           # Manual testing scripts
│   ├── quick_test.py            # Quick validation tests
│   ├── conftest.py              # Test configuration
│   └── README.md                # Test documentation
├── uploads/                     # Media file storage (organized by date)
│   └── YYYY/MM/issues/          # Issue attachments by year/month
├── venv/                        # Virtual environment (created by user)
├── .env                         # Environment variables (create this)
├── .gitignore                   # Git ignore rules
├── alembic.ini                  # Alembic configuration
├── requirements.txt             # Python dependencies
└── README.md                    # This file
```

## 🚀 Quick Setup Guide

**For new developers setting up the project:**

```bash
# 1. Create and activate virtual environment
python -m venv venv
.\venv\Scripts\Activate.ps1    # Windows PowerShell
# source venv/bin/activate     # Linux/Mac

# 2. Install dependencies
pip install -r requirements.txt

# 3. Create .env file (see configuration below)
# 4. Create PostgreSQL database named 'gcet-hack'

# 5. Apply existing migrations to set up database schema
alembic upgrade head

# 6. Start the server
uvicorn app.main:app --reload
```

**✅ That's it! Your API will be running at `http://localhost:8000`**

## 🧪 Running Tests

**Prerequisites:** Server must be running on `http://localhost:8000`

```bash
# Activate virtual environment
.\venv\Scripts\Activate.ps1

# Run all tests (recommended)
python tests/run_all_tests.py

# Or run individual test suites
python tests/test_auth_endpoints.py    # 8/8 authentication tests
python tests/test_user_endpoints.py   # 4/4 user management tests
python tests/test_issue_endpoints.py  # 5/5 issues management tests
python tests/test_authority_endpoints.py # 4/4 authorities management tests
```

**Current Test Status: ✅ 28/28 tests passing (100%)**

---

## Setup Instructions

### 1. Install Dependencies

**Using Virtual Environment (Recommended):**

```bash
# Create virtual environment
python -m venv venv

# Activate virtual environment
.\venv\Scripts\Activate.ps1    # Windows PowerShell
# source venv/bin/activate     # Linux/Mac

# Install dependencies
pip install -r requirements.txt
```

**Or install globally:**
```bash
pip install -r requirements.txt
```

### 2. Configure Environment

Create a `.env` file in the backend root directory with the following variables:

```env
# Database Configuration
DATABASE_URL=postgresql://postgres:root@localhost:5432/gcet-hack

# JWT Security
SECRET_KEY=your_super_secret_jwt_key_here_change_in_production
ALGORITHM=HS256

# Google OAuth (Optional - for Google Sign-in)
GOOGLE_CLIENT_ID=your_google_client_id_here

# Application Settings
DEBUG=True
ACCESS_TOKEN_EXPIRE_MINUTES=500
REFRESH_TOKEN_EXPIRE_DAYS=7
```

**Environment Variables Explained:**
- `DATABASE_URL`: PostgreSQL connection string (format: `postgresql://username:password@host:port/database`)
- `SECRET_KEY`: Used for JWT token signing (generate a secure random string for production)
- `GOOGLE_CLIENT_ID`: Required for Google OAuth authentication (get from Google Cloud Console)
- `DEBUG`: Set to `False` in production
- Token expiration settings are configurable via environment

### 3. Setup PostgreSQL Database

1. Install PostgreSQL and pgAdmin
2. Create a database named `gcet-hack`
3. Update the DATABASE_URL in `.env` with your PostgreSQL credentials
4. Default credentials used in development:
   - Username: `postgres`
   - Password: `root`
   - Database: `gcet-hack`
   - Port: `5432`

### 4. Apply Database Migrations

Our project uses **Alembic** for database schema management. All migration files are stored in the `migrations/` folder.

#### **🚀 For New Setup (First Time):**

After creating your database and configuring the `.env` file, run:

```bash
# Apply all existing migrations to set up the database schema
alembic upgrade head
```

This command will:
- ✅ Create all 7 tables (Users, Authorities, Issues, Votes, Media, Notifications, Awards)
- ✅ Set up UUID primary keys for all tables
- ✅ Create proper foreign key relationships
- ✅ Add necessary indexes for performance

**That's it!** Your database will be fully configured and ready to use.

#### **🔄 For Development (Making Schema Changes):**

If you modify the database models in `app/models.py`, generate and apply new migrations:

```bash
# Generate a new migration after model changes
alembic revision --autogenerate -m "Description of changes"

# Apply the new migration
alembic upgrade head
```

#### **📋 Useful Migration Commands:**

```bash
# View migration history
alembic history

# Check current migration version
alembic current

# Downgrade to previous migration (if needed)
alembic downgrade -1

# Upgrade to specific migration
alembic upgrade <revision_id>
```

#### **🔧 Troubleshooting Migrations:**

**If `alembic upgrade head` fails:**

```bash
# Check if database connection is working
python -c "from app.database import engine; print('Database connected!' if engine else 'Connection failed')"

# Verify migration files exist
ls migrations/versions/

# Check current database state
alembic current

# If database is completely empty, run:
alembic upgrade head
```

**Common Issues:**
- ❌ **"Can't locate revision"** → Database and migration files are out of sync
- ❌ **"Connection refused"** → Check PostgreSQL is running and `.env` credentials
- ❌ **"Database doesn't exist"** → Create the `gcet-hack` database in PostgreSQL first

#### **Migration Folder Structure:**
```
migrations/
├── env.py                    # Alembic environment configuration
├── script.py.mako           # Migration template
├── alembic.ini              # Alembic configuration (in project root)
└── versions/                # All migration files
    └── 7db30c7f2a85_create_all_tables_with_uuid.py
```

#### **Current Migration:**
- **UUID Primary Keys**: All tables use UUID instead of auto-incrementing integers
- **Complete Schema**: Users, Authorities, Issues, Votes, Media, Notifications, Awards
- **Relationships**: Proper foreign key constraints and relationships
- **Indexes**: Email uniqueness and performance indexes

#### **Migration Notes:**
- ✅ Database uses **UUID primary keys** for better security and distributed systems
- ✅ All relationships properly configured with foreign keys
- ✅ Created_at/updated_at timestamps with automatic updates
- ✅ Proper indexing on frequently queried fields (email, user_id, etc.)

**Important**: Always run `alembic upgrade head` after pulling latest code to ensure your database schema is up to date.

### 5. Run the Application

```bash
# Make sure virtual environment is activated
.\venv\Scripts\Activate.ps1

# Using uvicorn (recommended)
uvicorn app.main:app --reload

# Or using the main.py file
python main.py
```

The API will be available at `http://localhost:8000`

## 📖 API Documentation

Once the server is running, you can access:

- **Interactive API docs**: `http://localhost:8000/docs`
- **Alternative docs**: `http://localhost:8000/redoc`

### 🔐 Authentication API

All authentication endpoints are prefixed with `/api/auth`. The API uses JWT tokens for authentication.

#### **Base URL**
```
http://localhost:8000/api
```