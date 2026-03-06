This project is a FastAPI-based application that serves as a backend for querying and filtering entities from the ERA-IFF database. It fetches data from a remote source, processes it into a searchable in-memory cache, and exposes a set of RESTful API endpoints for the frontend.

---

## Project Structure

The application is organized into a modular structure to ensure a clear separation of concerns.

```
era_iff_api/
│
├── app/
│   ├── services/
│   │   ├── __init__.py
│   │   ├── data_loader.py    # Handles all logic for fetching and processing data
│   │   └── search.py         # Contains the core business logic for filtering and searching
│   │
│   ├── __init__.py
│   ├── config.py             # Centralized configuration (URLs, credentials, settings)
│   ├── http_client.py        # Configured asynchronous HTTP client with retry logic
│   ├── logging_setup.py      # Sets up structured JSON logging
|   ├── database.py           # for connections with db
│   ├── models.py             # Pydantic models for API requests and data validation
│   ├── routes.py             # Defines all API endpoints (/search, /details, etc.)
│   └── store.py              # Manages the in-memory data cache and application state
│
├── .gitignore                # Specifies files and folders for Git to ignore
├── main.py                   # Application entry point, middleware, and lifespan events
├── README.md                 # This file
└── requirements.txt          # Project dependencies
```

---

## Setup and Installation

Follow these steps to get the application running locally.

### 1. Clone the Repository

```bash
git clone <your-repository-url>
cd era_iff_api
```

### 2. Create a Virtual Environment

It's highly recommended to use a virtual environment to manage project dependencies.

```bash
# For Windows
python -m venv venv
venv\Scripts\activate

# For macOS/Linux
python3 -m venv venv
source venv/bin/activate
```

### 3. Install Dependencies

Install all the required libraries from the `requirements.txt` file.

```bash
pip install -r requirements.txt
```

---

## Running the Application

Once the setup is complete, you can run the application using Uvicorn.

```bash
uvicorn main:app --reload
```

-   The server will start, typically on `http://127.0.0.1:8000`.
-   The `--reload` flag enables auto-reloading, so the server will restart automatically when you save changes to a file.


---

## API Endpoints

The application provides the following main endpoints:

-   `GET /health/ready`: A health check to see if the initial data load is complete. Returns a `503` status until the data is ready.
-   `GET /filters/options`: Provides a list of all available filter options (e.g., all authors, all classifications) for populating frontend dropdowns.
-   `GET /details/{entity}/{entity_id}`: Retrieves the full details for a specific entity.
-   `POST /entities/search`: The main endpoint for performing complex, filtered searches for entities.
-   
## FastAPI docs access:
`http://127.0.0.1:8000/docs`

## Details about Mappings from MongoDB to API (details in MongoDB to API.xlsx)

This section describes how the data stored in MongoDB is exposed and filtered through the FastAPI-based web service. These mappings are essential for the frontend team to correctly request and display data on the official website.

The **"MongoDB to API"** documentation provides a detailed view of every entity available in the system.

### Entity-to-Filter Mapping

For each MongoDB collection, the documentation lists:

- The corresponding **Entity name** used by the API  
- The available **Filter Names** that can be used for querying the data

### Standardized Schema

Most entities follow a consistent filtering schema to ensure predictability and uniform behavior across the API.

### Special Cases & Exceptions

Some entities require specific handling:

- **Visual Object Entity**  
  Includes custom logic for:
  - normal text search
  - proximity text search  
  These differ from the standard filtering system.

- **Entity-Relationship Graphs**  
  These are mapped using a different structural approach to handle the complexity of graph-based data.

### Implementation Examples

To simplify the integration process, the documentation includes **cURL request examples** for each entity. These examples demonstrate how the extracted fields and filters are structured in real API requests.

### Testing the API

For real-time testing and interactive exploration of these mappings, developers should refer to the **FastAPI `/docs` interface (Swagger UI)**.

This interface allows developers to:

- test endpoints directly  
- inspect request schemas  
- validate request bodies defined in the Excel documentation

