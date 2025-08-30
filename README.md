

---

# 📘 **SMSPROJ - Comprehensive Communication Platform (MVP)**

**SMSPROJ** is a powerful FastAPI-based communication platform focused on SMS service verification and messaging. This MVP replaces Twilio with a mock SMS provider, allowing you to develop and test a fully functional SMS platform without external dependencies for sending and receiving messages.

---

## 🔸 **Platform Overview**

### 🚀 **Core Capabilities (MVP)**

1. **Service Verification Hub**

   * Use TextVerified API to obtain temporary numbers for service verification
   * Support for WhatsApp, Google, Telegram, and 100+ other services
   * Automated SMS code retrieval and display

2. **International SMS Communication (Mocked)**

   * Send and receive SMS using a **mock SMS provider** for local development and testing
   * Simulated delivery without real SMS gateway dependencies
   * Provider-agnostic architecture allowing easy integration of real providers in the future

3. **Dedicated Number Management**

   * Manage verification numbers via TextVerified API where supported
   * Local storage of number assignments during MVP phase
   * Prepare for future integration with dedicated number purchases and subscriptions

4. **AI-Powered Messaging Assistance**

   * Embedded language models for response suggestions and contextual help
   * Local AI processing focused on privacy and offline usage

5. **Flexible Usage Models**

   * One-time verification via TextVerified
   * SMS sending and receiving via mock provider for now
   * Future-ready architecture for switching SMS providers seamlessly

---

## 🛠 **Technology Stack**

* **Backend**: FastAPI (Python 3.11+)
* **APIs**: TextVerified (verification & number mgmt), MockSMS (MVP SMS sending/receiving)
* **Database**: SQLAlchemy with PostgreSQL
* **Caching**: Redis (token management, optional)
* **AI**: Local transformer models for messaging assistance
* **Authentication**: JWT tokens
* **Deployment**: Docker + Docker Compose

---

## ⚙️ **Environment Setup**

### 📋 Prerequisites

* Python 3.11+
* TextVerified account for verification services
* PostgreSQL database
* Redis (optional)
* Docker and Docker Compose (recommended for production/dev environment)

### 📥 Install and Setup

```bash
git clone <repository-url>
cd SMSPROJ
pip install -r requirements.txt
```

### 📝 Environment Variables (`.env`)

```env
# TextVerified API
TEXTVERIFIED_API_KEY=your_textverified_api_key
TEXTVERIFIED_EMAIL=your_email@example.com

# SMS Provider
SMS_PROVIDER=mock  # Use 'mock' for local dev, switch to real providers later

# Database
DATABASE_URL=postgresql://user:password@localhost/communication_db

# Redis (optional)
REDIS_URL=redis://localhost:6379

# JWT Secret
JWT_SECRET_KEY=your_jwt_secret_key

# AI Model (optional)
AI_MODEL_PATH=./models/local_model
```

### 🚀 Run the Application

```bash
uvicorn main:app --host 0.0.0.0 --port 8000 --reload
```

---

## 🔸 **API Endpoints**

### 🔐 Authentication

* `POST /api/auth/login` — User login
* `POST /api/auth/register` — User registration
* `POST /api/auth/refresh` — Refresh JWT token

### 📱 Verification Services (TextVerified)

* `POST /api/verification/create` — Create service verification
* `GET /api/verification/{id}/number` — Get verification number
* `GET /api/verification/{id}/messages` — Get received SMS codes
* `DELETE /api/verification/{id}` — Cancel verification

### 💬 SMS Communication (Mock Provider)

* `POST /api/sms/send` — Send SMS (via mock backend in MVP)
* `POST /api/sms/receive` — Webhook or simulated incoming SMS receiver

### 📞 Voice Communication (Currently Not Supported)

> All voice-related endpoints return HTTP 501 Not Implemented in MVP.

* `POST /api/voice/call` — Make outbound call
* `POST /api/voice/receive` — Webhook for incoming calls
* `POST /api/voice/record` — Manage call recording
* `POST /api/voice/forward` — Forward calls
* `POST /api/voice/conference` — Conference call setup

### 📞 Number Management (TextVerified)

* `GET /api/numbers/available/{country_code}` — List available numbers
* `POST /api/numbers/purchase` — Purchase/assign number
* `GET /api/numbers/user/{user_id}` — List user numbers
* `DELETE /api/numbers/{number_id}` — Release number

### 💳 Subscriptions

* `GET /api/subscriptions/plans` — List subscription plans
* `POST /api/subscriptions/subscribe` — Create subscription
* `GET /api/subscriptions/user/{user_id}` — User subscriptions

### 🧠 AI-Powered Messaging

* `GET /api/sms/suggestions` — Get AI response suggestions

---

## 🔸 **Usage Examples**

### Create Verification & Get Temporary Number

```python
import httpx

response = await httpx.post(
    "http://localhost:8000/api/verification/create",
    json={"service_name": "whatsapp", "capability": "sms"},
    headers={"Authorization": "Bearer your_jwt_token"}
)
verification_id = response.json()["verification_id"]

number_resp = await httpx.get(f"http://localhost:8000/api/verification/{verification_id}/number")
print("Temp Number:", number_resp.json()["number"])

messages_resp = await httpx.get(f"http://localhost:8000/api/verification/{verification_id}/messages")
print("Received Codes:", messages_resp.json()["messages"])
```

### Send SMS (Mocked)

```python
response = await httpx.post(
    "http://localhost:8000/api/sms/send",
    json={"to_number": "+1234567890", "message": "Hello from SMSPROJ MVP"},
    headers={"Authorization": "Bearer your_jwt_token"}
)
print("SMS Send Status:", response.status_code)
```

---

## 🐳 **Docker Compose Setup**

```yaml
version: '3.8'
services:
  app:
    build: .
    ports:
      - "8000:8000"
    environment:
      - DATABASE_URL=postgresql://postgres:password@db:5432/communication_db
      - REDIS_URL=redis://redis:6379
      - SMS_PROVIDER=mock
    depends_on:
      - db
      - redis
    volumes:
      - ./models:/app/models

  db:
    image: postgres:15
    environment:
      POSTGRES_DB: communication_db
      POSTGRES_USER: postgres
      POSTGRES_PASSWORD: password
    volumes:
      - postgres_data:/var/lib/postgresql/data

  redis:
    image: redis:7-alpine
    ports:
      - "6379:6379"

volumes:
  postgres_data:
```

### Deploy

```bash
docker-compose up -d
docker-compose exec app alembic upgrade head
docker-compose logs -f app
```

---

## 🔸 **Development Roadmap**

| Phase                          | Features                                                                                  | Status        |
| ------------------------------ | ----------------------------------------------------------------------------------------- | ------------- |
| **Phase 1: MVP**               | Service Verification (TextVerified), SMS sending/receiving (mock provider), AI messaging  | ✅ Implemented |
| **Phase 2: Advanced Features** | Smart routing, voice calling, call recording, voicemail, real number purchases, analytics | 🚧 Planned    |

---

## ⚠️ **Notes**

* **Voice communication features are not supported in the MVP** and will respond with HTTP 501 errors.
* SMS sending and receiving use a **mock provider**; messages are simulated locally without external SMS delivery.
* Number management relies on **TextVerified API**; features depend on TextVerified's availability.
* To enable real SMS gateways, implement and configure providers and update `SMS_PROVIDER` accordingly.
* AI messaging runs locally to preserve privacy.

---

## 🤝 **Support**

For issues or questions:

* Open a GitHub issue
* Review the API docs and examples
* Contribute by forking and submitting pull requests

---

**Crafted with ❤️ — SMSPROJ MVP**

---

---


