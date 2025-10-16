#RIC Q&A Application

A full-stack question-and-answer application powered by Google's Gemini AI. This application allows users to interact with an AI chatbot that provides intelligent responses to their questions.

## Table of Contents

- [Overview](#overview)
- [Project Structure](#project-structure)
- [Prerequisites](#prerequisites)
- [Environment Setup](#environment-setup)
- [Running the Application](#running-the-application)
  - [Option 1: Using Docker Compose (Recommended)](#option-1-using-docker-compose-recommended)
  - [Option 2: Running Locally](#option-2-running-locally)
- [API Endpoints](#api-endpoints)
- [Technology Stack](#technology-stack)
- [Configuration](#configuration)
- [Troubleshooting](#troubleshooting)

## 🔍 Overview

This application consists of:
- **Frontend**: A React-based chat interface built with Vite
- **Backend**: A Flask API that integrates with Google's Gemini AI model
- **Containerization**: Docker setup for easy deployment

##  Project Structure

```
ric-qa-main/
├── docker-compose.yaml       # Docker Compose configuration
├── backend/
│   ├── Dockerfile            # Backend container configuration
│   ├── requirements.txt      # Python dependencies
│   ├── wsgi.py              # WSGI entry point
│   └── src/
│       ├── app.py           # Flask application
│       ├── data.csv         # Data file
│       └── predefined_chat.py # Chat history setup
└── frontend/
    ├── Dockerfile           # Frontend container configuration
    ├── package.json         # Node.js dependencies
    ├── vite.config.js       # Vite configuration
    ├── index.html           # HTML entry point
    ├── nginx.conf           # Nginx configuration
    ├── public/
    │   └── RIC.png          # Logo image
    └── src/
        ├── App.jsx          # Main React component
        ├── app.css          # Styles
        └── main.jsx         # React entry point
```

## ✅ Prerequisites

### For Docker Setup:
- [Docker](https://docs.docker.com/get-docker/) (version 20.10+)
- [Docker Compose](https://docs.docker.com/compose/install/) (version 1.29+)

### For Local Setup:
- [Python](https://www.python.org/downloads/) (version 3.8+)
- [Node.js](https://nodejs.org/) (version 16+)
- [npm](https://www.npmjs.com/) or [yarn](https://yarnpkg.com/)

##  Environment Setup

### Backend Environment Variables

The backend requires a Google Gemini API key. You can configure it in two ways:

1. **For Docker**: The API key is set in `docker-compose.yaml`:
   ```yaml
   environment:
     - GEMINI_API_KEY=your_api_key_here
   ```

2. **For Local Development**: Create a `.env` file in the `backend` directory:
   ```bash
   GEMINI_API_KEY=your_api_key_here
   ```

> **Note**: Replace `your_api_key_here` with your actual [Google Gemini API key](https://ai.google.dev/).

### Frontend Environment Variables

Create a `.env` file in the `frontend` directory:
```bash
VITE_APP_BACKEND_URL=http://localhost:8080/api/v1
```

##  Running the Application

### Option 1: Using Docker Compose (Recommended)

This is the easiest way to run the entire application:

1. **Clone the repository**:
   ```bash
   git clone <repository-url>
   cd ric-qa-main
   ```

2. **Update the Gemini API key** in `docker-compose.yaml`:
   ```yaml
   environment:
     - GEMINI_API_KEY=your_actual_api_key
   ```

3. **Build and start the containers**:
   ```bash
   docker-compose up --build
   ```

4. **Access the application**:
   - Frontend: http://localhost:3000
   - Backend API: http://localhost:8080/api/v1

5. **To stop the application**:
   ```bash
   docker-compose down
   ```

### Option 2: Running Locally

#### Backend Setup

1. **Navigate to the backend directory**:
   ```bash
   cd backend
   ```

2. **Create a virtual environment** (recommended):
   ```bash
   python3 -m venv venv
   source venv/bin/activate  # On Windows: venv\Scripts\activate
   ```

3. **Install dependencies**:
   ```bash
   pip install -r requirements.txt
   ```

4. **Create a `.env` file** with your Gemini API key:
   ```bash
   echo "GEMINI_API_KEY=your_actual_api_key" > .env
   ```

5. **Run the Flask application**:
   ```bash
   python wsgi.py
   ```
   
   Or with Gunicorn:
   ```bash
   gunicorn --bind 0.0.0.0:8080 wsgi:app
   ```

   The backend will start on http://localhost:8080

#### Frontend Setup

1. **Open a new terminal** and navigate to the frontend directory:
   ```bash
   cd frontend
   ```

2. **Install dependencies**:
   ```bash
   npm install
   ```

3. **Create a `.env` file**:
   ```bash
   echo "VITE_APP_BACKEND_URL=http://localhost:8080/api/v1" > .env
   ```

4. **Start the development server**:
   ```bash
   npm run dev
   ```

   The frontend will start on http://localhost:5173 (Vite default) or the port shown in the terminal.

5. **Build for production** (optional):
   ```bash
   npm run build
   npm run preview
   ```

## 🔌 API Endpoints

### Base URL
```
http://localhost:8080/api/v1
```

### Endpoints

#### Health Check
```
GET /api/v1
```
Returns a simple "Hello, World!" message to verify the API is running.

#### Generate Answer
```
POST /api/v1/generate-answer?client={client_uuid}
```

**Request Body**:
```json
{
  "question": "Your question here"
}
```

**Response**:
```json
{
  "answer": "AI-generated answer"
}
```

**Query Parameters**:
- `client`: UUID to maintain conversation history per client

## 🛠️ Technology Stack

### Backend
- **Flask**: Web framework
- **Flask-CORS**: Cross-origin resource sharing
- **Google Generative AI**: Gemini AI integration
- **Gunicorn**: WSGI HTTP server
- **Python-dotenv**: Environment variable management

### Frontend
- **React**: UI library
- **Vite**: Build tool and dev server
- **React Markdown**: Markdown rendering
- **UUID**: Unique client identification

### DevOps
- **Docker**: Containerization
- **Docker Compose**: Multi-container orchestration
- **Nginx**: Web server (for production frontend)

## ⚙️ Configuration

### Backend Configuration

The backend uses the Gemini 2.5 Flash model with the following settings:
- `candidate_count`: 1
- `max_output_tokens`: 25000
- `temperature`: 1.0

These can be modified in `backend/src/app.py`.

### Frontend Configuration

Vite configuration can be modified in `frontend/vite.config.js`:
```javascript
export default defineConfig({
  plugins: [react()],
  server: {
    port: 3000, // Change port here
  },
})
```

### Docker Configuration

Port mappings in `docker-compose.yaml`:
- Backend: `8080:8080`
- Frontend: `3000:3000`

##  Troubleshooting

### Common Issues

1. **Port already in use**:
   ```bash
   # Find and kill the process using the port
   lsof -ti:8080 | xargs kill -9  # For backend
   lsof -ti:3000 | xargs kill -9  # For frontend
   ```

2. **Docker containers not starting**:
   ```bash
   # Check logs
   docker-compose logs backend
   docker-compose logs frontend
   
   # Rebuild containers
   docker-compose down
   docker-compose up --build --force-recreate
   ```

3. **API Key errors**:
   - Verify your Gemini API key is valid
   - Check that the key is properly set in environment variables
   - Ensure no extra spaces or quotes around the key

4. **CORS errors**:
   - Verify `VITE_APP_BACKEND_URL` matches your backend URL
   - Check that Flask-CORS is properly configured

5. **Frontend can't connect to backend**:
   - Ensure backend is running and accessible
   - Check the `.env` file in the frontend directory
   - Verify the backend URL in the browser's network tab

### Development Tips

- **Hot Reload**: Both frontend (Vite) and backend (Flask debug mode) support hot reload during development
- **Logs**: Check Docker logs with `docker-compose logs -f` for real-time updates
- **Clear Cache**: If experiencing issues, try clearing Docker cache: `docker system prune -a`

## License

[Add your license information here]

##  Contributors

[Add contributor information here]

##  Contact

[Add contact information here]
