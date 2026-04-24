# DataAI web.config Configuration Guide

This guide explains how to configure the `web.config` file for your DataAI installation. Every setting you need to change is marked with `[placeholder text]` in the default file.

**Before you start**, make a backup:

```
copy web.config web.config.backup
```

---

## Step 1: Connection Strings

The `<connectionStrings>` section is where you tell DataAI how to reach your database. The file ships with all database templates commented out inside `<!-- ... -->`. You need to uncomment exactly one block that matches your database engine.

**Do not change the order of the three connection names.** They must always appear in this order:

1. **MySqlConnection** — DataAI operational database (stores reports, users, configuration)
2. **UserSqlConnection** — your data database (the data you want to analyze)
3. **CSVconnection** — used for CSV import (typically same as UserSqlConnection)

Pick your database engine below, uncomment its three lines, and replace the placeholders.

### MySQL

```xml
<add name="MySqlConnection"
     connectionString="Server=YOUR_SERVER; Database=YOUR_OPERATIONAL_DB; User ID=YOUR_USER; Password=YOUR_PASSWORD;"
     providerName="MySql.Data.MySqlClient" />
<add name="UserSqlConnection"
     connectionString="Server=YOUR_DATA_SERVER; Database=YOUR_DATA_DB; User ID=YOUR_USER; Password=YOUR_PASSWORD;"
     providerName="MySql.Data.MySqlClient" />
<add name="CSVconnection"
     connectionString="Server=YOUR_DATA_SERVER; Database=YOUR_DATA_DB; User ID=YOUR_USER; Password=YOUR_PASSWORD;"
     providerName="MySql.Data.MySqlClient" />
```

### SQL Server

```xml
<add name="MySqlConnection"
     connectionString="Server=YOUR_SERVER; Database=YOUR_OPERATIONAL_DB; User ID=YOUR_USER; Password=YOUR_PASSWORD; Trusted_Connection=True"
     providerName="System.Data.SqlClient" />
<add name="UserSqlConnection"
     connectionString="Server=YOUR_DATA_SERVER; Database=YOUR_DATA_DB; User ID=YOUR_USER; Password=YOUR_PASSWORD; Trusted_Connection=True"
     providerName="System.Data.SqlClient" />
<add name="CSVconnection"
     connectionString="Server=YOUR_DATA_SERVER; Database=YOUR_DATA_DB; User ID=YOUR_USER; Password=YOUR_PASSWORD; Trusted_Connection=True"
     providerName="System.Data.SqlClient" />
```

### PostgreSQL

Default port is 5432.

```xml
<add name="MySqlConnection"
     connectionString="Server=YOUR_SERVER; Port=5432; Database=YOUR_OPERATIONAL_DB; User ID=YOUR_USER; Password=YOUR_PASSWORD;"
     providerName="Npgsql" />
<add name="UserSqlConnection"
     connectionString="Server=YOUR_DATA_SERVER; Port=5432; Database=YOUR_DATA_DB; User ID=YOUR_USER; Password=YOUR_PASSWORD;"
     providerName="Npgsql" />
<add name="CSVconnection"
     connectionString="Server=YOUR_DATA_SERVER; Port=5432; Database=YOUR_DATA_DB; User ID=YOUR_USER; Password=YOUR_PASSWORD;"
     providerName="Npgsql" />
```

### Oracle

Oracle also requires a fourth `SystemSqlConnection` line.

```xml
<add name="SystemSqlConnection"
     connectionString="Data Source=YOUR_DATA_SOURCE; User ID=SYS; Password=YOUR_SYS_PASSWORD; DBA Privilege=SYSDBA"
     providerName="Oracle.ManagedData.Client" />
<add name="MySqlConnection"
     connectionString="Data Source=YOUR_DATA_SOURCE; User ID=YOUR_USER; Password=YOUR_PASSWORD;"
     providerName="Oracle.ManagedData.Client" />
<add name="UserSqlConnection"
     connectionString="Data Source=YOUR_DATA_SOURCE; User ID=YOUR_USER; Password=YOUR_PASSWORD;"
     providerName="Oracle.ManagedData.Client" />
<add name="CSVconnection"
     connectionString="Data Source=YOUR_DATA_SOURCE; User ID=YOUR_USER; Password=YOUR_PASSWORD;"
     providerName="Oracle.ManagedData.Client" />
```

**License required:** Oracle Database requires a valid commercial license from Oracle Corporation. DataAI does not provide or distribute Oracle software. Visit https://www.oracle.com for licensing information.

### InterSystems IRIS

Default port is 1972 or 51773. IRIS also requires a `SystemSqlConnection` line.

```xml
<add name="SystemSqlConnection"
     connectionString="Server=YOUR_SERVER; Port=1972; Namespace=%SYS; User ID=_SYSTEM; Password=YOUR_SYS_PASSWORD"
     providerName="InterSystems.Data.IRISClient" />
<add name="MySqlConnection"
     connectionString="Server=YOUR_SERVER; Port=1972; Namespace=YOUR_NAMESPACE; User ID=YOUR_USER; Password=YOUR_PASSWORD"
     providerName="InterSystems.Data.IRISClient" />
<add name="UserSqlConnection"
     connectionString="Server=YOUR_DATA_SERVER; Port=1972; Namespace=YOUR_NAMESPACE; User ID=YOUR_USER; Password=YOUR_PASSWORD"
     providerName="InterSystems.Data.IRISClient" />
<add name="CSVconnection"
     connectionString="Server=YOUR_DATA_SERVER; Port=1972; Namespace=YOUR_NAMESPACE; User ID=YOUR_USER; Password=YOUR_PASSWORD"
     providerName="InterSystems.Data.IRISClient" />
```

**License required:** InterSystems IRIS requires a valid commercial license from InterSystems Corporation. DataAI does not provide or distribute InterSystems software. Visit https://www.intersystems.com for licensing information.

### InterSystems Caché

Same format as IRIS, but use `providerName="InterSystems.Data.CacheClient"` on each line. Default port is 1972.

**License required:** InterSystems Caché requires a valid commercial license from InterSystems Corporation.

---

## Step 2: Application Identity and URLs

In the `<appSettings>` section, update these values:

```xml
<add key="pagettl" value="DataAI - Data Analytical Intelligence" />
```

Set `pagettl` to the title you want displayed in the browser tab.

```xml
<add key="webour" value="https://yourserver/DataAI/" />
<add key="weboureports" value="https://yourserver/DataAI/" />
<add key="webhelpdesk" value="https://yourserver/DataAI/" />
```

Set all three to the full URL where your DataAI site is hosted.

```xml
<add key="OUReportsServer" value="your-db-server-hostname" />
```

Set this to the hostname or IP of your operational database server.

---

## Step 3: Email (SMTP)

DataAI sends email for user registration and support tickets. You need to configure it in two places.

**In `<appSettings>`:**

```xml
<add key="SmtpCred" value="smtp.gmail.com" />
<add key="smtpemail" value="noreply@yourcompany.com" />
<add key="smtpemailpass" value="your-app-password" />
<add key="supportemail" value="support@yourcompany.com" />
```

**In `<system.net>` / `<mailSettings>`:**

```xml
<smtp deliveryMethod="Network" from="noreply@yourcompany.com">
  <network defaultCredentials="false"
           host="smtp.gmail.com"
           password="your-app-password"
           port="587"
           userName="noreply@yourcompany.com"
           enableSsl="true" />
</smtp>
```

Make sure both locations use the same email address and password.

**Gmail users:** You must generate an App Password at https://myaccount.google.com/apppasswords (requires 2-Step Verification enabled). Your regular Gmail password will not work.

---

## Step 4: File Upload Folder

```xml
<add key="fileupload" value="C:\DataAI\uploads\" />
```

Set this to a folder on the server where DataAI can store uploaded CSV and data files. The IIS application pool identity must have read and write permissions on this folder.

---

## Step 5: Google Maps API Key (optional)

Only needed if your reports use geographic map visualizations.

```xml
<add key="mapkey" value="AIzaSy..." />
```

Get a key at https://console.cloud.google.com/google/maps-api. If you are not using maps, leave the placeholder value.

---

## Step 6: OpenAI Integration (optional)

Only needed if you want AI-powered data analysis and the ChatAI feature.

```xml
<add key="openaikey" value="sk-proj-..." />
<add key="openaiorganization" value="org-..." />
<add key="apiURL" value="https://api.openai.com/v1/chat/completions" />
<add key="openaimodel" value="gpt-4o" />
<add key="openaimaxTokens" value="128000" />
```

Available models: `gpt-4o`, `gpt-4o-mini`. Get your API key at https://platform.openai.com/api-keys. The `apiURL` is already set to the correct default and usually does not need to change.

If you are not using AI features, leave the placeholder values.

---

## Step 7: Database Provider Flags

Set the provider flag to `OK` for each database engine your installation can use. Leave the others empty.

```xml
<add key="MySqlProv" value="OK" />       <!-- MySQL -->
<add key="SQLServerProv" value="OK" />   <!-- SQL Server -->
<add key="CacheProv" value="" />         <!-- InterSystems Caché -->
<add key="IRISProv" value="" />          <!-- InterSystems IRIS -->
<add key="CSVProv" value="OK" />         <!-- CSV flat file import -->
<add key="Oracle" value="" />            <!-- Oracle -->
<add key="Npgsql" value="OK" />          <!-- PostgreSQL -->
<add key="ODBC" value="" />              <!-- ODBC generic -->
<add key="OleDb" value="" />             <!-- OLE DB generic -->
```

The default file ships with MySQL, SQL Server, CSV, and PostgreSQL enabled. Adjust to match the database drivers installed on your server.

---

## Step 8: Database Case Sensitivity

These control how DataAI formats table and column names in SQL queries. Set them to match your database engine.

```xml
<add key="ourdbcase" value="lower" />   <!-- operational database -->
<add key="csvdbcase" value="lower" />   <!-- CSV import database -->
<add key="userdbcase" value="" />       <!-- user data database -->
```

Recommended values by database engine:

| Database | Recommended Setting |
|----------|-------------------|
| MySQL | `lower` |
| PostgreSQL | `lower` |
| SQL Server | `mix` |
| Oracle | `upper` |
| InterSystems IRIS / Caché | `upper` |

---

## Settings You Usually Don't Need to Change

| Key | Default | What It Does |
|-----|---------|--------------|
| `webinstall` | `OURweb` | Internal install marker |
| `dbinstall` | `OURdb` | Internal install marker |
| `unit` | `OUR` | Unit identifier |
| `unitenddate` | `2040-12-31 23:59:00` | License expiration |
| `UnitAuthenticate` | `NO` | Unit-level authentication toggle |
| `maxretries` | `5` | API call retry attempts |
| `SiteFor` | `Production` | Environment label |
| `DaysFree` | `2000` | Free trial days for new users |
| `version` | `34-00` | DataAI version (do not change) |
| `dataaidownpay` | `$10` | Download fee (if payments enabled) |
| `dataaidbpay` | `$100` | Database setup fee |
| `ACEOLEDBversion` | `Provider=Microsoft.ACE.OLEDB.16.0;` | ACE driver for Excel/Access imports |

---

## Quick Checklist

After editing, verify you have completed each step:

- [ ] Uncommented exactly one database block in `<connectionStrings>` with your credentials filled in
- [ ] Set `pagettl` to your site title
- [ ] Set `webour`, `weboureports`, `webhelpdesk` to your site URL
- [ ] Set `OUReportsServer` to your database server hostname
- [ ] Configured SMTP email in both `<appSettings>` and `<system.net>`
- [ ] Set `fileupload` to a writable folder path
- [ ] Set `mapkey` (if using map reports)
- [ ] Set `openaikey` and `openaimodel` (if using AI features)
- [ ] Enabled the correct database provider flags
- [ ] Set `ourdbcase`, `csvdbcase`, `userdbcase` for your database engine
- [ ] Saved the file and restarted the IIS application pool

---

## Troubleshooting

**"Could not load file or assembly 'MySql.Data'"** — Place `MySql.Data.dll` (version 8.0.30) in your DataAI `Bin` folder.

**Connection timeout errors** — Increase `executionTimeout` in `<httpRuntime>` (default is 36000 seconds = 10 hours).

**Large file uploads failing** — Increase `maxRequestLength` in `<httpRuntime>` (default is 8192 KB = 8 MB, maximum is 2147483647).

**Emails not sending** — Verify the email and password match in both `<appSettings>` and `<system.net>`. For Gmail, use an App Password.

**IIS 500 errors** — Check that `debug="true"` is set in `<compilation>` to see detailed errors. Verify `validateIntegratedModeConfiguration="false"` is present in `<system.webServer>`.

**Wrong column/table names in queries** — Adjust `ourdbcase`, `csvdbcase`, and `userdbcase` to match your database's case sensitivity rules.
