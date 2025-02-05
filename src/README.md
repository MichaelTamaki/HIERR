# Local project installation and setup

```
git clone https://github.com/CodeforHawaii/HIERR.git
cd HIERR
npm install
touch .env
```

- Add the following to the .env:
  1.  DATABASE_URL="file:./db.sqlite"
  2.  NEXTAUTH_URL="http://localhost:3000"
  3.  EMAIL_SERVER={your email server}
  4.  EMAIL_FROM={the email to send the verification link}
  5.  NEXT_PUBLIC_POLIS_SURVEYS='[{"id": "{yourSurveyID1}", "title": "{yourSurveyTitle1}", "description", "{yourSurveyDescription1}"}, ...]'
  6.  NEXT_PUBLIC_SEARCH_API='{your ArcGIS Search Api Key}'
  7.  AUTHORIZED_POLIS_CONVERT_EMAILS_FILE={path to file that contains a list of email addresses (one per line) whose users are authorized to export POLIS data}

```
npx prisma db push
npm run dev
```

# Update Prisma

1. Update schema.prisma with your model

Validate your prisma model

```
npx prisma validate
```

Format the prisma file

```
npx prisma format
```

Generate Prisma Client

```
npx prisma generate
```

Update the database

```
npx prisma db push
```

# SMTP Server Setup

- Gmail
  - Follow the steps at this YouTube video for setting up an SMTP gmail account
    - [Youtube](https://www.youtube.com/watch?v=1YXVdyVuFGA)
  - Fill in the .env variables for EMAIL_SERVER and EMAIL_FROM
    - EMAIL_SERVER="smtps://{username}:{password}@{smtpserver}:{port}"
    - EMAIL_FROM={Email sending the verification link}
- [Brevo (formerly Sendinblue)](https://app.brevo.com)
  - Create an account
  - Fill in the .env variables for EMAIL_SERVER and EMAIL_FROM
    - EMAIL_SERVER=smtp://LOGIN:SMTP_KEY_VALUE@smtp-relay.brevo.com:587
      - Replace LOGIN and SMTP_KEY_VALUE from [SMTP Credentials](https://app.brevo.com/settings/keys/smtp)
    - EMAIL_FROM={Email sending the verification link}
  - Debugging [Log](https://app-smtp.brevo.com/log)

# Prisma SQL Server Migration

- Windows
  - Install SQL Server
    - Docker
      - [Docker Microsoft SQL Server Images Download](https://hub.docker.com/_/microsoft-mssql-server)
        - docker pull mcr.microsoft.com/mssql/server:2017-latest
      - Start SQL Server (2017)
        - docker run -e "ACCEPT_EULA=Y" -e "MSSQL_SA_PASSWORD=yourStrong(!)Password" -p 1433:1433 -d mcr.microsoft.com/mssql/server:2017-latest
    - Microsoft
      - [Miscrosoft SQL Server Download](https://www.microsoft.com/en-us/sql-server/sql-server-downloads)
      - Enable SQL Server TCP/IP
        - Open SQL Server Configuration Manager. (Search for "SQL Server Configuration Manager" in the Start Menu, or open the Start Menu and type "SQL Server Configuration Manager".)
        - In the left-hand panel, click SQL Server Network Configuration > Protocols for MSSQLSERVER
        - Right-click TCP/IP and choose Enable.
  - [Download Microsoft SQL Server Management Studio](https://learn.microsoft.com/en-us/sql/ssms/download-sql-server-management-studio-ssms)
  - Create a database named HIERR
  - In the .env file update the DATABASE_URL variable
  - SQL Server 2019 (local install):
    - sqlserver://localhost:1433;initial catalog=HIERR;integratedSecurity=true;trustServerCertificate=true;
  - SQL Server 2017 (docker):
    - sqlserver://localhost:1433;database=HIERR;user=sa;password=yourStrong(!)Password;encrypt=DANGER_PLAINTEXT;
    - Note: Connection string uses the default user and password from docker. Also the DANGER_PLAINTEXT is used because of an issue with TLS.
  - Other OS systems:
    - sqlserver://HOST:PORT;database=HIERR;user=USER;password=PASSWORD;encrypt=true
    - Note: Be sure to change USER and PASSWORD to your system's requirements
  - Docs: https://www.prisma.io/docs/concepts/database-connectors/sql-server
- Run the following prisma commands
  - `npx prisma migrate dev`
  - `npx prisma db push`
- Run the application and confirm it works

# Working with prisma

When the data model changes, run the following to update your local database with the latest migrations

```
npx prisma migrate dev
```

Docs: https://www.prisma.io/docs/guides/database/developing-with-prisma-migrate/team-development

# Adding Pol.is surveys to the application

In the .env file, add the following environment variable with the survey ids comma separated.

NEXT_PUBLIC_POLIS_SURVEYS='[{"id": "SurveyID1", "title": "SurveyTitle1", "description", "SurveyDescription1"}, ...]'

# Exporting data from Pol.is survey data

Visit http://<hierr server>/polisconvert. You must be an administrator to be able to export data. Upload a participant-votes.csv file when prompted by the form. A CSV with census tract and zip code data from users (joined by xid) will be returned.

# Preparing a new release

1. Open [the Github page to create a new release](https://github.com/CodeforHawaii/HIERR/releases/new).
1. Enter a release title with the date (yyyy-mm-dd) and basic description of changes.
1. Click the "Choose a tag" popup, enter the current date (yyyy-mm-dd) as a tag, and click "Create new tag: yyyy-mm-dd on publish".
1. Click the "Publish release" at the bottom of the page.
1. On the following page, copy the link for "Source code (zip)"
1. Paste that link into the installation instructions [Google Document in the Node.js installation instructions](https://docs.google.com/document/d/1evPMUb8FKiK-BrtP1ILxUfEr9OE8Vwxyew7zUsragCw/edit#heading=h.c5qw6vbumyf6).
