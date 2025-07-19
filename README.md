# Patient Management System API

## Overview
This is a FastAPI-based application for managing patient records. It allows users to create, read, update, and delete patient information stored in a JSON file. The API includes validation using Pydantic models and supports sorting and querying patient data.

## Features
- **Create Patients**: Add new patient records with details like ID, name, city, age, gender, height, and weight.
- **Read Patients**: Retrieve all patients or a specific patient by ID.
- **Update Patients**: Modify existing patient records with optional fields.
- **Delete Patients**: Remove patient records by ID.
- **Sort Patients**: Sort patient records by height, weight, or BMI in ascending or descending order.
- **Computed Fields**: Automatically calculate BMI and health verdict (Underweight, Normal, Obese) for each patient.

## Prerequisites
- Python 3.8+
- FastAPI (`pip install fastapi`)
- Uvicorn (`pip install uvicorn`)
- Pydantic (`pip install pydantic`)

## Installation
1. Clone the repository:
   ```bash
   git clone <repository-url>
   cd <repository-directory>
   ```
2. Install dependencies:
   ```bash
   pip install -r requirements.txt
   ```
3. Create a `patients.json` file in the project root to store patient data (an empty JSON object `{}` is sufficient to start).

## Running the API
Run the FastAPI server using Uvicorn:
```bash
uvicorn main:app --reload
```
The API will be available at `http://127.0.0.1:8000`. Access the interactive API documentation at `http://127.0.0.1:8000/docs`.

## API Endpoints

### General
- **GET /**: Returns a welcome message for the Patient Management System API.
- **GET /about**: Provides a brief description of the API.

### Patient Management
- **GET /view**: Retrieves all patient records.
- **GET /patient/{patient_id}**: Retrieves a specific patient by ID.
  - **Path Parameter**: `patient_id` (e.g., `P001`)
  - **Status Codes**: 200 (OK), 404 (Not Found)
- **GET /sort**: Sorts patients by `height`, `weight`, or `bmi`.
  - **Query Parameters**:
    - `sort_by`: Field to sort by (`height`, `weight`, `bmi`)
    - `order`: Sort order (`asc` or `desc`)
  - **Status Codes**: 200 (OK), 400 (Bad Request)
- **POST /create**: Creates a new patient record.
  - **Request Body**: JSON with `id`, `name`, `city`, `age`, `gender`, `height`, `weight`
  - **Status Codes**: 201 (Created), 400 (Bad Request)
- **PUT /edit/{patient_id}**: Updates an existing patient record.
  - **Path Parameter**: `patient_id`
  - **Request Body**: JSON with optional fields (`name`, `city`, `age`, `gender`, `height`, `weight`)
  - **Status Codes**: 200 (OK), 404 (Not Found)
- **DELETE /delete/{patient_id}**: Deletes a patient record by ID.
  - **Path Parameter**: `patient_id`
  - **Status Codes**: 200 (OK), 404 (Not Found)

## Data Model
The API uses Pydantic models for validation:
- **Patient**:
  - `id`: String (required, e.g., `P001`)
  - `name`: String (required)
  - `city`: String (required)
  - `age`: Integer (required, >0, <120)
  - `gender`: Literal (`male`, `female`, `others`)
  - `height`: Float (required, >0, in meters)
  - `weight`: Float (required, >0, in kilograms)
  - Computed fields:
    - `bmi`: Calculated as `weight / (height^2)`, rounded to 2 decimal places
    - `verdict`: Health status based on BMI (`Underweight`, `Normal`, `Obese`)
- **PatientUpdate**: Same fields as `Patient`, but all are optional for partial updates.

## Data Storage
Patient data is stored in a `patients.json` file in the project root. The file is read and written for each operation, ensuring persistence.

## HTTP Status Codes
- **200 OK**: Request successful.
- **201 Created**: Resource created successfully.
- **400 Bad Request**: Invalid input or parameters.
- **404 Not Found**: Resource not found.
- **500 Internal Server Error**: Unexpected server error.

## Query Parameters
Used in the `/sort` endpoint to filter and sort data:
- Format: `?key=value&key2=value2`
- Example: `/sort?sort_by=height&order=desc`

## Example Usage
### Create a Patient
```bash
curl -X POST "http://127.0.0.1:8000/create" -H "Content-Type: application/json" -d '{
  "id": "P001",
  "name": "John Doe",
  "city": "New York",
  "age": 30,
  "gender": "male",
  "height": 1.75,
  "weight": 70
}'
```

### View a Patient
```bash
curl "http://127.0.0.1:8000/patient/P001"
```

### Sort Patients
```bash
curl "http://127.0.0.1:8000/sort?sort_by=bmi&order=asc"
```

### Update a Patient
```bash
curl -X PUT "http://127.0.0.1:8000/edit/P001" -H "Content-Type: application/json" -d '{
  "name": "John Smith",
  "weight": 72
}'
```

### Delete a Patient
```bash
curl -X DELETE "http://127.0.0.1:8000/delete/P001"
```

## Notes
- The API uses a JSON file for simplicity. For production, consider using a database like PostgreSQL or MongoDB.
- Validation is enforced via Pydantic, ensuring data integrity.
- The `/sort` endpoint recalculates BMI and verdict for accurate sorting.
