# rijksmuseum-api-postman-tests
Rijksmuseum Postman collection 

This project is a Postman-based test suite for the [Rijksmuseum API](https://data.rijksmuseum.nl/object-metadata/api). It includes a Postman collection, environment configuration, and a GitHub Actions workflow for continuous integration.

## Setup

### Prerequisites
- [Postman](https://www.postman.com/)
- [Newman](https://www.npmjs.com/package/newman) (for CLI testing)
- Node.js (for GitHub Actions or local Newman use)

### Environment Setup
For CI/CD make sure the ApiKey is set as a github repository secret under value `RIJKSMUSEUM_API_KEY`.
If running locally, replace the ApiKey placeholder in `Rijksmuseum.postman_environment.json`

## Files

- `Rijksmuseum.postman_collection.json`: Main collection with test cases.
- `Rijksmuseum.postman_environment.json`: Environment with API key.
- `.github/workflows/postman-ci.yml`: GitHub Actions workflow.
- `newman-report.html`: Generated after test runs (via CI or Newman CLI).

## Test Case Breakdown

### 1. **Get Collection (Basic)**
- **Purpose**: Test basic connectivity and retrieval of the art collection.
- **Assertions**:
  - Status code = `200 OK`
  - Response contains an array `artObjects`
  - At least one object is returned

### 2. **Get Collection by Artist – Rembrandt**
- **Purpose**: Validate filtering by artist using `involvedMaker=Rembrandt`
- **Assertions**:
  - Status code = `200 OK`
  - Each `artObject.principalOrFirstMaker` includes “Rembrandt”

### 3. **Get Object Details**
- **Purpose**: Retrieve detailed metadata for a specific object ( the `objectNumber`has been hardcoded to `BK-2000-17` for simplicity )
- **Assertions**:
  - Status code = `200 OK`
  - Response contains `artObject` with `title` and `objectNumber`

### 4. **Invalid API Key**
- **Purpose**: Ensure unauthorized access with a bad API key is rejected
- **Assertions**:
  - Status code is `401 Unauthorized` or `403 Forbidden`

### 5. **Invalid Object Number**
- **Purpose**: Confirm behavior for nonexistent objects
- **Assertions**:
  - Status code is `404` or `200` with an invalid object format

## CI/CD Integration

The included GitHub Actions workflow:
- Installs Newman
- Runs the Postman test suite
- Exports an HTML report (`newman-report.html`)
- Uploads the report as an artifact

### Run it locally:
```bash
# make sure the ApiKey placeholder in `Rijksmuseum.postman_environment.json` has been replaced
# with a correct api key
newman run Rijksmuseum.postman_collection.json \
  --environment Rijksmuseum.postman_environment.json \
  --reporters cli,html \
  --reporter-html-export newman-report.html
```