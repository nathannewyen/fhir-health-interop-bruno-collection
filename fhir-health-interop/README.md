# FHIR Health Interop API - Bruno Collection

Comprehensive API testing collection for the FHIR Health Interoperability API built with Go.

## Overview

This Bruno collection provides complete coverage for testing FHIR R4 Patient and Observation resources with advanced search capabilities.

## Collection Structure

```
FHIR Health Interop/
├── Health/
│   └── health-check.bru              # API health check endpoint
├── Patient/
│   ├── create-patient.bru            # POST new patient
│   ├── get-patient-by-id.bru         # GET patient by ID
│   ├── get-all-patients.bru          # GET all patients (uses search)
│   ├── get-sample-patient.bru        # GET sample patient data
│   └── Search/
│       ├── search-by-name.bru        # Search by name (partial match)
│       ├── search-by-gender.bru      # Filter by gender
│       ├── search-by-birthdate-range.bru  # Date range filtering
│       ├── search-active-patients.bru     # Filter by active status
│       └── search-combined-filters.bru    # Multi-filter example
└── Observation/
    ├── create-observation.bru        # POST new observation
    ├── get-observation-by-id.bru     # GET observation by ID
    ├── get-observations-by-patient.bru    # GET by patient (uses search)
    └── Search/
        ├── search-by-patient.bru     # Filter by patient ID
        ├── search-by-code.bru        # Filter by LOINC code
        ├── search-by-date-range.bru  # Date range filtering
        ├── search-by-status.bru      # Filter by status
        ├── search-by-category.bru    # Filter by category
        └── search-combined-filters.bru    # Multi-filter example
```

## Environments

### Local Environment (Default)
- **Base URL**: `http://localhost:8080`
- Used for local development and testing
- No authentication required

### Production Environment
- **Base URL**: `https://api.yourdomain.com`
- Update with your production URL
- Add API keys/tokens in secret variables

## Environment Variables

### Common Variables
| Variable | Description | Example |
|----------|-------------|---------|
| `baseUrl` | API base URL | `http://localhost:8080` |
| `patientId` | Patient ID for testing | `123` |
| `observationId` | Observation ID for testing | `6921098b24a75c75762b39e1` |

### Search Variables
| Variable | Description | Default |
|----------|-------------|---------|
| `searchName` | Name to search for | `Smith` |
| `searchGender` | Gender filter | `female` |
| `searchStatus` | Observation status | `final` |
| `searchCode` | LOINC code | `8480-6` (Systolic BP) |
| `searchCategory` | Observation category | `vital-signs` |

## Getting Started

### 1. Start the API Server

```bash
cd /Users/nathannewyen/Desktop/projects/fhir-health-interop

# Start Docker containers
docker-compose up -d

# Run the server
go run ./cmd/server
```

The API will be available at `http://localhost:8080`

### 2. Open Collection in Bruno

1. Open Bruno app
2. Open Collection → Select folder:
   `/Users/nathannewyen/Desktop/projects/bruno-collections/fhir-health-interop`
3. Select "local" environment from dropdown

### 3. Test Basic Endpoints

1. Run `Health/health-check` to verify API is running
2. Run `Patient/create-patient` to create a test patient
3. Copy the returned patient ID
4. Set `patientId` variable in environment
5. Run `Observation/create-observation` to create test data

### 4. Test Search Functionality

Now you can explore all the search endpoints with real data!

## Patient Search Parameters

### Basic Filters
- `name` - Search both given and family name (partial, case-insensitive)
- `family` - Search family name only
- `given` - Search given name only
- `gender` - Filter by gender (`male`, `female`, `other`, `unknown`)
- `active` - Filter by active status (`true`/`false`)

### Date Filters
- `birthdate` - Birth date with prefixes:
  - `eq` - Equal to (e.g., `birthdate=eq1990-01-15`)
  - `ge` - Greater than or equal (e.g., `birthdate=ge1990-01-01`)
  - `le` - Less than or equal (e.g., `birthdate=le2000-12-31`)
  - `gt` - Greater than
  - `lt` - Less than

### Sorting & Pagination
- `_sort` - Sort field (prefix with `-` for descending)
  - Options: `name`, `family_name`, `given_name`, `birthdate`, `gender`, `created_at`
  - Examples: `_sort=name` (asc), `_sort=-created_at` (desc)
- `_count` - Results per page (max: 100, default: 10)
- `_offset` - Number of results to skip (for pagination)

### Example Patient Queries

```
# Active patients named Smith
GET /fhir/Patient?active=true&name=Smith

# Female patients born after 1990
GET /fhir/Patient?gender=female&birthdate=ge1990-01-01

# Recent patients, sorted by creation date
GET /fhir/Patient?_sort=-created_at&_count=20

# Paginated results (page 2, 25 per page)
GET /fhir/Patient?_count=25&_offset=25
```

## Observation Search Parameters

### Basic Filters
- `patient` - Patient ID (supports `123` or `Patient/123` format)
- `code` - LOINC code (exact match, e.g., `8480-6`)
- `category` - Observation category (e.g., `vital-signs`, `laboratory`)
- `status` - Observation status (e.g., `final`, `preliminary`)

### Date Filters
- `date` - Effective date with prefixes:
  - `ge` - Greater than or equal (e.g., `date=ge2024-01-01`)
  - `le` - Less than or equal (e.g., `date=le2024-12-31`)
  - `gt`, `lt`, `eq` - Also supported

### Sorting & Pagination
- `_sort` - Sort field (prefix with `-` for descending)
  - Options: `effective_date`, `code`, `status`, `created_at`
  - Examples: `_sort=-effective_date` (newest first)
- `_count` - Results per page (max: 100, default: 10)
- `_offset` - Number of results to skip

### Common LOINC Codes

#### Vital Signs
- `85354-9` - Blood Pressure Panel
- `8480-6` - Systolic Blood Pressure
- `8462-4` - Diastolic Blood Pressure
- `8867-4` - Heart Rate
- `8310-5` - Body Temperature
- `9279-1` - Respiratory Rate
- `2708-6` - Oxygen Saturation

#### Body Measurements
- `29463-7` - Body Weight
- `8302-2` - Body Height
- `39156-5` - Body Mass Index (BMI)

#### Laboratory
- `2093-3` - Total Cholesterol
- `2571-8` - Triglycerides
- `2339-0` - Glucose
- `6299-2` - Blood Urea Nitrogen

### Example Observation Queries

```
# Recent vital signs for patient
GET /fhir/Observation?patient=123&category=vital-signs&_count=10&_sort=-effective_date

# Final blood pressure readings from 2024
GET /fhir/Observation?patient=123&code=85354-9&status=final&date=ge2024-01-01

# All laboratory results for patient
GET /fhir/Observation?patient=123&category=laboratory&_count=50

# Temperature readings
GET /fhir/Observation?patient=123&code=8310-5&status=final
```

## Tips & Best Practices

### 1. Using Variables
After creating resources, save their IDs to environment variables:
- Right-click response → Save to Environment → Select variable

### 2. Chaining Requests
1. Create a patient
2. Save returned `id` to `{{patientId}}`
3. Create observations using `{{patientId}}`
4. Search observations by patient

### 3. Testing Search Combinations
Start simple and add filters incrementally:
1. `?patient=123`
2. `?patient=123&category=vital-signs`
3. `?patient=123&category=vital-signs&status=final`
4. `?patient=123&category=vital-signs&status=final&date=ge2024-01-01`

### 4. Pagination Strategy
For large datasets:
```
Page 1: ?_count=20&_offset=0
Page 2: ?_count=20&_offset=20
Page 3: ?_count=20&_offset=40
```

### 5. Date Range Searches
For a specific date range, use one parameter at a time:
- Start date: `?date=ge2024-01-01` (on or after Jan 1)
- End date: `?date=le2024-12-31` (on or before Dec 31)

Note: The current implementation processes these separately. For true ranges, adjust implementation or make separate queries.

## Troubleshooting

### API Not Responding
```bash
# Check if server is running
curl http://localhost:8080/health

# Check Docker containers
docker-compose ps

# Check server logs
docker-compose logs -f
```

### Database Connection Issues
```bash
# Restart containers
docker-compose down
docker-compose up -d

# Check PostgreSQL
docker exec -it fhir-postgres psql -U fhir_user -d fhir_health_db -c "SELECT COUNT(*) FROM patients;"

# Check MongoDB
docker exec -it fhir-mongodb mongosh --username fhir_user --password fhir_password --authenticationDatabase admin
```

### Empty Search Results
1. Ensure you have test data created
2. Check filter values match your data
3. Verify patient IDs are correct
4. Check date formats (use YYYY-MM-DD)

## API Documentation

### Base URL
```
http://localhost:8080
```

### Endpoints

#### Health Check
```
GET /health
```

#### Patient CRUD
```
POST   /fhir/Patient           # Create patient
GET    /fhir/Patient/{id}      # Get by ID
GET    /fhir/Patient           # Search patients (with query params)
PUT    /fhir/Patient/{id}      # Update patient (planned)
DELETE /fhir/Patient/{id}      # Delete patient (planned)
```

#### Observation CRUD
```
POST   /fhir/Observation       # Create observation
GET    /fhir/Observation/{id}  # Get by ID
GET    /fhir/Observation       # Search observations (with query params)
PUT    /fhir/Observation/{id}  # Update observation (planned)
DELETE /fhir/Observation/{id}  # Delete observation (planned)
```

## FHIR Compliance

This API implements:
- **FHIR R4** specification
- **Search parameters** per FHIR spec
- **Date prefixes** (ge, le, gt, lt, eq)
- **Pagination** (_count, _offset)
- **Sorting** (_sort)

## Support

For issues or questions:
- Check the `/health` endpoint first
- Review server logs: `docker-compose logs -f`
- Verify environment variables are set
- Ensure Docker containers are running

## Version

- Collection Version: 1.0
- FHIR Version: R4
- API Version: 0.1.0

---

**Last Updated**: November 2024
**Author**: Nathan Newyen
**License**: MIT
