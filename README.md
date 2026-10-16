# AI Chat Application on Google Cloud Run

A lightweight, AI-powered conversational web application built with
**Python, Flask, Docker, Google Cloud Run, and the Gemini API**. The
application accepts user prompts through a web interface, sends them to
Gemini, and displays the generated response.

> **Project focus:** Generative AI integration, Flask web development,
> containerization, and deployment on Google Cloud.

## Table of Contents

-   [Overview](#overview)
-   [Features](#features)
-   [Architecture](#architecture)
-   [How It Works](#how-it-works)
-   [Technology Stack](#technology-stack)
-   [Project Structure](#project-structure)
-   [Prerequisites](#prerequisites)
-   [Run Locally](#run-locally)
-   [Deploy to Google Cloud Run](#deploy-to-google-cloud-run)
-   [Configuration](#configuration)
-   [Security and Cost
    Considerations](#security-and-cost-considerations)
-   [Future Improvements](#future-improvements)
-   [Author](#author)

## Overview

This project demonstrates how to build and deploy a web application that
uses Google's Gemini large language model to answer user questions. A
Flask backend receives the prompt, creates a chat session, calls the
Gemini API, and returns the generated response to the browser.

The application is containerized with Docker. Google Cloud Build builds
the container image, Artifact Registry stores the image, and Cloud Run
runs the container as a managed service.

## Features

-   **AI-powered conversations:** Send prompts to Gemini and display
    generated responses.
-   **Web-based interface:** Enter a prompt and view the response in the
    browser.
-   **Flask backend:** Handles web routes and communication with the
    model.
-   **Containerized deployment:** Packages the application into a Docker
    image.
-   **Managed cloud hosting:** Runs on Google Cloud Run without managing
    servers directly.
-   **Cloud build pipeline:** Uses Cloud Build to build the container
    image.
-   **Image storage:** Uses Artifact Registry to store and retrieve the
    built image.
-   **Environment-based configuration:** Supplies project, region, and
    model settings through environment variables.

## Architecture

### High-level system architecture

``` mermaid
flowchart TB
    USER[User] -->|Enters prompt| UI[Web Interface<br/>HTML + JavaScript]
    UI -->|HTTP request| FLASK[Flask Application<br/>Cloud Run Container]
    FLASK --> SESSION[Create Gemini Chat Session]
    SESSION --> GEMINI[Gemini API]
    GEMINI -->|Generated response| FLASK
    FLASK -->|JSON response| UI
    UI -->|Displays answer| USER

    subgraph GCP["Google Cloud"]
        RUN[Cloud Run]
        BUILD[Cloud Build]
        AR[Artifact Registry]
        RUN --- FLASK
        BUILD -->|Builds container image| AR
        AR -->|Image deployed to| RUN
    end
```

### Deployment pipeline

``` mermaid
flowchart LR
    SRC[Application Source Code] --> CB[Cloud Build]
    CB --> IMG[Docker Container Image]
    IMG --> AR[Artifact Registry]
    AR --> CR[Cloud Run Service]
    CR --> URL[Service URL]
    USER[User] --> URL
```

### Component responsibilities

  -----------------------------------------------------------------------
  Component                           Responsibility
  ----------------------------------- -----------------------------------
  HTML and JavaScript frontend        Collects the prompt, sends the
                                      request, and renders the response.

  Flask application                   Exposes the web routes and
                                      coordinates the request-response
                                      flow.

  Gemini API                          Generates a response based on the
                                      submitted prompt.

  Docker                              Packages the application and its
                                      runtime dependencies into a
                                      container image.

  Cloud Build                         Builds the container image from the
                                      project source.

  Artifact Registry                   Stores the container image used for
                                      deployment.

  Cloud Run                           Runs the container as a managed
                                      service and provides a service URL.

  Environment variables               Configure the Google Cloud project,
                                      region, and Gemini model
                                      identifier.
  -----------------------------------------------------------------------

## How It Works

1.  The user opens the web application and enters a prompt.
2.  The frontend sends the prompt to the Flask `/gemini` route.
3.  Flask creates a Gemini chat session.
4.  The backend sends the user's message to Gemini.
5.  Gemini generates a response.
6.  Flask returns the response as JSON.
7.  The frontend displays the answer to the user.

The application routes described in the project source are:

-   `/` --- serves the main page (`index.html`).
-   `/gemini` --- accepts the user's input, calls the model, and returns
    the generated response as JSON.

## Technology Stack

  Layer                Technology           Purpose
  -------------------- -------------------- --------------------------------------
  Language             Python               Backend application logic
  Web framework        Flask                Routes and HTTP request handling
  Frontend             HTML, JavaScript     Prompt input and response display
  Generative AI        Gemini API           Natural-language response generation
  Containerization     Docker               Packaging the application
  Build service        Google Cloud Build   Building the container image
  Container registry   Artifact Registry    Storing the image
  Deployment           Google Cloud Run     Running the containerized service

## Project Structure

The following is a representative structure. Keep the filenames aligned
with the actual repository:

``` text
AI-Chat-Application-on-Google-Cloud-Run/
├── app.py                 # Flask routes and Gemini integration
├── templates/
│   └── index.html         # Chat interface
├── requirements.txt       # Python dependencies
├── Dockerfile             # Container build instructions
└── README.md
```

If the repository uses a different layout, update this tree to match it.

## Prerequisites

Before running or deploying the project, prepare:

-   Python and `pip`.
-   Git.
-   Docker, if building and running the container locally.
-   A Google Cloud project with billing enabled where required.
-   Google Cloud CLI (`gcloud`).
-   The necessary Google Cloud APIs and permissions for Cloud Run, Cloud
    Build, and Artifact Registry.
-   Access to the Gemini model/API used by the application.

Google Cloud resources can incur charges. Review current pricing and
your project configuration before deployment.

## Run Locally

### 1. Clone the repository

``` bash
git clone https://github.com/ManikaR26/AI-Chat-Application-on-Google-Cloud-Run-.git
cd AI-Chat-Application-on-Google-Cloud-Run-
```

### 2. Create a virtual environment

``` bash
python -m venv .venv
```

Activate it:

**Windows PowerShell**

``` powershell
.venv\Scripts\Activate.ps1
```

**macOS / Linux**

``` bash
source .venv/bin/activate
```

### 3. Install dependencies

``` bash
pip install -r requirements.txt
```

### 4. Configure Google Cloud and model access

Configure the project and model settings expected by the code. The
deployed version uses these environment variable names:

``` bash
GCP_PROJECT_ID=YOUR_PROJECT_ID
GCP_REGION=YOUR_REGION
GEMINI_MODEL_ID=YOUR_MODEL_ID
```

Set them in your local environment using the method appropriate for your
shell. Confirm the exact model identifier and authentication method in
`app.py` and the Google Cloud configuration before running the app.

### 5. Start the Flask application

If `app.py` exposes a Flask application named `app`, run:

``` bash
flask --app app run --debug
```

Open the local URL printed by Flask. If the repository uses a different
entry point, use the command appropriate to that file.

## Deploy to Google Cloud Run

The following steps describe the deployment flow used by the project.
Replace the placeholders with your own Google Cloud project ID, region,
and model identifier.

### 1. Authenticate and select the project

``` bash
gcloud auth login
gcloud config set project YOUR_PROJECT_ID
```

Enable the required APIs in your Google Cloud project if they are not
already enabled.

### 2. Set deployment variables

``` bash
export PROJECT_ID="YOUR_PROJECT_ID"
export REGION="YOUR_REGION"
export GEMINI_MODEL_ID="YOUR_MODEL_ID"
export AR_REPO="chat-app-repo"
export SERVICE_NAME="chat-flask-app"
```

In Windows PowerShell, use `$env:VARIABLE_NAME = "value"` to set
environment variables for the current session.

### 3. Create an Artifact Registry repository

``` bash
gcloud artifacts repositories create "$AR_REPO" \
  --location="$REGION" \
  --repository-format=Docker
```

If the repository already exists, use the existing repository instead of
creating it again.

### 4. Build and upload the container image

Run this command from the directory containing the Dockerfile and
application source:

``` bash
gcloud builds submit \
  --tag "$REGION-docker.pkg.dev/$PROJECT_ID/$AR_REPO/$SERVICE_NAME"
```

Cloud Build builds the image and uploads it to Artifact Registry.

### 5. Deploy to Cloud Run

``` bash
gcloud run deploy "$SERVICE_NAME" \
  --port=8080 \
  --image="$REGION-docker.pkg.dev/$PROJECT_ID/$AR_REPO/$SERVICE_NAME:latest" \
  --region="$REGION" \
  --platform=managed \
  --project="$PROJECT_ID" \
  --set-env-vars="GCP_PROJECT_ID=$PROJECT_ID,GCP_REGION=$REGION,GEMINI_MODEL_ID=$GEMINI_MODEL_ID"
```

The original guided deployment uses `--allow-unauthenticated` to make
the service publicly accessible. Only add that flag if public access is
intended and appropriate for your application. Otherwise, configure
authentication and access controls.

After deployment, open the Cloud Run service URL shown in the command
output and test the chat interface.

## Configuration

Check the actual application code for the precise configuration and
authentication requirements. In particular:

-   Set the correct Google Cloud project ID and region.
-   Use a valid model identifier supported by the configured Gemini
    integration.
-   Ensure the runtime identity has the required permissions.
-   Do not commit service-account keys, API keys, passwords, or other
    credentials.
-   Keep local secrets out of Git using `.gitignore` and use an
    appropriate secrets-management approach for production.

## Security and Cost Considerations

-   Review whether the service should be publicly accessible before
    enabling unauthenticated requests.
-   Apply least-privilege permissions to the identity used by Cloud Run.
-   Avoid logging sensitive prompts or personal information
    unnecessarily.
-   Set appropriate resource limits and monitor service usage.
-   Review Cloud Run, Cloud Build, Artifact Registry, and Gemini usage
    for potential costs.
-   Never commit temporary lab credentials or personal cloud credentials
    to the repository.

## Future Improvements

-   Add conversation history and session persistence.
-   Improve the chat interface with loading indicators and error
    handling.
-   Add authentication for private or personalized conversations.
-   Add request validation, rate limiting, and usage controls.
-   Add automated tests for Flask routes and model integration.
-   Set up a CI/CD workflow for repeatable builds and deployments.
-   Add structured logging and monitoring for failures and latency.

## Author

**Manika Rattan**

-   GitHub: [ManikaR26](https://github.com/ManikaR26)
-   Project repository: [AI Chat Application on Google Cloud
    Run](https://github.com/ManikaR26/AI-Chat-Application-on-Google-Cloud-Run-)
