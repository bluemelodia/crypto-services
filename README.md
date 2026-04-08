# Crypto Services (Workshop)

This repository contains two minimal Java Spring Boot HTTP services for a workshop outage simulation.
Each service exposes a single endpoint: `/process` which returns a JSON payload.

- Service A (crypto-svc-one): returns a confirmationNumber + generic fields (no `limit`).
- Service B (crypto-svc-two): returns confirmationNumber + generic fields + `limit`.

**Goal for attendees:** Run the services locally with Docker and observe that one service lacks the `limit` field.

## Prerequisites (attendees)
- Docker Desktop installed and running
- curl (or browser)

## Run locally (using Docker)
Build and run Service A:
```bash
cd service-a
docker build -t crypto-svc-one .
docker run --rm -p 8081:8080 crypto-svc-one
```
Visit: http://localhost:8081/api/transaction

Build and run Service B (in a separate terminal):
```bash
cd service-b
docker build -t crypto-svc-two .
docker run --rm -p 8082:8080 crypto-svc-two
```
Visit: http://localhost:8082/api/transaction

**Notes for attendees**
- The two services are intentionally separate. When running locally, use ports 8081 (service-a) and 8082 (service-b) to avoid conflicts.
- The JSON payloads differ: service-a does **not** include `limit`. This is the observable difference for the workshop.
- Do not assume service names reveal which is "old" or "new".

## Instructor (deployment to Fly.io)
Instructor-only steps (not in attendee README):
Note: Some app names like crypto-svc-one and crypto-svc-two may already be taken globally. To avoid errors, we will use crypto-svc-v1 and crypto-svc-v2 for Fly deployment.

- Create GitHub repo and push these files.
- Use `flyctl` to create apps and deploy from the respective service folders:
  ```
cd service-a
# Launch the app (without deploying yet)
fly launch --no-deploy --name crypto-svc-v1
# Deploy the app
fly deploy --app crypto-svc-v1 --no-cache

cd ../service-b
# Launch the app (without deploying yet)
fly launch --no-deploy --name crypto-svc-v2
# Deploy the app
fly deploy --app crypto-svc-v2 --no-cache
  ```
- After deployment, note the public URLs and share them with attendees as the "prod" endpoints.
- To simulate outage of the service that lacks `limit`, scale it to zero:
  ```
  fly scale count 0 --app crypto-svc-one
  ```
  and scale back up with:
  ```
  fly scale count 1 --app crypto-svc-one
  ```

## File layout
- service-a/  (crypto-svc-one)
- service-b/  (crypto-svc-two)

---
