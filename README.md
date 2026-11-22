# Assignment 12: FastAPI Calculator, Users + Calculations (BREAD)

Docker Hub: https://hub.docker.com/repository/docker/shakawathh/assignment12/general

## Run the app
```bash
docker compose up --build
```
Open http://localhost:8000/docs (or /redoc). Make sure nothing else is using port 8000.

## Run the tests locally
Tests need Postgres. Start only the database (this avoids the port 8000 conflict):
```bash
docker compose up -d db
python3 -m venv venv && source venv/bin/activate
pip install -r requirements.txt
playwright install chromium
pytest
```
`DATABASE_URL` defaults to `postgresql://postgres:postgres@localhost:5432/fastapi_db`. The test session drops and recreates tables, so don't point it at data you care about.

## Manual checks with OpenAPI (/docs)
1. `POST /users/register` with first_name, last_name, email, username, password (8+ chars with upper, lower, digit and special), confirm_password. Expect 201.
2. `POST /users/login` with username and password. Expect 200 and tokens.
3. Click **Authorize**, enter the username and password, and click Authorize.
4. `POST /calculations` with `{"type": "addition", "inputs": [10.5, 3, 2]}`. Expect 201.
5. `GET /calculations` and `GET /calculations/{id}` return 200.
6. `PUT /calculations/{id}` with `{"inputs": [42, 7]}` returns 200 with a new result.
7. `DELETE /calculations/{id}` returns 204, and a follow-up GET returns 404.
8. Error checks: a duplicate register gives 400, a wrong password 401, `{"type":"division","inputs":[1,0]}` gives 422.

## CI/CD
GitHub Actions (`.github/workflows/test.yml`) runs unit, integration and e2e tests against a Postgres service, scans the image with Trivy, and pushes to Docker Hub on success.