# Extractinator

> **Turn publicly accessible web interfaces into developer-ready APIs.**

Extractinator is an open-source web intelligence and API engineering toolkit for inspecting public HTTP traffic, discovering accessible endpoints, analyzing recorded flows, generating local APIs, and exporting requests to Postman/OpenAPI.

It combines a request laboratory, endpoint discovery, HAR analysis, flow inspection, API generation, replay, extraction, and optional mitmproxy integration into a single developer-focused workspace.

---

## What is Extractinator?

Modern websites often communicate through HTTP endpoints that are not documented as public APIs.

Extractinator helps developers understand and work with those **publicly accessible interfaces** by providing a controlled workflow:

```text
Public Website
      │
      ▼
Request / Browser Traffic
      │
      ▼
Extractinator
      │
 ┌────┼─────────────────────┐
 ▼    ▼                     ▼
Inspect  Discover        Capture
 │       Endpoints       HTTP Flows
 └────┼─────────────────────┘
      ▼
Analyze / Replay
      │
      ▼
Generate API
      │
      ▼
OpenAPI / Postman
```

The goal is not to bypass authentication or defeat access controls.

The goal is to **engineer a usable interface around data and HTTP behavior that is already publicly accessible**.

---

## Features

### Request Lab

Initiate HTTP requests directly from Extractinator.

Supported request configuration includes:

- HTTP method
- URL
- query parameters
- headers
- request body
- response inspection
- timing and status information

---

### Public Endpoint Discovery

Inspect publicly returned HTML and resources for likely endpoint references.

Extractinator can identify patterns such as:

```text
/api/
/rest/
/v1/
/v2/
/graphql
.json
```

Discovered URLs are treated as **candidates**, not automatically as official or supported APIs.

---

### Flow Inspector

Inspect recorded HTTP exchanges in a network-debugging style interface.

Each flow can contain:

- request metadata
- response metadata
- headers
- request body
- response body
- status code
- redirects
- retries
- errors
- timing information
- extraction results
- source information

Flows can be filtered and inspected individually.

---

### HAR Import & Export

Extractinator supports working with HTTP Archive (HAR) data.

Capabilities include:

- HAR import
- HAR export
- request/response mapping
- timing reconstruction
- text response bodies
- base64-encoded bodies
- malformed-entry handling
- flow filtering after import

This makes Extractinator compatible with other browser and debugging workflows.

---

### mitmproxy Integration

Extractinator can receive HTTP flow information from a mitmproxy addon.

Typical workflow:

```text
Browser
   │
   ▼
mitmproxy
   │
   ▼
Extractinator
   │
   ▼
Flow Inspector
```

Sensitive headers are redacted before persisted or exported flow data is handled by the application.

The proxy workflow is designed for traffic you are authorized to inspect.

---

### API Forge

API Forge turns a compatible public GET/HEAD upstream endpoint into an Extractinator-managed endpoint.

Example:

```text
Public endpoint
       │
       ▼
API Forge
       │
       ▼
/generated/my-api
       │
       ▼
Extractinator API
```

Generated APIs can then be consumed independently of the original endpoint structure.

Extractinator-issued bearer tokens protect **your generated API**. They are not credentials for the upstream website.

---

### Postman & OpenAPI

Generated APIs and captured requests can be exported for use with developer tools.

Supported workflows include:

```text
Extractinator
     │
     ├── OpenAPI
     │
     └── Postman
```

This makes it easier to move from exploration to a repeatable development workflow.

---

### Flow Replay

Previously recorded public flows can be replayed through Extractinator's request policy.

This is useful for:

- debugging
- regression testing
- parser development
- comparing responses
- reproducing public requests

Replay does not attempt to bypass authentication or access restrictions.

---

### Offline Extraction

Stored response bodies can be analyzed without making a new upstream request.

This makes recorded flows useful for:

- parser development
- extraction testing
- repeatable experiments
- offline analysis

---

### Generic Web Extraction

Extractinator also includes generic extraction capabilities for publicly accessible web pages.

The extraction pipeline can use:

```text
JSON-LD
   ↓
Metadata / OpenGraph
   ↓
Structured JSON
   ↓
Semantic HTML
   ↓
Generic HTML extraction
```

Site-specific adapters can provide more accurate extraction for websites with known structures.

The project already includes a Books to Scrape adapter as a reference implementation.

---

# Architecture

Extractinator is split into several layers.

```text
                    ┌────────────────────┐
                    │      Frontend      │
                    │ Dashboard / Flows  │
                    └─────────┬──────────┘
                              │
                              ▼
                    ┌────────────────────┐
                    │      FastAPI       │
                    │    API Layer       │
                    └─────────┬──────────┘
                              │
          ┌───────────────────┼───────────────────┐
          ▼                   ▼                   ▼
   Request Lab          Flow Inspector        API Forge
          │                   │                   │
          └───────────────────┼───────────────────┘
                              ▼
                    ┌────────────────────┐
                    │   Flow / Service   │
                    │       Layer        │
                    └─────────┬──────────┘
                              │
              ┌───────────────┼────────────────┐
              ▼               ▼                ▼
           Fetcher        Discovery        Extractors
              │               │                │
              └───────────────┼────────────────┘
                              ▼
                         Flow Store
                              │
                              ▼
                       HAR / Replay
```

---

# Project Structure

```text
Extractinator/
│
├── app.py
├── run.py
├── start.bat
├── test.bat
│
├── extractinator/
│   ├── api.py
│   ├── discovery.py
│   ├── forge.py
│   ├── request_lab.py
│   ├── service.py
│   │
│   ├── adapters/
│   │   ├── base.py
│   │   └── books_to_scrape.py
│   │
│   ├── core/
│   │   ├── config.py
│   │   ├── encoding.py
│   │   ├── exceptions.py
│   │   ├── fetcher.py
│   │   ├── flows.py
│   │   ├── models.py
│   │   ├── policy.py
│   │   ├── registry.py
│   │   └── store.py
│   │
│   ├── extractors/
│   │   ├── generic.py
│   │   ├── html.py
│   │   ├── json_data.py
│   │   ├── jsonld.py
│   │   └── metadata.py
│   │
│   └── schemas/
│       └── common.py
│
├── frontend/
│   ├── index.html
│   ├── flows.html
│   └── assets/
│
├── scripts/
│   ├── demo.py
│   ├── live_smoke.py
│   └── mitmproxy_capture.py
│
├── tests/
│   ├── fixtures/
│   └── test_*.py
│
├── legacy/
│   └── original Books.toscrape implementation
│
├── requirements.txt
├── requirements-dev.txt
├── requirements-proxy.txt
├── pytest.ini
├── SECURITY.md
├── CHANGELOG.md
└── README.md
```

---

# Installation

## Requirements

- Python 3.11+
- Git
- Windows, macOS, or Linux

Optional:

- mitmproxy for traffic capture

---

## Clone the repository

```bash
git clone https://github.com/writo13/Extractinator.git
cd Extractinator
```

---

## Create a virtual environment

### Windows

```cmd
python -m venv .venv
```

Activate:

```cmd
.venv\Scripts\activate.bat
```

### Linux / macOS

```bash
python3 -m venv .venv
source .venv/bin/activate
```

---

## Install dependencies

```bash
python -m pip install -r requirements.txt
```

For development and testing:

```bash
python -m pip install -r requirements-dev.txt
```

For mitmproxy support:

```bash
python -m pip install -r requirements-proxy.txt
```

---

# Running Extractinator

## Windows

The easiest option is:

```cmd
start.bat
```

Or directly:

```bash
python run.py serve
```

The dashboard is available at:

```text
http://127.0.0.1:8000/
```

API documentation:

```text
http://127.0.0.1:8000/docs
```

Alternative API documentation:

```text
http://127.0.0.1:8000/redoc
```

---

# Main Workflows

## 1. Inspect a public webpage

Enter a public URL into the Extractinator dashboard.

The system can inspect the returned page and identify structured information and potential endpoint references.

---

## 2. Send a request

Use Request Lab to configure a request:

```text
Method:
GET

URL:
https://example.com/

Headers:
...

Query:
...
```

The response can then be inspected as a flow.

---

## 3. Discover endpoints

Use the discovery workflow against a publicly accessible page.

Potential endpoints can then be examined manually through Request Lab.

Discovery results are suggestions and must be validated individually.

---

## 4. Generate an API

Once a compatible public GET/HEAD endpoint has been identified:

```text
Public endpoint
      ↓
API Forge
      ↓
Generated endpoint
```

The resulting API is hosted by Extractinator.

Example:

```text
GET /generated/example
```

Clients can authenticate to the generated endpoint using an Extractinator-issued bearer token.

---

## 5. Inspect recorded traffic

Open the Flow Inspector:

```text
http://127.0.0.1:8000/flows-ui
```

A flow can be examined through:

```text
Summary
Request
Response
Timeline
Extraction
```

---

# HAR Workflow

Export flows:

```bash
python run.py export-har flows.har
```

Import a HAR archive:

```bash
python run.py import-har flows.har
```

After importing, flows can be searched, inspected, extracted, and replayed according to the application's security policy.

---

# mitmproxy Workflow

Start Extractinator first.

Then start the proxy integration:

```cmd
proxy.bat
```

or:

```bash
mitmdump -s scripts/mitmproxy_capture.py
```

Configure your own browser/client to use the mitmproxy instance.

Captured traffic can then appear in Extractinator's Flow Inspector.

### Important

Only capture traffic you are authorized to inspect.

Do not use the proxy integration to obtain:

- private credentials
- session cookies
- authentication tokens
- other people's personal information
- restricted application data

---

# Security Model

Extractinator is intended for **publicly accessible web interfaces and authorized traffic analysis**.

It is not designed to:

- bypass OAuth
- bypass login systems
- defeat CAPTCHAs
- bypass authorization
- steal or replay private credentials
- bypass paywalls
- defeat access controls
- access private user data

Extractinator-issued API tokens protect APIs **generated by Extractinator itself**.

They are not upstream authentication credentials.

Sensitive request information should not be committed to source control.

Examples include:

```text
Authorization
Cookie
Set-Cookie
API keys
CSRF tokens
client secrets
private credentials
```

See [SECURITY.md](SECURITY.md) for additional security guidance.

---

# Responsible Use

Extractinator should be used against:

- public resources
- systems you own
- systems you have permission to test
- publicly accessible interfaces where automated access is permitted

Respect:

- robots.txt
- rate limits
- website terms
- applicable law
- server capacity
- privacy requirements

Public accessibility does not automatically mean unrestricted or unlimited automated access.

---

# Development

Run the complete test suite:

```bash
python -m pytest -q
```

Run a specific test module:

```bash
python -m pytest tests/test_flows.py -v
```

Run API tests:

```bash
python -m pytest tests/test_api.py -v
```

---

# Design Principles

Extractinator is built around a few principles:

### Public-first

The system works with publicly accessible resources rather than trying to defeat authentication or authorization.

### Inspect before automate

Requests should be observable and understandable before they are turned into generated APIs.

### Separate discovery from execution

A discovered endpoint is a candidate—not automatically a trusted API.

### Preserve provenance

Recorded flows retain information about how a request and response were obtained.

### Keep extraction modular

Generic extraction handles unknown structures while adapters handle known websites.

### Make traffic reproducible

HAR, stored flows, replay, and offline extraction make experiments repeatable.

---

# Roadmap

Planned areas for future development include:

- richer OpenAPI generation
- improved schema inference
- additional site adapters
- browser automation for JavaScript-heavy public pages
- improved flow comparison
- response diffing
- request templating
- environment management
- collection versioning
- richer Postman workflows
- distributed flow storage
- improved observability

---

# Disclaimer

Extractinator is an independent open-source project.

It is not affiliated with, sponsored by, or endorsed by websites whose public resources may be inspected using the software.

Site owners retain control over their infrastructure, content, trademarks, and access policies.

---

# License

This project is released under the license included in this repository.

See [LICENSE](LICENSE) for details.

---

# Author

**Writobrata Chatterjee**

GitHub:

https://github.com/writo13

---

## Copyright

© 2026 Writobrata Chatterjee. All rights reserved.
