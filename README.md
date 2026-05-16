# AI Resume Parser

AI Resume Parser is a full-stack resume analysis system for uploading PDF resumes, extracting resume text, scoring candidates, and displaying ranked HR insights. The application uses a React/Vite frontend, a Spring Boot backend, Azure Blob Storage, Azure Cosmos DB, and Groq's OpenAI-compatible chat API for resume evaluation.

## Features

- PDF resume upload for individual candidates.
- Bulk HR upload flow for multiple resumes.
- Async resume parsing and ATS-style analysis.
- Candidate ranking by role match score.
- Skill filtering and candidate comparison.
- Azure Blob Storage for resume files.
- Azure Cosmos DB for candidate records.

## Tech Stack

- Frontend: React 18, Vite, Tailwind CSS, Axios, Lucide React
- Backend: Java 17, Spring Boot 3.2, Maven
- Storage: Azure Blob Storage
- Database: Azure Cosmos DB
- AI: Groq API using `llama-3.3-70b-versatile`
- PDF parsing: Apache PDFBox
- Optional serverless module: Azure Functions Java

## Project Structure

```text
Resume-Parser/
  backend/        Spring Boot REST API and resume parsing service
  frontend/       React/Vite user interface
  function_app/   Azure Function module for blob-triggered parsing
```

## Prerequisites

Install these before running the project:

- Node.js 18 or newer
- npm
- Java 17
- Maven 3.9 or newer
- Azure Storage Account with a blob container
- Azure Cosmos DB SQL API database
- Groq API key

## Environment Setup

Copy the example environment file and then add your own local credentials:

```bash
cd backend
cp .env.example .env
```

`backend/.env` should contain:

```env
COSMOS_ENDPOINT=your_cosmos_endpoint
COSMOS_KEY=your_cosmos_key
BLOB_CONNECTION_STRING=your_blob_storage_connection_string
GROQ_API_KEY=your_groq_api_key
```

The backend configuration in `backend/src/main/resources/application.yml` imports `backend/.env` for local development and references these values as environment variables.

For Azure Functions, copy the example settings file before running locally:

```bash
cp function_app/local.settings.example.json function_app/local.settings.json
```

These local configuration files are ignored by Git and are used only for your development environment.

## Backend Setup

From the backend directory:

```bash
cd backend
mvn clean install
mvn spring-boot:run
```

The backend starts on:

```text
http://localhost:8080
```

## Frontend Setup

From the frontend directory:

```bash
cd frontend
npm install
npm run dev
```

The frontend usually starts on:

```text
http://localhost:5173
```

## Frontend Routes

- `/#/` - Landing page
- `/#/candidate` - Single resume upload and ATS analysis
- `/#/hr` - HR dashboard for bulk upload, ranking, filtering, and comparison

## API Endpoints

### Resume API

| Method | Endpoint | Description |
| --- | --- | --- |
| `POST` | `/api/resumes/upload` | Upload one PDF using form field `file` |
| `GET` | `/api/resumes/{trackingId}/status` | Get processing status and parsed result |
| `GET` | `/api/resumes/` | List all candidates |

### HR API

| Method | Endpoint | Description |
| --- | --- | --- |
| `POST` | `/api/hr/upload` | Bulk upload PDFs using form field `files` |
| `GET` | `/api/hr/candidates` | List all candidates |
| `GET` | `/api/hr/candidates/{trackingId}` | Get one candidate |
| `GET` | `/api/hr/rank` | List candidates ranked by role match |
| `GET` | `/api/hr/filter?skill=java` | Filter candidates by skill |
| `POST` | `/api/hr/compare` | Compare selected candidates with body `{ "ids": ["id1", "id2"] }` |

## How It Works

1. The frontend uploads a PDF resume to the backend.
2. The backend creates a candidate record with `PROCESSING` status in Cosmos DB.
3. The backend uploads the PDF to Azure Blob Storage.
4. The backend extracts text from the PDF using PDFBox.
5. The backend sends extracted text to Groq for ATS analysis.
6. The backend saves scores, skills, summaries, strengths, weaknesses, and role recommendations in Cosmos DB.
7. The frontend polls or refreshes the API and displays completed candidate analysis.

## Build Commands

Frontend production build:

```bash
cd frontend
npm run build
```

Backend package:

```bash
cd backend
mvn clean package
```

Azure Function package:

```bash
cd function_app
mvn clean package
```

## Troubleshooting

- If uploads fail, verify Azure Blob Storage connection string and container permissions.
- If candidates are not saved, verify Cosmos DB endpoint, key, database name, and `Candidates` container.
- If AI analysis fails, verify `GROQ_API_KEY`.
- If the frontend cannot reach the backend, confirm the backend is running on port `8080`.
- If PDF parsing fails, test with a text-based PDF instead of a scanned image-only PDF.

## 📜 License

This project is licensed under the MIT License.

## 👨‍💻 Creator

Powered by Chhattish. This tool was created by Chhattish, a student passionate about cybersecurity, network programming, blockchain developer and ethical hacking. Feel free to ⭐ star this repo if you like it.
