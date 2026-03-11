# AdvanceMD Console

ASP.NET Core MVC + AdminLTE application for testing and operating AdvanceMD XMLRPC and REST APIs with the required login/redirect/token flow.

## Screens


## Purpose

- Authenticate to AdvanceMD using the required redirect process.
- Persist encrypted session/token state per `OfficeCode + AppName`.
- Execute XMLRPC and REST API calls from one UI.
- Auto-refresh tokens and retry once on HTTP `401`.
- Inspect raw request/response payloads.
- Parse common responses (`getupdatedpatients`, `getupdatedvisits`) into data grids.
- Export parsed data to CSV/JSON.

## Project Structure

- Solution: [AdvanceMDConsole.sln](./AdvanceMDConsole.sln)
- Web App: [AdvanceMDConsole.Web](./AdvanceMDConsole.Web)
- Main controller: [AdvanceMDConsole.Web/Controllers/HomeController.cs](./AdvanceMDConsole.Web/Controllers/HomeController.cs)
- API client: [AdvanceMDConsole.Web/Services/AdvanceMdApiClient.cs](./AdvanceMDConsole.Web/Services/AdvanceMdApiClient.cs)
- Main UI: [AdvanceMDConsole.Web/Views/Home/Index.cshtml](./AdvanceMDConsole.Web/Views/Home/Index.cshtml)

## Run

1. Open terminal in `f:\HITI\Projects\AdvanceMD\AdvanceMDConsole\AdvanceMDConsole.Web`
2. Run:
   ```powershell
   dotnet run
   ```
3. Open the local URL shown in console output.

## Operating Sequence (Recommended)

1. Use **1) Login / Saved Session** and click `Login + Redirect`.
2. Verify details in **Active Session**.
3. Run **2) Quick XMLRPC Actions** for standard workflows.
4. Use **3) Raw XMLRPC Call** for custom XML requests.
5. Use **4) REST Bearer Token Call** for REST endpoints.
6. Review **Parsed Patients/Visits** tables and export as needed.
7. Use **Token Refresh Log** to troubleshoot token refresh/retry behavior.

## Screen-Wise Buttons and Functionality

## 1) Login / Saved Session

Fields:
- `Username`
- `Password`
- `Office Code`
- `App Name`
- `Persist credentials for auto-refresh (encrypted store)`

Buttons:
- `Login + Redirect`: Logs in, handles redirect, stores token/session. If persistence is checked, credentials are also stored encrypted for auto-refresh.
- `Load Saved Session`: Loads previously saved session for `Office Code + App Name`.

## Active Session

Shows:
- Office/App
- Redirected webserver
- XMLRPC URL
- Token expiry (UTC)
- Auto-refresh status badge

Buttons:
- `Remove Current Saved Session`: Removes current saved office/app session.

## Token Refresh Log

Shows:
- Timestamped events for proactive refresh, 401 retries, and refresh outcomes.

Buttons:
- `Clear Log`: Clears refresh log history from ASP.NET session.

## 2) Quick XMLRPC Actions

Fields:
- `Action` dropdown (`getupdatedpatients`, `getupdatedvisits`, templates)
- `datechanged` (ISO 8601)

Buttons:
- `Run Quick Action`: Executes generated XMLRPC payload and parses supported responses.

## 3) Raw XMLRPC Call

Fields:
- `XML Payload`

Buttons:
- `Execute Raw XML`: Executes your exact XML payload.

## 4) REST Bearer Token Call

Fields:
- `Family` (`Practice Manager` / `EHR`)
- `Method`
- `Relative Path`
- `JSON Body`

Buttons:
- `Run REST Call`: Calls REST endpoint with bearer token from active session.

## Parsed Patients / Parsed Visits

Buttons:
- `CSV`: Export current parsed grid as CSV.
- `JSON`: Export current parsed grid as JSON.

## Last XMLRPC Request / Response

- Displays last XMLRPC request and response payloads.

## Last REST Request / Response

- Displays last REST request line and response payload.

## Configuration

`AdvanceMDConsole.Web/appsettings.json`:

```json
"AdvanceMd": {
  "Username": "",
  "Password": "",
  "OfficeCode": "",
  "AppName": "TEMP",
  "PersistCredentials": true
}
```

Notes:
- Token/session store file: `AdvanceMDConsole.Web/App_Data/advancemd-session-store.dat`
- Store uses ASP.NET Data Protection encryption.

## How to Add More API Calls

## Option A: No code (fastest)

- Use **Raw XMLRPC Call** and paste payload from API docs.

## Option B: Add to Quick Actions (UI + generated XML)

1. Add new branch in `BuildQuickActionPayload(...)`:
   - [HomeController.cs](./AdvanceMDConsole.Web/Controllers/HomeController.cs)
2. Add action option in dropdown:
   - [Index.cshtml](./AdvanceMDConsole.Web/Views/Home/Index.cshtml)
3. If needed, extend parsers:
   - `ParseXmlGrid(...)`, `ParsePatients(...)`, `ParseVisits(...)`, or new parser methods in `HomeController`.

## Option C: Add typed service method (recommended for scale)

1. Add request/response DTOs in:
   - [AdvanceMDConsole.Web/Models](./AdvanceMDConsole.Web/Models)
2. Add client method in:
   - [AdvanceMdApiClient.cs](./AdvanceMDConsole.Web/Services/AdvanceMdApiClient.cs)
3. Add controller action in:
   - [HomeController.cs](./AdvanceMDConsole.Web/Controllers/HomeController.cs)
4. Add UI card/form in:
   - [Index.cshtml](./AdvanceMDConsole.Web/Views/Home/Index.cshtml)
5. Reuse existing token refresh + 401 retry flow.

## Developer Extension Checklist

1. Define endpoint/action and expected payload/response.
2. Decide XMLRPC vs REST.
3. Implement service call (and typed DTOs if needed).
4. Add controller action and validation.
5. Add/modify UI card and hidden field propagation.
6. Add parsing/export if response should be tabular.
7. Verify refresh behavior (`near expiry`, `401 retry`).
8. Run build:
   ```powershell
   dotnet build
   ```
9. Smoke test end-to-end from login to export.
