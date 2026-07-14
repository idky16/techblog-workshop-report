---
title: "Export the Local TechBlog Database"
date: 2026-07-10
weight: 4
chapter: false
pre: " <b> 5.4.4. </b> "
---

## Objective

Create the local `techblog.sql` dump before EC2 is launched. Upload and import are intentionally deferred to 5.5.3.

## Prerequisites

- MySQL/Laragon is running locally.
- Local database `techblog` contains the verified application data.
- The output location is outside the Hugo repository.

## Steps

1. Open PowerShell on the local Windows machine.
2. Export the database:

```powershell
mysqldump -u root techblog > C:\WORKING\techblog.sql
```

3. Confirm the file exists and is not empty:

```powershell
Get-Item C:\WORKING\techblog.sql
```

4. Keep the dump outside Git and the public report.
5. Do not upload the file or run an RDS import until EC2 is available in 5.5.3.

## Configuration

| Item | Value |
|---|---|
| Source | Local MySQL/Laragon database `techblog` |
| Dump file | `C:\WORKING\techblog.sql` |
| Target later | RDS database `techblog` |
| Current action | Local export only |

## Verification

Check the file size and, if necessary, inspect only schema/table headers locally. Do not paste rows containing user data, password hashes, email addresses, or other personal information into the workshop.

## Expected Result

`C:\WORKING\techblog.sql` is ready for secure transfer after EC2 is created. No command in module 5.4 depends on an EC2 instance.

## Troubleshooting

| Issue | Resolution |
|---|---|
| `mysqldump` is not found | Run it from the MySQL/Laragon binary directory or add that directory to `PATH`. |
| Access is denied locally | Use the correct local MySQL account without recording its password in the report. |
| Dump file is empty | Confirm database `techblog` exists and rerun the export. |

## Cost and Security Notes

The local export has no AWS charge. Treat the SQL dump as sensitive data and never commit or upload it to the report website.

<!-- TODO: Add /images/5-Workshop/5.4-RDS-MySQL/local-database-export.png. Caption: *Figure 5.4.4.1. Local techblog.sql export verified before EC2 creation.* -->
