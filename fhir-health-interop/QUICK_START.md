# Quick Start Guide

Get up and running with the FHIR Health Interop API in 5 minutes!

## Prerequisites

- Docker & Docker Compose installed
- Bruno API client installed
- Go 1.25+ installed

## Step 1: Start the Server (2 minutes)

```bash
# Navigate to project
cd /Users/nathannewyen/Desktop/projects/fhir-health-interop

# Start databases
docker-compose up -d

# Run server
go run ./cmd/server
```

Server will be available at: `http://localhost:8080`

## Step 2: Open Bruno Collection (1 minute)

1. Open Bruno app
2. Click "Open Collection"
3. Navigate to: `/Users/nathannewyen/Desktop/projects/bruno-collections/fhir-health-interop`
4. Select "local" environment from dropdown

## Step 3: Create Test Data (2 minutes)

### Create a Patient

1. Open `FHIR Health Interop/Patient/create-patient`
2. Click "Send"
3. Copy the `id` from response
4. Click environment dropdown → Edit
5. Set `patientId` to the copied ID
6. Save environment

### Create an Observation

1. Open `FHIR Health Interop/Observation/create-observation`
2. Ensure the request body has your patient ID in subject.reference
3. Click "Send"
4. Copy the `id` from response
5. Set `observationId` in environment

## Step 4: Test Search (1 minute)

Now try these searches:

### Patient Searches
- `Patient/Search/search-by-name` - Find patients by name
- `Patient/Search/search-active-patients` - Filter active patients
- `Patient/Search/search-combined-filters` - Multi-filter example

### Observation Searches
- `Observation/Search/search-by-patient` - Get all observations for patient
- `Observation/Search/search-by-code` - Filter by LOINC code
- `Observation/Search/search-combined-filters` - Multi-filter example

## Common Queries

### Find active female patients born after 1990
```
GET /fhir/Patient?active=true&gender=female&birthdate=ge1990-01-01
```

### Get recent vital signs for patient
```
GET /fhir/Observation?patient={{patientId}}&category=vital-signs&_count=10&_sort=-effective_date
```

### Get final blood pressure readings from 2024
```
GET /fhir/Observation?patient={{patientId}}&code=85354-9&status=final&date=ge2024-01-01
```

## Troubleshooting

### Server won't start
```bash
# Check if containers are running
docker-compose ps

# Restart containers
docker-compose down && docker-compose up -d
```

### Can't connect to API
```bash
# Test health endpoint
curl http://localhost:8080/health
```

### No search results
- Ensure you created test data first
- Check that `patientId` is set in environment
- Verify date formats are YYYY-MM-DD

## Next Steps

- Read [README.md](README.md) for complete documentation
- Explore all search parameters
- Try combining multiple filters
- Test pagination with `_count` and `_offset`

## Quick Reference

### FHIR Date Prefixes
- `eq` - Equal to
- `ge` - Greater than or equal
- `le` - Less than or equal
- `gt` - Greater than
- `lt` - Less than

### Sorting
- `_sort=field` - Ascending
- `_sort=-field` - Descending

### Pagination
- `_count=20` - Page size (max 100)
- `_offset=0` - Skip N results

### Common LOINC Codes
- `8480-6` - Systolic BP
- `8462-4` - Diastolic BP
- `85354-9` - BP Panel
- `8867-4` - Heart Rate
- `8310-5` - Temperature

---

**Need help?** Check the full [README.md](README.md) or server logs:
```bash
docker-compose logs -f
```
