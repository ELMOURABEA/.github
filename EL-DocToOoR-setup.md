# EL-DocToOoR Docker Setup and Import Steps (Commands Only)

## 0) Project name

Use **MeGOctoGeN** in any mentions or labels (not ELMOURABEA).

---

## 1) Ensure Docker services are running

From the project root (where `docker-compose.yml` lives):

```bash
docker compose ps
```

Expected services:

- eldoctoor-mysql
- eldoctoor-api
- eldoctoor-frontend

If they are not running:

```bash
cp .env.example .env
docker compose up -d --build
```

---

## 2) Ensure the drugs file is in the correct location

Required path:

```
api/scripts/input/drugs.xlsx
```

Check:

```bash
ls -lh api/scripts/input/
```

If the filename differs, rename it:

```bash
mv "api/scripts/input/<your_file>.xlsx" "api/scripts/input/drugs.xlsx"
```

---

## 3) Run the import (inside the API container)

Copy the file into the API container and run the importer:

```bash
docker cp api/scripts/input/drugs.xlsx eldoctoor-api:/app/scripts/input/drugs.xlsx
docker exec -it eldoctoor-api node scripts/import-drugs-xlsx.js
```

Optional verification inside the container:

```bash
docker exec -it eldoctoor-api sh
ls -lh scripts/input/
exit
```

---

## 4) Verify import success

Health check:

```
http://localhost:8080/health
```

Expected:

```json
{ "ok": true, "service": "eldoctoor-api" }
```

Search check:

```
http://localhost:8080/api/drugs/search?q=para&limit=5
```

Frontend:

```
http://localhost:3000
```

---

## 5) Production domain

If you need to use the hosted environment instead of localhost, open:

```
https://www.eldoctooor.ae
```

---

## 6) Common errors

### Sheet 'Drugs' not found

Ensure the Excel sheet name is exactly `Drugs`.

### ENOENT: no such file or directory ... drugs.xlsx

Copy the file into the container again:

```bash
docker cp api/scripts/input/drugs.xlsx eldoctoor-api:/app/scripts/input/drugs.xlsx
docker exec -it eldoctoor-api node scripts/import-drugs-xlsx.js
```

### MySQL not ready / connection refused

Wait for MySQL to be healthy:

```bash
docker compose ps
docker logs eldoctoor-mysql --tail 50
```

---

## 7) Quick run (paste in order)

```bash
docker compose up -d --build
docker cp api/scripts/input/drugs.xlsx eldoctoor-api:/app/scripts/input/drugs.xlsx
docker exec -it eldoctoor-api node scripts/import-drugs-xlsx.js
```

---

## 8) Windows (PowerShell)

```powershell
cp .env.example .env
docker compose up -d --build
dir api\scripts\input
docker cp api\scripts\input\drugs.xlsx eldoctoor-api:/app/scripts/input/drugs.xlsx
docker exec -it eldoctoor-api node scripts/import-drugs-xlsx.js
```
