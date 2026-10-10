# API Development, REST API and FastAPI Fundamentals

Reference guide covering primary and fundamental topics of API Development, REST API, and FastAPI, with definitions and code examples in Python, Bash, and Go.

---

# API Development

## API Concepts

### API Definition
An Application Programming Interface (API) is a defined contract that lets one piece of software request data or actions from another without knowing its internal implementation. The contract specifies available operations, input formats, output formats, and error behavior.

### API Components
- **Client**: the software that sends requests (browser, mobile app, script, another service)
- **Server**: the software that receives requests and produces responses
- **Endpoint**: a specific URL where an operation is exposed
- **Request**: a message from client to server containing method, URL, headers, and optional body
- **Response**: a message from server to client containing status code, headers, and optional body
- **Resource**: a named piece of data or functionality (user, order, product)
- **Payload**: the data carried in a request or response body
- **Headers**: metadata key-value pairs attached to a message
- **Status code**: a three-digit number summarizing the outcome of a request

### API Consumers
- Web applications
- Mobile applications
- Third-party developers
- Internal services
- IoT devices

## API Types

### Classification by Audience

#### Public APIs (Open APIs)
Available to any developer, usually after registration for a key. Examples include weather, maps, and payment APIs.

#### Partner APIs
Shared only with selected business partners under agreements.

#### Private APIs (Internal APIs)
Used inside an organization to connect internal systems and services.

#### Composite APIs
Combine several API calls into a single request, reducing round trips for clients.

### Classification by Architecture or Protocol

#### REST
Resource-oriented style built on HTTP, typically exchanging JSON.

#### SOAP
XML-based protocol with strict contracts defined in WSDL.

#### GraphQL
Query language where the client specifies exactly which fields it needs from a single endpoint.

#### gRPC
High-performance RPC framework using HTTP/2 and Protocol Buffers.

#### WebSocket
Persistent, full-duplex connection for real-time bidirectional messaging.

#### Webhooks
Server-initiated HTTP callbacks sent when an event occurs.

#### Server-Sent Events
One-way stream from server to client over HTTP.

#### RPC and JSON-RPC
Remote procedure call style where the client invokes named functions on the server.

### Library and OS APIs
- Language library APIs
- Operating system APIs
- Database APIs
- Hardware APIs

## Client-Server Architecture

### Request-Response Cycle
The sequence in which a client sends a request and a server returns a response.
- Client request creation
- Network transmission
- Server processing
- Response delivery
- Client rendering or handling

### Stateless and Stateful Communication
In stateless communication, each request carries all the information needed to process it, and the server stores no client context between requests. In stateful communication, the server remembers earlier interactions (for example, a server-side session).

### Synchronous and Asynchronous Communication
Synchronous calls block until a response arrives. Asynchronous calls return immediately and deliver results later through callbacks, polling, queues, or events.

### Monolithic and Microservices Architecture
A monolith packages all functionality into one deployable unit. Microservices split functionality into small independently deployable services that communicate through APIs.

### API Gateway Pattern
A single entry point that routes requests to backend services and handles cross-cutting concerns such as authentication, rate limiting, and logging.

### Backend for Frontend Pattern
A dedicated backend layer tailored to the needs of one client type (web, mobile), shaping responses for that client.

## Web Fundamentals

### HTTP Protocol
HyperText Transfer Protocol is the application-layer, request-response protocol that most web APIs use.

#### HTTP Versions
- **HTTP/1.1**: text-based, one request per connection at a time, keep-alive supported
- **HTTP/2**: binary framing, multiplexed streams over one connection, header compression
- **HTTP/3**: runs over QUIC (UDP), reduces connection setup latency and head-of-line blocking

#### HTTP Request Structure
- Request line (method, path, version)
- Request headers
- Empty line
- Request body

```http
POST /users HTTP/1.1
Host: api.example.com
Content-Type: application/json
Authorization: Bearer <token>

{"name": "Asha", "email": "asha@example.com"}
```

#### HTTP Response Structure
- Status line (version, status code, reason phrase)
- Response headers
- Empty line
- Response body

```http
HTTP/1.1 201 Created
Content-Type: application/json
Location: /users/42

{"id": 42, "name": "Asha", "email": "asha@example.com"}
```

#### HTTP Methods

| Method | Purpose | Safe | Idempotent |
|--------|---------|------|------------|
| GET | Retrieve a resource | Yes | Yes |
| POST | Create a resource or trigger an action | No | No |
| PUT | Replace a resource completely | No | Yes |
| PATCH | Partially update a resource | No | Not guaranteed |
| DELETE | Remove a resource | No | Yes |
| HEAD | Retrieve headers only | Yes | Yes |
| OPTIONS | Discover allowed methods | Yes | Yes |

#### Safe and Idempotent Methods
A safe method does not modify server state. An idempotent method produces the same server state whether called once or many times.

#### HTTP Status Codes
Status codes are grouped by class.
- **1xx Informational**: request received, processing continues
- **2xx Success**: 200 OK, 201 Created, 202 Accepted, 204 No Content
- **3xx Redirection**: 301 Moved Permanently, 302 Found, 304 Not Modified, 307/308 Redirects preserving method
- **4xx Client Error**: 400 Bad Request, 401 Unauthorized, 403 Forbidden, 404 Not Found, 405 Method Not Allowed, 409 Conflict, 415 Unsupported Media Type, 422 Unprocessable Entity, 429 Too Many Requests
- **5xx Server Error**: 500 Internal Server Error, 501 Not Implemented, 502 Bad Gateway, 503 Service Unavailable, 504 Gateway Timeout

#### HTTP Headers
Headers carry metadata about the request, response, or payload.

| Header | Role |
|--------|------|
| Content-Type | Media type of the body being sent |
| Accept | Media types the client can handle |
| Authorization | Credentials for authentication |
| Cache-Control | Caching directives |
| ETag | Version identifier of a resource |
| If-None-Match | Conditional request using an ETag |
| Location | URL of a created or redirected resource |
| User-Agent | Client software identification |
| Origin | Origin of a cross-site request |
| Retry-After | Wait time before retrying |
| X-Request-ID | Correlation identifier for tracing |

#### Inspecting HTTP with Bash
```bash
# Show response headers and body
curl -i https://api.github.com/users/octocat

# Headers only
curl -I https://api.github.com

# Verbose view of request and response
curl -v https://api.github.com/users/octocat

# Send JSON with POST
curl -X POST https://api.example.com/users \
  -H "Content-Type: application/json" \
  -d '{"name": "Asha", "email": "asha@example.com"}'

# Print only the status code
curl -s -o /dev/null -w "%{http_code}\n" https://api.example.com/health
```

### HTTPS and TLS
HTTPS is HTTP carried inside a TLS-encrypted connection. TLS provides confidentiality, integrity, and server identity verification through certificates issued by certificate authorities.
- Encryption in transit
- Certificates
- Certificate authorities

### URL and URI Structure
A URL identifies where a resource lives.

```text
https://api.example.com:443/v1/users/42?include=orders#section
\___/   \_____________/ \_/ \_________/ \___________/ \_____/
scheme       host      port    path        query      fragment
```

### DNS Resolution
The process that translates a hostname such as `api.example.com` into an IP address before a connection is made.

```bash
dig +short api.example.com
nslookup api.example.com
```

### Cookies and Sessions
Cookies are small key-value pairs stored by the client and sent automatically with matching requests. Sessions use a cookie containing an identifier that maps to server-side state.

### Content Negotiation
Mechanism where client and server agree on a representation format using headers such as `Accept`, `Accept-Language`, and `Accept-Encoding`.

```bash
curl -H "Accept: application/json" https://api.example.com/users/1
```

### CORS (Cross-Origin Resource Sharing)
A browser security mechanism that controls whether a web page from one origin may call an API on another origin.
- Same-origin policy
- Preflight requests (OPTIONS) for non-simple requests
- Allowed origins, methods, headers

```bash
# Simulate a preflight request
curl -i -X OPTIONS https://api.example.com/users \
  -H "Origin: https://app.example.com" \
  -H "Access-Control-Request-Method: POST" \
  -H "Access-Control-Request-Headers: content-type"
```

## Data Formats

### JSON
JavaScript Object Notation is a lightweight text format built from objects, arrays, strings, numbers, booleans, and null. It is the default format of most REST APIs.
- JSON Schema describes and validates JSON structure

```json
{
  "id": 42,
  "name": "Asha",
  "active": true,
  "roles": ["admin", "editor"],
  "address": { "city": "Kolkata", "country": "IN" }
}
```

### XML
Tag-based markup format used by SOAP and many legacy systems.

### YAML
Human-readable data format commonly used for configuration and OpenAPI documents.

### Form Data
- `application/x-www-form-urlencoded`: key-value pairs encoded in the body
- `multipart/form-data`: mixed fields and file uploads

```bash
curl -X POST https://api.example.com/login -d "username=asha&password=secret"
curl -X POST https://api.example.com/upload -F "file=@report.pdf" -F "title=Report"
```

### Binary Data and File Transfer
Binary payloads use types such as `application/octet-stream`, or are encoded as Base64 inside JSON.

### Protocol Buffers
A compact binary serialization format with a schema defined in `.proto` files, used mainly with gRPC.

### Serialization and Deserialization
Serialization converts in-memory objects to a transferable format; deserialization reverses it.

```python
import json

user = {"id": 42, "name": "Asha"}
text = json.dumps(user)         # serialization
restored = json.loads(text)     # deserialization
```

```go
package main

import (
	"encoding/json"
	"fmt"
)

type User struct {
	ID   int    `json:"id"`
	Name string `json:"name"`
}

func main() {
	data, _ := json.Marshal(User{ID: 42, Name: "Asha"}) // serialization
	fmt.Println(string(data))

	var u User
	_ = json.Unmarshal(data, &u) // deserialization
	fmt.Println(u.Name)
}
```

```bash
# Parse and filter JSON in the terminal
curl -s https://api.github.com/users/octocat | jq '.login, .public_repos'
```

### Character Encoding (UTF-8)
UTF-8 is the standard text encoding for APIs, supporting all Unicode characters.

### Date and Time Formats (ISO 8601)
Standard timestamp format, preferably in UTC: `2026-10-06T14:30:00Z`.

## API Design Process

### Requirements Gathering
Identify consumers, use cases, data needs, performance targets, and security constraints before designing endpoints.

### API-First Design
Define the API contract before writing implementation code, so frontend, backend, and test teams work in parallel.

### Design-First and Code-First Approaches
Design-first writes the specification (OpenAPI) and generates code from it. Code-first writes code and generates the specification from annotations or types (FastAPI follows this approach).

### Resource Modeling
Identifying the nouns of the system, their attributes, and their relationships.

### Contract Definition
Formal description of paths, parameters, schemas, responses, and errors.

### Mock Servers and Prototyping
Simulated servers returning sample responses from a specification, allowing early client development.

```bash
# Mock server from an OpenAPI document using Prism
npm install -g @stoplight/prism-cli
prism mock openapi.yaml
```

### API Lifecycle
- Design
- Development
- Testing
- Deployment
- Monitoring
- Versioning
- Deprecation
- Retirement

## API Design Principles

### Consistency
Same naming, response structure, and error format across all endpoints.

### Simplicity
Minimal surface area, with each endpoint doing one clear job.

### Predictability
Behavior follows standard HTTP semantics, so developers can guess how an endpoint works.

### Naming Conventions
- Plural nouns for collections (`/users`)
- Lowercase and hyphenated paths (`/order-items`)
- Consistent field casing (snake_case or camelCase, not both)
- No verbs in paths

### Backward Compatibility
Changes should not break existing clients. Adding optional fields is safe; removing or renaming fields is breaking.

### Idempotency
Repeated identical requests have the same effect as a single request, enabling safe retries.

### Least Surprise
Avoid unexpected behavior such as GET requests that modify data.

### Developer Experience
Clear documentation, helpful errors, working examples, and quick onboarding.

## API Security

### Authentication
Verifying who the caller is.

#### Basic Authentication
Username and password sent as a Base64-encoded `Authorization` header. Only acceptable over HTTPS.

```bash
curl -u asha:secret https://api.example.com/profile
```

#### API Keys
A secret string identifying the calling application, sent in a header or query parameter.

```bash
curl -H "X-API-Key: abc123" https://api.example.com/data
```

#### Bearer Tokens
Opaque or structured tokens sent in the `Authorization` header; whoever holds the token is granted access.

```bash
curl -H "Authorization: Bearer $TOKEN" https://api.example.com/profile
```

#### JSON Web Tokens (JWT)
A signed, compact token with three Base64URL parts: header, payload (claims), and signature.
- Access tokens: short-lived, used for API calls
- Refresh tokens: long-lived, used to obtain new access tokens
- Token expiration through the `exp` claim

```python
import jwt  # pip install PyJWT
from datetime import datetime, timedelta, timezone

SECRET = "change-me"

def create_token(user_id: int) -> str:
    payload = {
        "sub": str(user_id),
        "exp": datetime.now(timezone.utc) + timedelta(minutes=30),
    }
    return jwt.encode(payload, SECRET, algorithm="HS256")

def verify_token(token: str) -> dict:
    return jwt.decode(token, SECRET, algorithms=["HS256"])
```

```go
package main

import (
	"fmt"
	"time"

	"github.com/golang-jwt/jwt/v5"
)

var secret = []byte("change-me")

func createToken(userID string) (string, error) {
	claims := jwt.MapClaims{
		"sub": userID,
		"exp": time.Now().Add(30 * time.Minute).Unix(),
	}
	return jwt.NewWithClaims(jwt.SigningMethodHS256, claims).SignedString(secret)
}

func verifyToken(tokenStr string) (jwt.MapClaims, error) {
	token, err := jwt.Parse(tokenStr, func(t *jwt.Token) (any, error) {
		return secret, nil
	}, jwt.WithValidMethods([]string{"HS256"}))
	if err != nil {
		return nil, err
	}
	return token.Claims.(jwt.MapClaims), nil
}

func main() {
	t, _ := createToken("42")
	claims, _ := verifyToken(t)
	fmt.Println(claims["sub"])
}
```

#### OAuth 2.0
An authorization framework allowing an application to obtain limited access to a resource on behalf of a user.
- Roles: resource owner, client, authorization server, resource server
- Authorization Code flow: for server-side web apps
- Authorization Code with PKCE: for mobile and single-page apps
- Client Credentials flow: for machine-to-machine communication
- Refresh Token flow: renews access tokens
- Scopes: limit what the token may do

```bash
# Client Credentials flow
curl -X POST https://auth.example.com/oauth/token \
  -d "grant_type=client_credentials" \
  -d "client_id=$CLIENT_ID" \
  -d "client_secret=$CLIENT_SECRET" \
  -d "scope=read:users"
```

#### OpenID Connect
An identity layer on top of OAuth 2.0 that returns an ID token describing the authenticated user.

#### Session-Based Authentication
The server creates a session after login and sends a session ID cookie.

#### Mutual TLS
Both client and server present certificates, authenticating each other.

#### Single Sign-On
One login grants access to several applications through a trusted identity provider.

### Authorization
Deciding what an authenticated caller is allowed to do.

#### Role-Based Access Control (RBAC)
Permissions are attached to roles (admin, editor, viewer), and users are assigned roles.

#### Attribute-Based Access Control (ABAC)
Decisions use attributes of user, resource, and context (department, time, location).

#### Scopes and Permissions
Fine-grained capabilities such as `read:orders` and `write:orders`.

#### Resource Ownership Checks
Verifying that the caller owns or may access the specific record requested.

### Common Vulnerabilities
- OWASP API Security Top 10
- Broken object-level authorization (accessing another user's record by changing an ID)
- Broken authentication
- Excessive data exposure
- Mass assignment (client sets fields it should not control)
- Injection (SQL, NoSQL, command)
- Cross-Site Scripting
- Cross-Site Request Forgery
- Security misconfiguration
- Server-Side Request Forgery
- Improper inventory management

### Security Controls

#### Input Validation
Reject malformed or unexpected input at the boundary.

#### Output Encoding
Escape data before rendering to prevent injection into clients.

#### Rate Limiting and Throttling
Cap request counts per client to prevent abuse.

#### Request Size Limits
Bound body sizes to prevent resource exhaustion.

#### Security Headers
`Strict-Transport-Security`, `X-Content-Type-Options`, `Content-Security-Policy`.

#### Secrets Management
Keep keys and passwords in environment variables or a secrets manager, never in source code.

#### Password Hashing
Store passwords only as salted hashes (bcrypt, argon2).

```python
from passlib.context import CryptContext  # pip install passlib[bcrypt]

pwd = CryptContext(schemes=["bcrypt"], deprecated="auto")
hashed = pwd.hash("my-password")
assert pwd.verify("my-password", hashed)
```

```go
import "golang.org/x/crypto/bcrypt"

hash, _ := bcrypt.GenerateFromPassword([]byte("my-password"), bcrypt.DefaultCost)
err := bcrypt.CompareHashAndPassword(hash, []byte("my-password")) // nil when correct
```

#### Audit Logging
Record who did what and when for sensitive actions.

#### Dependency Scanning
Detect vulnerable packages.

```bash
pip install pip-audit && pip-audit
go install golang.org/x/vuln/cmd/govulncheck@latest && govulncheck ./...
```

## API Performance and Scalability

### Caching
Storing responses so repeated requests are served faster without recomputation.
- HTTP caching (browser and proxy)
- ETag and conditional requests
- Cache-Control directives
- CDN caching
- Application-level caching (Redis, Memcached)

```python
import json
import redis  # pip install redis

r = redis.Redis(host="localhost", port=6379, decode_responses=True)

def get_user(user_id: int, db_lookup):
    key = f"user:{user_id}"
    cached = r.get(key)
    if cached:
        return json.loads(cached)
    user = db_lookup(user_id)
    r.setex(key, 300, json.dumps(user))  # expire after 5 minutes
    return user
```

### Pagination
Returning large collections in smaller pages.
- **Offset-based**: `?limit=20&offset=40`; simple but slow and unstable on large or changing data
- **Page-number**: `?page=3&per_page=20`
- **Cursor-based**: `?cursor=abc123`; stable and efficient
- **Keyset**: filters on the last seen sort key (`WHERE id > 100 ORDER BY id LIMIT 20`)

```bash
curl "https://api.example.com/users?limit=20&offset=40"
curl "https://api.example.com/users?cursor=eyJpZCI6MTAwfQ&limit=20"
```

```python
def paginate(items: list, limit: int = 20, offset: int = 0) -> dict:
    page = items[offset: offset + limit]
    return {
        "data": page,
        "total": len(items),
        "next_offset": offset + limit if offset + limit < len(items) else None,
    }
```

### Filtering, Sorting and Searching
Query parameters that narrow, order, and search collections.

```bash
curl "https://api.example.com/products?category=books&price_lt=500&sort=-created_at&q=python"
```

### Field Selection and Sparse Fieldsets
Letting clients request only needed fields, reducing payload size.

```bash
curl "https://api.example.com/users/42?fields=id,name,email"
```

### Compression (gzip, brotli)
Compressing response bodies to reduce transfer size.

```bash
curl -H "Accept-Encoding: gzip" --compressed -i https://api.example.com/users
```

### Connection Pooling
Reusing established connections to databases or upstream services instead of opening new ones per request.

```python
import httpx

with httpx.Client(limits=httpx.Limits(max_connections=20, max_keepalive_connections=10)) as client:
    for i in range(5):
        client.get("https://api.example.com/users")
```

```go
client := &http.Client{
	Transport: &http.Transport{
		MaxIdleConns:        100,
		MaxIdleConnsPerHost: 10,
		IdleConnTimeout:     90 * time.Second,
	},
	Timeout: 10 * time.Second,
}
```

### Load Balancing
Distributing incoming requests across multiple server instances using strategies such as round robin, least connections, or IP hash.

```nginx
upstream api_servers {
    least_conn;
    server 10.0.0.1:8000;
    server 10.0.0.2:8000;
}
server {
    listen 80;
    location / { proxy_pass http://api_servers; }
}
```

### Horizontal and Vertical Scaling
Horizontal scaling adds more instances. Vertical scaling adds more CPU or memory to an existing instance.

### Rate Limiting Algorithms
- **Fixed window**: counts requests in fixed time slices
- **Sliding window**: counts requests over a rolling period
- **Token bucket**: tokens refill at a steady rate; each request consumes one
- **Leaky bucket**: requests drain at a constant rate

```python
import time

class TokenBucket:
    def __init__(self, rate: float, capacity: int):
        self.rate, self.capacity = rate, capacity
        self.tokens, self.last = capacity, time.monotonic()

    def allow(self) -> bool:
        now = time.monotonic()
        self.tokens = min(self.capacity, self.tokens + (now - self.last) * self.rate)
        self.last = now
        if self.tokens >= 1:
            self.tokens -= 1
            return True
        return False

bucket = TokenBucket(rate=5, capacity=10)  # 5 requests per second, burst of 10
```

```go
import (
	"net/http"
	"golang.org/x/time/rate"
)

var limiter = rate.NewLimiter(5, 10) // 5 req/s, burst 10

func rateLimit(next http.Handler) http.Handler {
	return http.HandlerFunc(func(w http.ResponseWriter, r *http.Request) {
		if !limiter.Allow() {
			http.Error(w, "too many requests", http.StatusTooManyRequests)
			return
		}
		next.ServeHTTP(w, r)
	})
}
```

### Asynchronous Processing
Moving slow work out of the request cycle.
- Background jobs
- Message queues (RabbitMQ, Kafka, SQS)
- Task queues (Celery, RQ)
- Event-driven architecture

```python
# Celery task
from celery import Celery

celery = Celery("tasks", broker="redis://localhost:6379/0")

@celery.task
def send_welcome_email(user_id: int):
    ...  # slow work runs in a worker process
```

```bash
celery -A tasks worker --loglevel=info
```

### Bulk and Batch Operations
Single requests that create, update, or delete several resources at once.

```bash
curl -X POST https://api.example.com/users/bulk \
  -H "Content-Type: application/json" \
  -d '[{"name":"A"},{"name":"B"}]'
```

### Long-Running Operations
For slow tasks, return `202 Accepted` with a status URL that the client polls.

```json
{ "job_id": "j-123", "status": "processing", "status_url": "/jobs/j-123" }
```

## API Documentation

### OpenAPI Specification
A language-neutral standard for describing REST APIs in YAML or JSON.
- Info
- Servers
- Paths
- Components
- Schemas
- Parameters
- Request bodies
- Responses
- Security schemes
- Tags

```yaml
openapi: 3.1.0
info:
  title: User API
  version: 1.0.0
paths:
  /users/{id}:
    get:
      summary: Retrieve a user
      parameters:
        - name: id
          in: path
          required: true
          schema: { type: integer }
      responses:
        "200":
          description: User found
          content:
            application/json:
              schema:
                $ref: "#/components/schemas/User"
        "404":
          description: User not found
components:
  schemas:
    User:
      type: object
      properties:
        id: { type: integer }
        name: { type: string }
```

### Swagger UI
Interactive browser interface generated from an OpenAPI document, allowing endpoints to be tried live.

### ReDoc
Clean, three-panel documentation renderer for OpenAPI documents.

### Postman Collections
Shareable collections of saved requests, environments, and tests.

### Developer Portals
Websites hosting documentation, API keys, tutorials, and support.

### Changelogs and Release Notes
Dated records of additions, changes, deprecations, and removals.

### SDK and Client Code Generation
Generating client libraries automatically from an OpenAPI document.

```bash
npx @openapitools/openapi-generator-cli generate -i openapi.yaml -g python -o ./client
```

## API Testing

### Testing Levels
- **Unit testing**: tests individual functions in isolation
- **Integration testing**: tests components working together (API plus database)
- **Contract testing**: verifies provider and consumer agree on the contract
- **End-to-end testing**: tests full user flows
- **Functional testing**: verifies behavior against requirements
- **Load and performance testing**: measures behavior under traffic
- **Security testing**: probes for vulnerabilities
- **Regression testing**: confirms old behavior still works

### Testing Tools
- Postman, Insomnia
- curl, HTTPie
- pytest
- JMeter, Locust, k6
- Newman

### Test Strategies
- Mocking and stubbing
- Test fixtures
- Test databases
- Test coverage
- Schema validation tests

```python
# pytest with requests
import requests

BASE = "http://localhost:8000"

def test_create_and_get_user():
    created = requests.post(f"{BASE}/users", json={"name": "Asha"})
    assert created.status_code == 201
    user_id = created.json()["id"]

    fetched = requests.get(f"{BASE}/users/{user_id}")
    assert fetched.status_code == 200
    assert fetched.json()["name"] == "Asha"
```

```go
// Go handler test using httptest
package main

import (
	"net/http"
	"net/http/httptest"
	"testing"
)

func TestUsersHandler(t *testing.T) {
	req := httptest.NewRequest(http.MethodGet, "/users", nil)
	rec := httptest.NewRecorder()

	usersHandler(rec, req)

	if rec.Code != http.StatusOK {
		t.Fatalf("expected 200, got %d", rec.Code)
	}
}
```

```bash
# Load test with k6
k6 run --vus 50 --duration 30s load_test.js

# Quick load test with Apache Bench
ab -n 1000 -c 50 http://localhost:8000/health
```

```python
# Locust load test
from locust import HttpUser, task, between

class ApiUser(HttpUser):
    wait_time = between(1, 3)

    @task
    def list_users(self):
        self.client.get("/users")
```

## API Versioning

### Versioning Strategies
- **URI path versioning**: `/v1/users`
- **Query parameter versioning**: `/users?version=1`
- **Header versioning**: `X-API-Version: 1`
- **Media type versioning**: `Accept: application/vnd.example.v1+json`

```bash
curl https://api.example.com/v2/users
curl -H "Accept: application/vnd.example.v2+json" https://api.example.com/users
```

### Semantic Versioning
MAJOR.MINOR.PATCH, where MAJOR means breaking changes, MINOR means backward-compatible features, and PATCH means fixes.

### Breaking and Non-Breaking Changes
- Breaking: removing fields, renaming fields, changing types, tightening validation
- Non-breaking: adding optional fields, adding endpoints

### Deprecation Policy
Announce deprecation early, provide a migration path and a removal date.

### Sunset Headers
HTTP headers signalling planned retirement of an endpoint.

```http
Deprecation: true
Sunset: Wed, 31 Dec 2026 23:59:59 GMT
Link: </v2/users>; rel="successor-version"
```

## Error Handling

### Error Response Structure
A consistent body for every error.
- Error code
- Message
- Details
- Timestamp
- Request identifier
- Documentation link

```json
{
  "error": {
    "code": "VALIDATION_ERROR",
    "message": "Email format is invalid",
    "details": [{ "field": "email", "issue": "must be a valid email" }],
    "request_id": "req-8f2a",
    "timestamp": "2026-10-06T10:15:00Z"
  }
}
```

### Problem Details (RFC 7807 / RFC 9457)
A standard JSON error format with media type `application/problem+json`.

```json
{
  "type": "https://api.example.com/problems/out-of-stock",
  "title": "Item out of stock",
  "status": 409,
  "detail": "Product 981 has no remaining inventory.",
  "instance": "/orders/5521"
}
```

### Validation Errors
Return `400` or `422` listing each invalid field and the reason.

### Error Categories
- Client errors (4xx)
- Server errors (5xx)
- Network errors
- Timeout errors

### Retry Strategies
Retry only idempotent requests or those protected by idempotency keys, and only for transient failures (429, 502, 503, 504, timeouts).
- Exponential backoff: wait time doubles after each attempt
- Jitter: random variation prevents synchronized retries
- Circuit breaker: stops calling a failing service for a cooling-off period

```python
import random
import time
import httpx

def get_with_retry(url: str, retries: int = 5):
    for attempt in range(retries):
        try:
            resp = httpx.get(url, timeout=5)
            if resp.status_code not in (429, 502, 503, 504):
                return resp
        except httpx.TransportError:
            pass
        time.sleep((2 ** attempt) + random.random())  # backoff with jitter
    raise RuntimeError("request failed after retries")
```

```go
func getWithRetry(url string, retries int) (*http.Response, error) {
	var lastErr error
	for attempt := 0; attempt < retries; attempt++ {
		resp, err := http.Get(url)
		if err == nil && resp.StatusCode < 500 && resp.StatusCode != 429 {
			return resp, nil
		}
		if err != nil {
			lastErr = err
		} else {
			resp.Body.Close()
			lastErr = fmt.Errorf("status %d", resp.StatusCode)
		}
		backoff := time.Duration(1<<attempt)*time.Second + time.Duration(rand.Intn(500))*time.Millisecond
		time.Sleep(backoff)
	}
	return nil, lastErr
}
```

```bash
# curl built-in retry
curl --retry 5 --retry-delay 2 --retry-all-errors https://api.example.com/users
```

## Observability and Monitoring

### Logging
Recording events for debugging and auditing.
- Structured logging (JSON lines)
- Log levels (DEBUG, INFO, WARNING, ERROR, CRITICAL)
- Correlation IDs linking logs of one request across services

```python
import logging, json

logger = logging.getLogger("api")
logging.basicConfig(level=logging.INFO, format="%(message)s")

logger.info(json.dumps({"event": "user_created", "user_id": 42, "request_id": "req-8f2a"}))
```

```go
import "log/slog"

logger := slog.New(slog.NewJSONHandler(os.Stdout, nil))
logger.Info("user created", "user_id", 42, "request_id", "req-8f2a")
```

### Metrics
Numeric measurements over time. The four golden signals are:
- Latency
- Throughput (traffic)
- Error rate
- Saturation

### Distributed Tracing
Follows one request across multiple services.
- OpenTelemetry
- Jaeger
- Zipkin

### Health Checks
- **Liveness**: process is running
- **Readiness**: service can accept traffic (dependencies reachable)

```python
@app.get("/health/live")
def live():
    return {"status": "alive"}
```

```bash
curl -fsS http://localhost:8000/health/live || echo "service down"
```

### Alerting
Notifications triggered when metrics cross thresholds.

### Tools
- Prometheus
- Grafana
- ELK Stack
- Sentry
- Datadog

## API Management

### API Gateways
Infrastructure that fronts APIs and applies shared policies.
- Kong
- NGINX
- AWS API Gateway
- Azure API Management
- Apigee
- Traefik

### Traffic Management
- Routing
- Throttling
- Quotas

### Analytics and Usage Tracking
Measuring calls per client, endpoint popularity, latency, and error trends.

### API Monetization
Charging through subscriptions, usage tiers, or pay-per-call pricing.

### API Governance and Standards
Organization-wide rules for naming, security, versioning, and documentation, often enforced with linters.

```bash
npx @stoplight/spectral-cli lint openapi.yaml
```

## Deployment and DevOps

### Containerization
Packaging an application with its dependencies into an image that runs identically everywhere.

```dockerfile
FROM python:3.12-slim
WORKDIR /app
COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt
COPY . .
CMD ["uvicorn", "app.main:app", "--host", "0.0.0.0", "--port", "8000"]
```

```dockerfile
# Go multi-stage build
FROM golang:1.23 AS build
WORKDIR /src
COPY . .
RUN CGO_ENABLED=0 go build -o /api ./cmd/api

FROM gcr.io/distroless/static
COPY --from=build /api /api
EXPOSE 8080
ENTRYPOINT ["/api"]
```

```bash
docker build -t my-api .
docker run -d -p 8000:8000 --name my-api my-api
docker compose up -d
```

### Orchestration
Kubernetes schedules, scales, and heals containers across a cluster.

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: my-api
spec:
  replicas: 3
  selector:
    matchLabels: { app: my-api }
  template:
    metadata:
      labels: { app: my-api }
    spec:
      containers:
        - name: api
          image: my-api:1.0.0
          ports: [{ containerPort: 8000 }]
          readinessProbe:
            httpGet: { path: /health/ready, port: 8000 }
```

### CI/CD Pipelines
Automated build, test, and deploy workflows (GitHub Actions, GitLab CI, Jenkins).

```yaml
# .github/workflows/ci.yml
name: CI
on: [push]
jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-python@v5
        with: { python-version: "3.12" }
      - run: pip install -r requirements.txt
      - run: pytest
```

### Environments
- Development
- Staging
- Production

### Configuration Management
- Environment variables
- Secrets management
- Twelve-Factor App methodology (config in environment, stateless processes, logs as streams)

### Hosting Options
- Virtual machines
- Platform-as-a-Service
- Serverless
- Cloud providers (AWS, Azure, GCP)

### Deployment Strategies
- **Blue-green**: two identical environments; traffic switches at once
- **Canary**: a small percentage of traffic goes to the new version first
- **Rolling updates**: instances are replaced gradually

## Databases and Data Access

### Relational Databases
Store data in tables with fixed schemas and SQL queries (PostgreSQL, MySQL, SQLite).

### NoSQL Databases
Flexible data models: document (MongoDB), key-value (Redis, DynamoDB), wide-column (Cassandra).

### ORMs and Query Builders
Libraries mapping objects to tables.
- SQLAlchemy
- SQLModel
- Tortoise ORM
- Django ORM
- Prisma

```python
from sqlalchemy import create_engine, String
from sqlalchemy.orm import DeclarativeBase, Mapped, mapped_column, Session

class Base(DeclarativeBase):
    pass

class User(Base):
    __tablename__ = "users"
    id: Mapped[int] = mapped_column(primary_key=True)
    name: Mapped[str] = mapped_column(String(100))

engine = create_engine("sqlite:///app.db")
Base.metadata.create_all(engine)

with Session(engine) as session:
    session.add(User(name="Asha"))
    session.commit()
```

```go
import "database/sql"
import _ "github.com/lib/pq"

db, _ := sql.Open("postgres", "postgres://user:pass@localhost/app?sslmode=disable")
var name string
err := db.QueryRow("SELECT name FROM users WHERE id = $1", 42).Scan(&name)
```

### Database Migrations
Versioned scripts that evolve the schema safely.

```bash
alembic init migrations
alembic revision --autogenerate -m "create users table"
alembic upgrade head
```

### Transactions and Concurrency
- ACID properties: Atomicity, Consistency, Isolation, Durability
- Optimistic locking: detect conflicts using a version column
- Pessimistic locking: lock rows during update

### Indexing and Query Optimization
Indexes speed up lookups at the cost of slower writes. Use `EXPLAIN` to inspect query plans.

```sql
CREATE INDEX idx_users_email ON users (email);
EXPLAIN ANALYZE SELECT * FROM users WHERE email = 'asha@example.com';
```

### Repository Pattern
An abstraction layer hiding persistence details behind an interface.

```python
class UserRepository:
    def __init__(self, session):
        self.session = session

    def get(self, user_id: int):
        return self.session.get(User, user_id)

    def add(self, user: User):
        self.session.add(user)
```

### Data Transfer Objects
Objects that define the shape of data crossing the API boundary, separate from database models.

## Architecture and Design Patterns

### Layered Architecture
- Presentation layer: routes and request handling
- Service layer: business logic
- Data access layer: database operations

### Clean Architecture
Dependencies point inward toward business rules, keeping domain logic independent of frameworks.

### Hexagonal Architecture
Core logic communicates with the outside world through ports and adapters.

### Domain-Driven Design
Modeling software around the business domain using bounded contexts, entities, and aggregates.

### CQRS and Event Sourcing
CQRS separates read and write models. Event sourcing stores state changes as an append-only sequence of events.

### Dependency Injection
Supplying a component's dependencies from outside rather than creating them inside.

### Saga Pattern
Coordinates a distributed transaction as a sequence of local transactions with compensating actions.

### Idempotency Keys
A client-supplied unique key that lets the server detect and ignore duplicate submissions.

```bash
curl -X POST https://api.example.com/payments \
  -H "Idempotency-Key: 3f6c1e2a-9b7d-4c1a-8f3e-1a2b3c4d5e6f" \
  -H "Content-Type: application/json" \
  -d '{"amount": 1500, "currency": "INR"}'
```

### Service Discovery
Mechanism by which services locate each other dynamically (DNS, Consul, Kubernetes services).

### Webhooks and Callbacks
The API calls a URL registered by the client when an event happens.

```python
import hashlib, hmac

def verify_webhook(body: bytes, signature: str, secret: str) -> bool:
    expected = hmac.new(secret.encode(), body, hashlib.sha256).hexdigest()
    return hmac.compare_digest(expected, signature)
```

## Alternative API Styles

### GraphQL
A query language where clients request exactly the fields they need from a single endpoint.
- Schema and types
- Queries (read), mutations (write), subscriptions (real-time)
- Resolvers (functions that fetch each field)

```bash
curl -X POST https://api.example.com/graphql \
  -H "Content-Type: application/json" \
  -d '{"query": "{ user(id: 42) { name email } }"}'
```

### gRPC
RPC framework with strongly typed contracts in `.proto` files.
- Protocol Buffers
- Unary and streaming calls

```protobuf
syntax = "proto3";

service UserService {
  rpc GetUser (UserRequest) returns (UserReply);
}
message UserRequest { int32 id = 1; }
message UserReply   { int32 id = 1; string name = 2; }
```

### WebSockets
Persistent two-way connections for chat, live dashboards, and games.

### Server-Sent Events
One-way server-to-client streaming over a standard HTTP connection.

### SOAP and WSDL
XML messaging protocol with a machine-readable contract (WSDL).

### Comparison of API Styles

| Style | Format | Transport | Best Fit |
|-------|--------|-----------|----------|
| REST | JSON | HTTP | General web and mobile APIs |
| GraphQL | JSON | HTTP | Flexible client-driven queries |
| gRPC | Protobuf | HTTP/2 | Internal, low-latency services |
| SOAP | XML | HTTP and others | Enterprise and legacy systems |
| WebSocket | Any | TCP | Real-time bidirectional messaging |

---

# REST API

## REST Fundamentals

### REST Definition
Representational State Transfer is an architectural style for networked systems introduced by Roy Fielding in his 2000 doctoral dissertation. Clients interact with resources through representations transferred over a uniform interface, usually HTTP.

### Architectural Constraints
- **Client-Server**: separation of user interface concerns from data storage concerns
- **Stateless**: each request contains everything the server needs
- **Cacheable**: responses declare whether they may be cached
- **Uniform Interface**: consistent way to interact with all resources
- **Layered System**: clients cannot tell whether they talk to the end server or an intermediary
- **Code on Demand** (optional): server may send executable code to clients

### Uniform Interface Constraints
- Identification of resources through URIs
- Manipulation of resources through representations
- Self-descriptive messages (method, media type, status)
- Hypermedia as the Engine of Application State (HATEOAS)

### Richardson Maturity Model
A scale for measuring how fully an API follows REST.
- Level 0: single endpoint, HTTP as a tunnel
- Level 1: individual resources with their own URIs
- Level 2: proper use of HTTP verbs and status codes
- Level 3: hypermedia controls (HATEOAS)

## Resources and Representations

### Resource Identification
Every resource has a unique, stable URI such as `/users/42`.

### Resource Types
- **Collection resources**: `/users`
- **Singleton resources**: `/users/42`, `/config`
- **Sub-resources**: `/users/42/orders`
- **Controller resources**: action endpoints such as `/orders/42/cancel`

### Representations
A resource is separate from its representation. One resource can be delivered as:
- JSON
- XML
- HTML
- Plain text
- Binary

### Resource Relationships
- One-to-one: `/users/42/profile`
- One-to-many: `/users/42/orders`
- Many-to-many: `/students/7/courses`
- Nested resources

## Endpoint Design

### URL Structure
- Base URL: `https://api.example.com/v1`
- Resource path: `/users`
- Path parameters: `/users/{id}`
- Query parameters: `?status=active`

### Naming Rules
- Nouns for resources
- Plural collection names
- Hierarchical nesting
- Hyphen-separated words
- Lowercase letters
- No trailing slashes
- No file extensions

### Endpoint Examples

| Operation | Method and Path |
|-----------|-----------------|
| List users | `GET /users` |
| Retrieve a user | `GET /users/{id}` |
| Create a user | `POST /users` |
| Replace a user | `PUT /users/{id}` |
| Partially update a user | `PATCH /users/{id}` |
| Delete a user | `DELETE /users/{id}` |
| List a user's orders | `GET /users/{id}/orders` |

```bash
# Full CRUD cycle with curl
curl -X POST   http://localhost:8000/users -H "Content-Type: application/json" -d '{"name":"Asha","email":"asha@example.com"}'
curl -X GET    http://localhost:8000/users
curl -X GET    http://localhost:8000/users/1
curl -X PUT    http://localhost:8000/users/1 -H "Content-Type: application/json" -d '{"name":"Asha R","email":"asha@example.com"}'
curl -X PATCH  http://localhost:8000/users/1 -H "Content-Type: application/json" -d '{"name":"Asha Roy"}'
curl -X DELETE http://localhost:8000/users/1
```

### Nesting Depth Guidelines
Keep nesting to one or two levels. Prefer `/orders/5521` over `/users/42/orders/5521/items/9`.

### Non-CRUD Actions
- Action sub-resources: `POST /orders/42/cancel`
- Custom verbs when no noun fits
- State transitions through `PATCH` on a status field

## HTTP Methods in REST

### GET
Retrieves a representation. Must not change server state; responses are cacheable.

### POST
Creates a subordinate resource or triggers a processing action. Not idempotent.

### PUT
Replaces the target resource with the request body. Idempotent.

### PATCH
Applies a partial modification.
- JSON Patch (`application/json-patch+json`): list of operations
- JSON Merge Patch (`application/merge-patch+json`): partial document

```bash
curl -X PATCH http://localhost:8000/users/1 \
  -H "Content-Type: application/json-patch+json" \
  -d '[{"op": "replace", "path": "/name", "value": "Asha Roy"}]'
```

### DELETE
Removes a resource. Idempotent.
- Soft delete: marks the record as deleted
- Hard delete: permanently removes the record

### HEAD
Same as GET but returns headers only.

### OPTIONS
Returns supported methods and CORS information.

### Method Semantics Table

| Method | Request Body | Success Code | Cacheable |
|--------|--------------|--------------|-----------|
| GET | No | 200 | Yes |
| POST | Yes | 201 or 200 | Rarely |
| PUT | Yes | 200 or 204 | No |
| PATCH | Yes | 200 or 204 | No |
| DELETE | Usually no | 204 | No |

## Status Codes in REST

### Success Responses
- **200 OK**: request succeeded, body returned
- **201 Created**: resource created; include a `Location` header
- **202 Accepted**: accepted for asynchronous processing
- **204 No Content**: succeeded, no body

### Redirection Responses
- **301 Moved Permanently**
- **304 Not Modified**: cached copy is still valid

### Client Error Responses
- **400 Bad Request**: malformed syntax
- **401 Unauthorized**: missing or invalid credentials
- **403 Forbidden**: authenticated but not permitted
- **404 Not Found**
- **405 Method Not Allowed**
- **406 Not Acceptable**
- **409 Conflict**: state conflict such as a duplicate
- **410 Gone**
- **415 Unsupported Media Type**
- **422 Unprocessable Entity**: well-formed but semantically invalid
- **429 Too Many Requests**

### Server Error Responses
- **500 Internal Server Error**
- **502 Bad Gateway**
- **503 Service Unavailable**
- **504 Gateway Timeout**

### Status Code Selection per Operation

| Scenario | Code |
|----------|------|
| Successful read | 200 |
| Successful creation | 201 |
| Successful deletion | 204 |
| Invalid input | 400 or 422 |
| Not logged in | 401 |
| Not allowed | 403 |
| Missing resource | 404 |
| Duplicate or version conflict | 409 |
| Unexpected failure | 500 |

## Request Design

### Path Parameters
Identify a specific resource within the path: `/users/42`.

### Query Parameters
Modify or refine the result of a request.
- Filtering: `?status=active`
- Sorting: `?sort=-created_at`
- Pagination: `?limit=20&offset=0`
- Searching: `?q=asha`
- Field selection: `?fields=id,name`
- Expansion of related resources: `?expand=orders`

### Request Headers
Carry authentication, content type, language, and tracing identifiers.

### Request Body
- JSON body structure
- Required and optional fields
- Validation rules

### Content-Type and Accept
`Content-Type` declares the format of the body sent. `Accept` declares the formats the client can receive.

### Conditional Requests
Allow efficient caching and safe concurrent updates.
- `If-None-Match`: fetch only if the ETag changed
- `If-Modified-Since`: fetch only if modified after a date
- `If-Match`: update only if the ETag still matches (optimistic concurrency)

```bash
curl -i https://api.example.com/users/42
# ETag: "v7"

curl -i https://api.example.com/users/42 -H 'If-None-Match: "v7"'
# HTTP/1.1 304 Not Modified

curl -X PUT https://api.example.com/users/42 \
  -H 'If-Match: "v7"' -H "Content-Type: application/json" \
  -d '{"name":"Asha R"}'
# 412 Precondition Failed when the ETag is outdated
```

## Response Design

### Response Body Structure
- Single resource
- Collection
- Envelope structure (`{"data": ..., "meta": ...}`) versus flat structure
- Metadata for pagination and counts

```json
{
  "data": [
    { "id": 1, "name": "Asha" },
    { "id": 2, "name": "Ravi" }
  ],
  "meta": { "total": 120, "limit": 2, "offset": 0 },
  "links": { "next": "/users?limit=2&offset=2" }
}
```

### Response Headers
- `Location`
- `Content-Type`
- `Cache-Control`
- `ETag`
- `Link`

### Pagination Response Patterns
- Link headers with `rel="next"` and `rel="prev"`
- Next and previous cursors
- Total count in metadata

### Partial Responses
`206 Partial Content` for range requests; sparse fieldsets for selected attributes.

### Empty Responses
Use `204 No Content` for successful operations that have nothing to return; return an empty array with `200` for empty collections.

### Error Responses
Same structure for all failures, with machine-readable codes and human-readable messages.

## Hypermedia and HATEOAS

### Link Relations
Links inside responses describe what the client can do next.

```json
{
  "id": 5521,
  "status": "pending",
  "_links": {
    "self":   { "href": "/orders/5521" },
    "cancel": { "href": "/orders/5521/cancel", "method": "POST" },
    "items":  { "href": "/orders/5521/items" }
  }
}
```

### Hypermedia Formats
- HAL
- JSON:API
- Siren
- Collection+JSON

### Discoverability
Clients navigate by following links from a root endpoint, reducing hard-coded URLs.

## Idempotency and Safety

### Idempotent Operations
GET, PUT, DELETE, HEAD, and OPTIONS are idempotent by definition.

### Idempotency Keys for POST
A unique key stored by the server lets retries of the same POST return the original result.

```python
processed: dict[str, dict] = {}

def create_payment(idempotency_key: str, payload: dict) -> dict:
    if idempotency_key in processed:
        return processed[idempotency_key]
    result = {"payment_id": "pay_001", **payload}
    processed[idempotency_key] = result
    return result
```

### Safe Methods
GET, HEAD, and OPTIONS must not cause side effects.

### Retry Safety
Clients can retry idempotent calls after timeouts without causing duplicates.

## Caching in REST

### Cache-Control Directives
- `max-age=N`: fresh for N seconds
- `no-cache`: revalidate before reuse
- `no-store`: never store
- `public`: any cache may store
- `private`: only the end user's cache may store

```http
Cache-Control: public, max-age=300
```

### Validation Caching
- `ETag`: opaque version identifier
- `Last-Modified`: timestamp of last change

### Cache Invalidation
Removing or refreshing cached data after the underlying resource changes, through short TTLs, purge calls, or versioned URLs.

## Versioning in REST

### URI Versioning
`/v1/users`; most visible and common.

### Header Versioning
Custom header such as `X-API-Version: 2`.

### Media Type Versioning
`Accept: application/vnd.example.v2+json`.

### Query Parameter Versioning
`/users?version=2`.

## REST Security

### Transport Security
- HTTPS enforcement
- HSTS (`Strict-Transport-Security`)

### Authentication Methods
API keys, Bearer tokens, JWT, OAuth 2.0.

### Authorization Patterns
RBAC, ABAC, scopes, ownership checks.

### Input Validation
Validate type, length, range, and format of every input.

### Rate Limiting
Return `429 Too Many Requests` with `Retry-After`.

```http
HTTP/1.1 429 Too Many Requests
Retry-After: 30
X-RateLimit-Limit: 100
X-RateLimit-Remaining: 0
```

### CORS Configuration
Allow only trusted origins.

### Sensitive Data Handling
- Avoid secrets in URLs (they appear in logs)
- Mask and redact personal data in responses and logs

## REST Server and Client Examples

### Python Client with requests
```python
import requests

BASE = "http://localhost:8000"
headers = {"Authorization": "Bearer my-token"}

# Create
r = requests.post(f"{BASE}/users", json={"name": "Asha"}, headers=headers, timeout=5)
r.raise_for_status()
user = r.json()

# Read
print(requests.get(f"{BASE}/users/{user['id']}", headers=headers, timeout=5).json())

# Update
requests.patch(f"{BASE}/users/{user['id']}", json={"name": "Asha R"}, headers=headers, timeout=5)

# Delete
requests.delete(f"{BASE}/users/{user['id']}", headers=headers, timeout=5)
```

### Python Async Client with httpx
```python
import asyncio
import httpx

async def main():
    async with httpx.AsyncClient(base_url="http://localhost:8000", timeout=5) as client:
        responses = await asyncio.gather(
            client.get("/users/1"),
            client.get("/users/2"),
            client.get("/users/3"),
        )
        for r in responses:
            print(r.status_code, r.json())

asyncio.run(main())
```

### Go REST Server with Full CRUD
```go
package main

import (
	"encoding/json"
	"log"
	"net/http"
	"strconv"
	"sync"
)

type User struct {
	ID    int    `json:"id"`
	Name  string `json:"name"`
	Email string `json:"email"`
}

type Store struct {
	mu     sync.RWMutex
	users  map[int]User
	nextID int
}

func NewStore() *Store { return &Store{users: map[int]User{}, nextID: 1} }

func writeJSON(w http.ResponseWriter, status int, v any) {
	w.Header().Set("Content-Type", "application/json")
	w.WriteHeader(status)
	json.NewEncoder(w).Encode(v)
}

func (s *Store) list(w http.ResponseWriter, r *http.Request) {
	s.mu.RLock()
	defer s.mu.RUnlock()
	out := make([]User, 0, len(s.users))
	for _, u := range s.users {
		out = append(out, u)
	}
	writeJSON(w, http.StatusOK, out)
}

func (s *Store) get(w http.ResponseWriter, r *http.Request) {
	id, _ := strconv.Atoi(r.PathValue("id"))
	s.mu.RLock()
	u, ok := s.users[id]
	s.mu.RUnlock()
	if !ok {
		writeJSON(w, http.StatusNotFound, map[string]string{"error": "user not found"})
		return
	}
	writeJSON(w, http.StatusOK, u)
}

func (s *Store) create(w http.ResponseWriter, r *http.Request) {
	var u User
	if err := json.NewDecoder(r.Body).Decode(&u); err != nil || u.Name == "" {
		writeJSON(w, http.StatusBadRequest, map[string]string{"error": "invalid body"})
		return
	}
	s.mu.Lock()
	u.ID = s.nextID
	s.nextID++
	s.users[u.ID] = u
	s.mu.Unlock()
	w.Header().Set("Location", "/users/"+strconv.Itoa(u.ID))
	writeJSON(w, http.StatusCreated, u)
}

func (s *Store) delete(w http.ResponseWriter, r *http.Request) {
	id, _ := strconv.Atoi(r.PathValue("id"))
	s.mu.Lock()
	delete(s.users, id)
	s.mu.Unlock()
	w.WriteHeader(http.StatusNoContent)
}

func main() {
	s := NewStore()
	mux := http.NewServeMux()
	mux.HandleFunc("GET /users", s.list)
	mux.HandleFunc("GET /users/{id}", s.get)
	mux.HandleFunc("POST /users", s.create)
	mux.HandleFunc("DELETE /users/{id}", s.delete)

	log.Println("listening on :8080")
	log.Fatal(http.ListenAndServe(":8080", mux))
}
```

### Go REST Client
```go
package main

import (
	"bytes"
	"encoding/json"
	"fmt"
	"io"
	"net/http"
	"time"
)

func main() {
	client := &http.Client{Timeout: 5 * time.Second}

	body, _ := json.Marshal(map[string]string{"name": "Asha", "email": "asha@example.com"})
	req, _ := http.NewRequest(http.MethodPost, "http://localhost:8080/users", bytes.NewReader(body))
	req.Header.Set("Content-Type", "application/json")
	req.Header.Set("Authorization", "Bearer my-token")

	resp, err := client.Do(req)
	if err != nil {
		panic(err)
	}
	defer resp.Body.Close()

	data, _ := io.ReadAll(resp.Body)
	fmt.Println(resp.StatusCode, string(data))
}
```

### Go Middleware
Middleware wraps a handler to add behavior before or after it runs.

```go
func logging(next http.Handler) http.Handler {
	return http.HandlerFunc(func(w http.ResponseWriter, r *http.Request) {
		start := time.Now()
		next.ServeHTTP(w, r)
		log.Printf("%s %s %s", r.Method, r.URL.Path, time.Since(start))
	})
}

func authRequired(next http.Handler) http.Handler {
	return http.HandlerFunc(func(w http.ResponseWriter, r *http.Request) {
		if r.Header.Get("Authorization") == "" {
			http.Error(w, "unauthorized", http.StatusUnauthorized)
			return
		}
		next.ServeHTTP(w, r)
	})
}

// usage
// http.ListenAndServe(":8080", logging(authRequired(mux)))
```

### Go Request Timeouts with Context
```go
ctx, cancel := context.WithTimeout(context.Background(), 3*time.Second)
defer cancel()

req, _ := http.NewRequestWithContext(ctx, http.MethodGet, "https://api.example.com/slow", nil)
resp, err := http.DefaultClient.Do(req)
```

## REST Best Practices

### Consistent Naming
One casing style and plural nouns throughout.

### Proper Status Code Usage
Reflect the actual outcome of the request.

### Stateless Design
No server-side session dependence; send credentials with each request.

### Meaningful Error Messages
Actionable text with machine-readable codes.

### Pagination for Collections
Never return unbounded lists.

### Filtering and Sorting Conventions
Same parameter names across all collections.

### Documentation with OpenAPI
Maintain an accurate, published specification.

### Versioning from the Start
Include `/v1` in the base path from the first release.

### Avoiding Chatty APIs
Provide expansion, bulk endpoints, or composite resources to reduce round trips.

### Idempotent Design
Make retries safe.

## REST Anti-Patterns
- Verbs in URLs (`/getUsers`)
- Ignoring HTTP methods (everything through POST)
- Always returning 200, even for errors
- Deep nesting
- Exposing database structure directly
- Inconsistent response format
- Missing pagination
- Overloading POST for every action
- Leaking internal errors and stack traces

## REST Tools and Clients
- curl
- HTTPie
- Postman
- Insomnia
- Python `requests`
- `httpx`
- Fetch API
- Axios

```bash
# HTTPie examples
http GET  localhost:8000/users
http POST localhost:8000/users name=Asha email=asha@example.com
http -A bearer -a "$TOKEN" GET localhost:8000/profile
```

## REST Compared with Other Styles

### REST and SOAP
REST is lightweight, flexible, and format-agnostic; SOAP is strict, XML-only, and includes standards for security and transactions.

### REST and GraphQL
REST exposes many resource endpoints; GraphQL exposes one endpoint with client-defined queries, avoiding over-fetching and under-fetching.

### REST and gRPC
REST favors broad compatibility and human readability; gRPC favors speed, streaming, and strict typing.

---

# FastAPI

## FastAPI Overview

### Framework Introduction
FastAPI is a modern, high-performance Python web framework for building APIs using standard Python type hints. It is built on Starlette (web layer) and Pydantic (data validation), and runs on the ASGI standard.

### Core Features
- Automatic interactive documentation
- Type-hint-based validation
- Dependency injection
- Native async support
- OpenAPI and JSON Schema generation
- Editor support and autocompletion

### Underlying Technologies
- **Starlette**: lightweight ASGI toolkit providing routing, middleware, and WebSockets
- **Pydantic**: data validation and serialization using type annotations
- **Uvicorn**: ASGI server that runs the application
- **ASGI specification**: asynchronous server-application interface
- **Python type hints**: declarations that drive validation and documentation

### FastAPI Compared with Flask and Django

| Aspect | FastAPI | Flask | Django |
|--------|---------|-------|--------|
| Interface | ASGI | WSGI | WSGI and ASGI |
| Async support | Native | Limited | Partial |
| Validation | Built-in (Pydantic) | Extension needed | Forms and serializers |
| API docs | Automatic | Extension needed | Extension needed (DRF) |
| Included features | Minimal, focused on APIs | Minimal | Full-stack (ORM, admin, auth) |

## Prerequisites

### Python Fundamentals
- Functions and classes
- Modules and packages
- Decorators
- Generators
- Context managers

### Python Type Hints
Annotations declaring the expected type of variables, parameters, and return values.

```python
from typing import Annotated, Literal, Optional

def greet(name: str, age: Optional[int] = None) -> str:
    return f"Hello {name}"

Status = Literal["active", "inactive"]
Port = Annotated[int, "network port"]
names: list[str] = ["Asha", "Ravi"]
scores: dict[str, float] = {"Asha": 9.5}
```

- Basic types
- `Optional` and `Union` (or `X | None`)
- `List`, `Dict`, `Tuple`, `Set` (or lowercase built-ins)
- `Annotated`
- Generics
- `Literal`

### Asynchronous Programming in Python
- **`async` and `await`**: syntax for defining and awaiting coroutines
- **Event loop**: scheduler that runs coroutines
- **Coroutines**: functions defined with `async def`
- **`asyncio`**: standard library module for async programming
- **Concurrency and parallelism**: concurrency interleaves tasks; parallelism runs them simultaneously on multiple cores

```python
import asyncio

async def fetch(n: int) -> int:
    await asyncio.sleep(1)  # non-blocking wait
    return n * 2

async def main():
    results = await asyncio.gather(fetch(1), fetch(2), fetch(3))
    print(results)  # completes in about 1 second

asyncio.run(main())
```

### Virtual Environments
Isolated Python environments with their own packages.
- venv
- virtualenv
- pipenv
- poetry
- uv

```bash
python -m venv .venv
source .venv/bin/activate          # Linux and macOS
.venv\Scripts\activate             # Windows
pip install --upgrade pip
```

## Installation and Setup

### Environment Preparation
Install Python 3.9 or later, create a virtual environment, and activate it.

### Package Installation
- `fastapi`: the framework
- `uvicorn`: the server
- `fastapi[standard]`: framework plus common extras (CLI, Uvicorn, httpx, Jinja2, python-multipart)

```bash
pip install "fastapi[standard]"
# or minimal
pip install fastapi "uvicorn[standard]"
# with uv
uv add "fastapi[standard]"
```

### Project Initialization
```bash
mkdir my-api && cd my-api
python -m venv .venv && source .venv/bin/activate
pip install "fastapi[standard]"
pip freeze > requirements.txt
touch main.py
```

### Running the Server

```bash
# Uvicorn
uvicorn main:app --reload --host 0.0.0.0 --port 8000

# FastAPI CLI
fastapi dev main.py     # development with auto-reload
fastapi run main.py     # production-style run
```

- `main:app` means module `main`, variable `app`
- `--reload` restarts on code change (development only)

### First Application
```python
from fastapi import FastAPI

app = FastAPI()

@app.get("/")
async def root():
    return {"message": "Hello World"}
```

```bash
curl http://127.0.0.1:8000/
# {"message":"Hello World"}
```

Interactive docs are served at `http://127.0.0.1:8000/docs` (Swagger UI) and `/redoc` (ReDoc).

## ASGI and Servers

### ASGI Specification
Asynchronous Server Gateway Interface, a standard between async Python web servers and applications, supporting HTTP, WebSockets, and lifespan events.

### WSGI and ASGI Differences
WSGI is synchronous and handles one request per worker thread or process at a time. ASGI is asynchronous and can manage many concurrent connections, including long-lived ones.

### Uvicorn
Fast ASGI server based on `uvloop` and `httptools`.

### Gunicorn with Uvicorn Workers
Gunicorn as process manager supervising multiple Uvicorn workers.

```bash
gunicorn main:app -k uvicorn.workers.UvicornWorker -w 4 -b 0.0.0.0:8000
```

### Hypercorn
ASGI server supporting HTTP/2 and HTTP/3.

### Daphne
ASGI server from the Django Channels project.

### Worker Processes and Concurrency
Each worker is a separate process with its own event loop. A common starting point is one worker per CPU core.

```bash
uvicorn main:app --workers 4
```

## Application Instance

### FastAPI Class Parameters
- `title`, `description`, `version`: documentation metadata
- `docs_url`, `redoc_url`, `openapi_url`: documentation paths (set to `None` to disable)
- `openapi_tags`: tag descriptions
- `lifespan`: startup and shutdown context manager

```python
app = FastAPI(
    title="User Service",
    description="Manages users and orders",
    version="1.0.0",
    docs_url="/docs",
    redoc_url="/redoc",
)
```

### Application Metadata
Title, version, contact, license, and terms of service appear in the generated OpenAPI document.

### Lifespan Events
A single async context manager that runs code before the application starts serving and after it stops.

```python
from contextlib import asynccontextmanager
from fastapi import FastAPI

@asynccontextmanager
async def lifespan(app: FastAPI):
    # startup: open connections, load models
    app.state.db = await connect_to_database()
    yield
    # shutdown: release resources
    await app.state.db.close()

app = FastAPI(lifespan=lifespan)
```

## Path Operations

### Path Operation Decorators
A path operation is a combination of an HTTP method and a path bound to a function.

```python
@app.get("/items")
@app.post("/items")
@app.put("/items/{item_id}")
@app.patch("/items/{item_id}")
@app.delete("/items/{item_id}")
@app.options("/items")
@app.head("/items")
```

### Path Operation Functions
- `def` functions run in a thread pool, suitable for blocking code
- `async def` functions run on the event loop, suitable for awaitable code

```python
@app.get("/sync")
def sync_endpoint():
    return {"mode": "sync"}

@app.get("/async")
async def async_endpoint():
    return {"mode": "async"}
```

### Path Operation Configuration
```python
@app.post(
    "/items",
    status_code=201,
    tags=["items"],
    summary="Create an item",
    description="Creates an item and returns it.",
    response_description="The created item",
    deprecated=False,
    operation_id="create_item",
    response_model=Item,
    responses={409: {"description": "Item already exists"}},
    include_in_schema=True,
)
async def create_item(item: Item): ...
```

### Operation Order and Route Matching
Routes match in declaration order, so fixed paths must appear before parameterized paths.

```python
@app.get("/users/me")          # declared first
async def read_me(): ...

@app.get("/users/{user_id}")
async def read_user(user_id: int): ...
```

## Path Parameters

### Declaring Path Parameters
Variables in the path, declared in braces, become function arguments.

```python
@app.get("/items/{item_id}")
async def read_item(item_id: int):
    return {"item_id": item_id}
```

### Type Conversion and Validation
`/items/5` converts to integer 5. `/items/abc` returns an automatic `422` error.

### Enum Path Parameters
```python
from enum import Enum

class Role(str, Enum):
    admin = "admin"
    editor = "editor"
    viewer = "viewer"

@app.get("/roles/{role}")
async def read_role(role: Role):
    return {"role": role.value}
```

### Path Convertors (`:path`)
Capture a path that contains slashes.

```python
@app.get("/files/{file_path:path}")
async def read_file(file_path: str):
    return {"file_path": file_path}
```

### Path Validation with `Path()`
```python
from typing import Annotated
from fastapi import Path

@app.get("/items/{item_id}")
async def read_item(
    item_id: Annotated[int, Path(title="Item ID", ge=1, le=10000, description="Unique item identifier")],
):
    return {"item_id": item_id}
```

Supported constraints: `gt`, `ge`, `lt`, `le`, `min_length`, `max_length`, `pattern`, `title`, `description`, `example`.

## Query Parameters

### Declaring Query Parameters
Function parameters not present in the path are interpreted as query parameters.

```python
@app.get("/items")
async def list_items(skip: int = 0, limit: int = 10):
    return {"skip": skip, "limit": limit}
```

```bash
curl "http://127.0.0.1:8000/items?skip=20&limit=5"
```

### Default Values and Optional Parameters
```python
@app.get("/search")
async def search(q: str | None = None):
    return {"q": q}
```

### Required Query Parameters
A parameter without a default is required.

```python
@app.get("/search")
async def search(q: str):
    return {"q": q}
```

### Type Conversion
Booleans accept `true`, `1`, `yes`, `on`; numbers are converted automatically.

### Query Validation with `Query()`
```python
from typing import Annotated
from fastapi import Query

@app.get("/search")
async def search(
    q: Annotated[str | None, Query(min_length=3, max_length=50, pattern="^[a-z]+$", alias="search-term")] = None,
):
    return {"q": q}
```

Options include `min_length`, `max_length`, `pattern`, `alias`, `deprecated`, and `include_in_schema`.

### Multiple Values (List Query Parameters)
```python
@app.get("/items")
async def list_items(tag: Annotated[list[str] | None, Query()] = None):
    return {"tag": tag}
```

```bash
curl "http://127.0.0.1:8000/items?tag=a&tag=b"
```

### Query Parameter Models
Group related query parameters in a Pydantic model.

```python
from pydantic import BaseModel, Field

class FilterParams(BaseModel):
    limit: int = Field(100, gt=0, le=100)
    offset: int = Field(0, ge=0)
    order_by: str = "created_at"

@app.get("/items")
async def list_items(filters: Annotated[FilterParams, Query()]):
    return filters
```

## Request Body

### Pydantic Models as Request Bodies
```python
from pydantic import BaseModel

class Item(BaseModel):
    name: str
    price: float
    description: str | None = None

@app.post("/items", status_code=201)
async def create_item(item: Item):
    return item
```

```bash
curl -X POST http://127.0.0.1:8000/items \
  -H "Content-Type: application/json" \
  -d '{"name": "Notebook", "price": 49.5}'
```

### Multiple Body Parameters
```python
class User(BaseModel):
    username: str

@app.put("/items/{item_id}")
async def update_item(item_id: int, item: Item, user: User):
    return {"item_id": item_id, "item": item, "user": user}
# Expected body: {"item": {...}, "user": {...}}
```

### Singular Values in Body with `Body()`
```python
from fastapi import Body

@app.put("/items/{item_id}")
async def update_item(item_id: int, importance: Annotated[int, Body()]):
    return {"importance": importance}
```

### Embedded Body Parameters (`embed=True`)
```python
@app.put("/items/{item_id}")
async def update_item(item_id: int, item: Annotated[Item, Body(embed=True)]):
    return item
# Expected body: {"item": {...}}
```

### Nested Models
```python
class Image(BaseModel):
    url: str
    name: str

class Product(BaseModel):
    name: str
    images: list[Image] = []
```

### Body Fields with `Field()`
```python
from pydantic import Field

class Item(BaseModel):
    name: str = Field(min_length=1, max_length=100)
    price: float = Field(gt=0, description="Price must be positive")
    quantity: int = Field(default=1, ge=1)
```

### Mixing Path, Query and Body Parameters
FastAPI identifies each source automatically: path variables from the URL pattern, Pydantic models as body, and remaining simple types as query parameters.

```python
@app.put("/items/{item_id}")
async def update_item(item_id: int, item: Item, notify: bool = False):
    ...
```

### Request Body Examples
```python
class Item(BaseModel):
    name: str
    price: float

    model_config = {
        "json_schema_extra": {
            "examples": [{"name": "Notebook", "price": 49.5}]
        }
    }
```

## Pydantic

### BaseModel
The base class for validated data models. Instances are validated on creation and raise `ValidationError` for invalid data.

```python
from pydantic import BaseModel, ValidationError

class User(BaseModel):
    id: int
    name: str

try:
    User(id="abc", name="Asha")
except ValidationError as e:
    print(e)
```

### Field Types
- `str`, `int`, `float`, `bool`
- `list`, `dict`, `set`, `tuple`
- `datetime`, `date`, `time`, `timedelta`
- `UUID`
- `Decimal`
- `EmailStr` (requires `email-validator`)
- `HttpUrl`
- `SecretStr`
- `Enum`

### Field Constraints
Limits applied through `Field()` or constrained types: `gt`, `ge`, `lt`, `le`, `min_length`, `max_length`, `pattern`, `multiple_of`.

### Optional Fields and Default Values
```python
class Profile(BaseModel):
    bio: str | None = None
    tags: list[str] = []
    active: bool = True
```

### Validators
- `field_validator`: validates a single field
- `model_validator`: validates the entire model
- Modes: `before` (raw input) and `after` (parsed value)

```python
from pydantic import BaseModel, field_validator, model_validator

class Signup(BaseModel):
    username: str
    password: str
    confirm: str

    @field_validator("username")
    @classmethod
    def username_lowercase(cls, v: str) -> str:
        return v.lower()

    @model_validator(mode="after")
    def passwords_match(self):
        if self.password != self.confirm:
            raise ValueError("passwords do not match")
        return self
```

### Computed Fields
```python
from pydantic import computed_field

class Rectangle(BaseModel):
    width: float
    height: float

    @computed_field
    @property
    def area(self) -> float:
        return self.width * self.height
```

### Model Configuration (`model_config`)
```python
from pydantic import ConfigDict

class User(BaseModel):
    model_config = ConfigDict(
        from_attributes=True,        # read from ORM objects
        populate_by_name=True,       # allow field name and alias
        extra="forbid",              # reject unknown fields
        str_strip_whitespace=True,
    )
    id: int
    name: str
```

### Aliases and Serialization Aliases
```python
from pydantic import Field

class Item(BaseModel):
    item_name: str = Field(alias="itemName")
```

### Model Serialization
```python
user = User(id=1, name="Asha")
user.model_dump()                     # dict
user.model_dump(exclude_unset=True)   # only fields that were set
user.model_dump_json()                # JSON string
User.model_validate({"id": 1, "name": "Asha"})
User.model_validate_json('{"id": 1, "name": "Asha"}')
user.model_copy(update={"name": "Asha R"})
```

### Nested and Recursive Models
Models can contain other models, including references to themselves (tree structures).

### Generic Models
```python
from typing import Generic, TypeVar
from pydantic import BaseModel

T = TypeVar("T")

class Page(BaseModel, Generic[T]):
    data: list[T]
    total: int

# Page[User] validates a page of users
```

### Discriminated Unions
Choose the correct model in a union using a type field.

```python
from typing import Literal, Union
from pydantic import Field

class Cat(BaseModel):
    pet_type: Literal["cat"]
    meows: int

class Dog(BaseModel):
    pet_type: Literal["dog"]
    barks: float

class Owner(BaseModel):
    pet: Union[Cat, Dog] = Field(discriminator="pet_type")
```

### Pydantic Settings
`BaseSettings` reads configuration from environment variables and `.env` files.

```python
# pip install pydantic-settings
from pydantic_settings import BaseSettings, SettingsConfigDict

class Settings(BaseSettings):
    app_name: str = "My API"
    database_url: str
    secret_key: str
    model_config = SettingsConfigDict(env_file=".env")

settings = Settings()
```

### Pydantic v1 and v2 Differences
- `model_dump()` replaces `dict()`
- `model_validate()` replaces `parse_obj()`
- `field_validator` replaces `validator`
- `model_config` replaces inner `class Config`
- `pattern` replaces `regex`
- Rust-based core makes v2 much faster

## Other Parameter Types

### Header Parameters
```python
from typing import Annotated
from fastapi import Header

@app.get("/info")
async def info(user_agent: Annotated[str | None, Header()] = None,
               x_token: Annotated[list[str] | None, Header()] = None):
    return {"User-Agent": user_agent, "X-Token": x_token}
```
- Underscores convert to hyphens automatically (`user_agent` reads `User-Agent`)
- Duplicate headers collect into a list

### Cookie Parameters
```python
from fastapi import Cookie

@app.get("/me")
async def me(session_id: Annotated[str | None, Cookie()] = None):
    return {"session_id": session_id}
```

### Form Data
Requires `python-multipart`.

```python
from fastapi import Form

@app.post("/login")
async def login(username: Annotated[str, Form()], password: Annotated[str, Form()]):
    return {"username": username}
```

```bash
curl -X POST http://127.0.0.1:8000/login -d "username=asha&password=secret"
```

### File Uploads
- `bytes`: whole file in memory, for small files
- `UploadFile`: spooled file object, suitable for large files

```python
from fastapi import File, UploadFile

@app.post("/upload")
async def upload(file: UploadFile):
    content = await file.read()
    return {"filename": file.filename, "content_type": file.content_type, "size": len(content)}

@app.post("/upload-many")
async def upload_many(files: list[UploadFile]):
    return {"names": [f.filename for f in files]}
```

```bash
curl -X POST http://127.0.0.1:8000/upload -F "file=@report.pdf"
curl -X POST http://127.0.0.1:8000/upload-many -F "files=@a.png" -F "files=@b.png"
```

### Request Object
Direct access to low-level request data.

```python
from fastapi import Request

@app.get("/client")
async def client_info(request: Request):
    return {"host": request.client.host, "headers": dict(request.headers), "url": str(request.url)}
```

### Parameter Models for Headers, Cookies and Forms
Group headers, cookies, or form fields into Pydantic models using `Header()`, `Cookie()`, and `Form()` with a model type.

## Response Handling

### Response Model
Declares the output schema. FastAPI filters the returned data through it, removing fields not in the model (for example, a password).

```python
class UserIn(BaseModel):
    username: str
    password: str

class UserOut(BaseModel):
    username: str

@app.post("/users", response_model=UserOut)
async def create_user(user: UserIn):
    return user  # password is filtered out
```

Options: `response_model_exclude_unset`, `response_model_exclude_defaults`, `response_model_exclude_none`, `response_model_include`, `response_model_exclude`.

### Return Type Annotations
```python
@app.get("/items/{item_id}")
async def read_item(item_id: int) -> Item:
    return Item(name="Notebook", price=49.5)
```

### Response Status Codes
```python
from fastapi import status

@app.post("/items", status_code=status.HTTP_201_CREATED)
async def create_item(item: Item): ...

@app.delete("/items/{item_id}", status_code=status.HTTP_204_NO_CONTENT)
async def delete_item(item_id: int): ...
```

### Response Classes
- `JSONResponse`: default JSON response
- `ORJSONResponse`: faster JSON using `orjson`
- `UJSONResponse`
- `HTMLResponse`
- `PlainTextResponse`
- `RedirectResponse`
- `StreamingResponse`
- `FileResponse`
- `Response`: raw custom response

```python
from fastapi.responses import HTMLResponse, RedirectResponse, FileResponse, JSONResponse

@app.get("/page", response_class=HTMLResponse)
async def page():
    return "<h1>Hello</h1>"

@app.get("/old")
async def old():
    return RedirectResponse("/new", status_code=307)

@app.get("/download")
async def download():
    return FileResponse("report.pdf", filename="report.pdf")

@app.get("/custom")
async def custom():
    return JSONResponse(content={"ok": True}, status_code=202, headers={"X-Custom": "1"})
```

### Custom Response Headers and Cookies
```python
from fastapi import Response

@app.get("/set")
async def set_values(response: Response):
    response.headers["X-Request-Source"] = "api"
    response.set_cookie(key="session", value="abc123", httponly=True, secure=True, samesite="lax")
    return {"ok": True}
```

### Multiple Response Models and Union Responses
Use `Union[A, B]` as return type or `responses={...}` to document alternatives.

### Additional Responses in OpenAPI
```python
class Message(BaseModel):
    detail: str

@app.get("/items/{item_id}", response_model=Item, responses={404: {"model": Message}})
async def read_item(item_id: int): ...
```

### Streaming Responses
```python
from fastapi.responses import StreamingResponse
import asyncio

async def event_generator():
    for i in range(5):
        yield f"chunk {i}\n"
        await asyncio.sleep(1)

@app.get("/stream")
async def stream():
    return StreamingResponse(event_generator(), media_type="text/plain")
```

```bash
curl -N http://127.0.0.1:8000/stream
```

### Server-Sent Events
```python
@app.get("/events")
async def events():
    async def gen():
        for i in range(5):
            yield f"data: tick {i}\n\n"
            await asyncio.sleep(1)
    return StreamingResponse(gen(), media_type="text/event-stream")
```

### Model Inheritance for Input and Output Schemas
Separate models for each purpose to avoid exposing or accepting unwanted fields.

```python
class UserBase(BaseModel):
    username: str
    email: str

class UserCreate(UserBase):
    password: str

class UserUpdate(BaseModel):
    email: str | None = None

class UserRead(UserBase):
    id: int

class UserDB(UserBase):
    id: int
    hashed_password: str
```

## Dependency Injection

### Dependency Concept
A dependency is a callable that FastAPI runs before a path operation, injecting its result as a parameter. It enables reusable logic, shared parameters, and separation of concerns.

### `Depends()`
```python
from fastapi import Depends

def pagination(skip: int = 0, limit: int = 10):
    return {"skip": skip, "limit": limit}

@app.get("/items")
async def list_items(page: Annotated[dict, Depends(pagination)]):
    return page
```

### Function Dependencies
Plain functions, sync or async, whose parameters follow the same rules as path operation parameters.

### Class Dependencies
```python
class Pagination:
    def __init__(self, skip: int = 0, limit: int = 10):
        self.skip = skip
        self.limit = limit

@app.get("/users")
async def list_users(page: Annotated[Pagination, Depends()]):
    return {"skip": page.skip, "limit": page.limit}
```

### Sub-Dependencies
A dependency may depend on other dependencies, forming a tree resolved automatically.

```python
def get_settings(): ...

def get_db(settings: Annotated[Settings, Depends(get_settings)]): ...

def get_current_user(db: Annotated[Session, Depends(get_db)], token: str = Depends(oauth2_scheme)): ...
```

### Dependencies with `yield`
Run setup before the request and cleanup after the response.

```python
def get_db():
    db = SessionLocal()
    try:
        yield db
    finally:
        db.close()

@app.get("/users")
def list_users(db: Annotated[Session, Depends(get_db)]):
    return db.query(User).all()
```

### Dependencies in Decorators
Run a dependency for its side effect when the return value is not needed.

```python
async def verify_token(x_token: Annotated[str, Header()]):
    if x_token != "secret":
        raise HTTPException(status_code=400, detail="Invalid X-Token")

@app.get("/secure", dependencies=[Depends(verify_token)])
async def secure(): ...
```

### Global Dependencies
```python
app = FastAPI(dependencies=[Depends(verify_token)])
```

### Router-Level Dependencies
```python
router = APIRouter(prefix="/admin", dependencies=[Depends(verify_admin)])
```

### Dependency Caching (`use_cache`)
Within one request, the same dependency is evaluated once and reused. Disable with `Depends(func, use_cache=False)`.

### Dependency Overrides for Testing
```python
def override_get_db():
    yield test_db

app.dependency_overrides[get_db] = override_get_db
```

### Annotated Dependencies
Reusable type aliases reduce repetition.

```python
DbSession = Annotated[Session, Depends(get_db)]
CurrentUser = Annotated[User, Depends(get_current_user)]

@app.get("/me")
async def me(user: CurrentUser, db: DbSession): ...
```

### Common Dependency Use Cases
- Authentication
- Pagination parameters
- Database sessions
- Permission checks
- Configuration access

## Routers and Project Structure

### APIRouter
A mini-application used to group related path operations in separate modules.
- `prefix`
- `tags`
- `dependencies`
- `responses`

```python
# app/routers/users.py
from fastapi import APIRouter

router = APIRouter(prefix="/users", tags=["users"], responses={404: {"description": "Not found"}})

@router.get("/")
async def list_users(): ...

@router.get("/{user_id}")
async def read_user(user_id: int): ...
```

### Including Routers
```python
# app/main.py
from fastapi import FastAPI
from app.routers import users, items

app = FastAPI()
app.include_router(users.router)
app.include_router(items.router, prefix="/api/v1")
```

### Nested Routers
Routers can include other routers to build hierarchies.

### Recommended Project Layout
```text
my-api/
├── app/
│   ├── main.py
│   ├── core/
│   │   └── config.py
│   ├── db/
│   │   ├── base.py
│   │   └── session.py
│   ├── models/          # database models
│   ├── schemas/         # Pydantic models
│   ├── crud/            # database operations
│   ├── services/        # business logic
│   ├── routers/         # path operations
│   └── dependencies/    # shared dependencies
├── tests/
├── alembic/
├── requirements.txt
├── Dockerfile
└── .env
```

### Modular Application Design
Routers handle HTTP, services hold business rules, CRUD modules talk to the database, and schemas define data shapes.

### Configuration Management
Centralize settings in `core/config.py` using `BaseSettings`.

### Versioned Routers
```python
from fastapi import APIRouter

api_v1 = APIRouter(prefix="/api/v1")
api_v1.include_router(users.router)
app.include_router(api_v1)
```

### Sub-Applications and Mounting
```python
from fastapi.staticfiles import StaticFiles

app.mount("/static", StaticFiles(directory="static"), name="static")

sub_app = FastAPI()
app.mount("/subapi", sub_app)
```

## Error Handling in FastAPI

### HTTPException
Raises an HTTP error response from anywhere in the application.

```python
from fastapi import HTTPException

items = {"foo": "The Foo Item"}

@app.get("/items/{item_id}")
async def read_item(item_id: str):
    if item_id not in items:
        raise HTTPException(
            status_code=404,
            detail="Item not found",
            headers={"X-Error": "item-missing"},
        )
    return {"item": items[item_id]}
```

### Custom Exceptions
```python
from fastapi import Request
from fastapi.responses import JSONResponse

class OutOfStockError(Exception):
    def __init__(self, product_id: int):
        self.product_id = product_id

@app.exception_handler(OutOfStockError)
async def out_of_stock_handler(request: Request, exc: OutOfStockError):
    return JSONResponse(
        status_code=409,
        content={"error": "OUT_OF_STOCK", "product_id": exc.product_id},
    )
```

### Exception Handlers
`@app.exception_handler(ExceptionClass)` registers a function that converts an exception into a response, and can override default handlers.

### Request Validation Errors
Invalid input produces a `422 Unprocessable Entity` response listing each problem.

```json
{
  "detail": [
    {
      "type": "int_parsing",
      "loc": ["path", "item_id"],
      "msg": "Input should be a valid integer",
      "input": "abc"
    }
  ]
}
```

Customize with `RequestValidationError`:

```python
from fastapi.exceptions import RequestValidationError

@app.exception_handler(RequestValidationError)
async def validation_handler(request: Request, exc: RequestValidationError):
    return JSONResponse(status_code=400, content={"errors": exc.errors()})
```

### Response Validation Errors
If returned data does not match `response_model`, FastAPI raises `ResponseValidationError`, resulting in a `500` response.

### Starlette HTTPException
FastAPI's `HTTPException` extends Starlette's. Register handlers for the Starlette class to catch both.

```python
from starlette.exceptions import HTTPException as StarletteHTTPException

@app.exception_handler(StarletteHTTPException)
async def http_exception_handler(request, exc):
    return JSONResponse(status_code=exc.status_code, content={"error": exc.detail})
```

### Global Exception Handling
```python
@app.exception_handler(Exception)
async def unhandled(request: Request, exc: Exception):
    return JSONResponse(status_code=500, content={"error": "Internal server error"})
```

### Error Response Standardization
Use one error schema everywhere and register handlers that produce it.

## Authentication and Security

### Security Utilities
`fastapi.security` offers ready-made schemes that also appear in the Swagger UI "Authorize" dialog.
- `OAuth2PasswordBearer`
- `OAuth2PasswordRequestForm`
- `HTTPBasic`
- `HTTPBearer`
- `APIKeyHeader`
- `APIKeyQuery`
- `APIKeyCookie`
- `SecurityScopes`

### Password Hashing
Use `passlib` with bcrypt, `argon2`, or `pwdlib`.

```python
from passlib.context import CryptContext

pwd_context = CryptContext(schemes=["bcrypt"], deprecated="auto")

def hash_password(password: str) -> str:
    return pwd_context.hash(password)

def verify_password(plain: str, hashed: str) -> bool:
    return pwd_context.verify(plain, hashed)
```

### OAuth2 with Password Flow and JWT Authentication
Complete login and protected-route example.

```python
from datetime import datetime, timedelta, timezone
from typing import Annotated

import jwt
from fastapi import Depends, FastAPI, HTTPException, status
from fastapi.security import OAuth2PasswordBearer, OAuth2PasswordRequestForm
from jwt.exceptions import InvalidTokenError
from pydantic import BaseModel

SECRET_KEY = "replace-with-a-long-random-secret"   # openssl rand -hex 32
ALGORITHM = "HS256"
ACCESS_TOKEN_MINUTES = 30

app = FastAPI()
oauth2_scheme = OAuth2PasswordBearer(tokenUrl="token")

fake_users = {
    "asha": {"username": "asha", "hashed_password": hash_password("secret"), "role": "admin"},
}

class Token(BaseModel):
    access_token: str
    token_type: str

class User(BaseModel):
    username: str
    role: str

def create_access_token(data: dict) -> str:
    payload = data.copy()
    payload["exp"] = datetime.now(timezone.utc) + timedelta(minutes=ACCESS_TOKEN_MINUTES)
    return jwt.encode(payload, SECRET_KEY, algorithm=ALGORITHM)

async def get_current_user(token: Annotated[str, Depends(oauth2_scheme)]) -> User:
    credentials_error = HTTPException(
        status_code=status.HTTP_401_UNAUTHORIZED,
        detail="Could not validate credentials",
        headers={"WWW-Authenticate": "Bearer"},
    )
    try:
        payload = jwt.decode(token, SECRET_KEY, algorithms=[ALGORITHM])
        username = payload.get("sub")
        if username is None:
            raise credentials_error
    except InvalidTokenError:
        raise credentials_error
    user = fake_users.get(username)
    if user is None:
        raise credentials_error
    return User(username=user["username"], role=user["role"])

@app.post("/token")
async def login(form: Annotated[OAuth2PasswordRequestForm, Depends()]) -> Token:
    user = fake_users.get(form.username)
    if not user or not verify_password(form.password, user["hashed_password"]):
        raise HTTPException(status_code=400, detail="Incorrect username or password")
    return Token(access_token=create_access_token({"sub": user["username"]}), token_type="bearer")

@app.get("/users/me")
async def read_me(current_user: Annotated[User, Depends(get_current_user)]):
    return current_user
```

```bash
# Obtain a token
TOKEN=$(curl -s -X POST http://127.0.0.1:8000/token \
  -d "username=asha&password=secret" | jq -r .access_token)

# Call a protected endpoint
curl -H "Authorization: Bearer $TOKEN" http://127.0.0.1:8000/users/me
```

- Token creation and verification
- Expiry and claims (`sub`, `exp`)
- Refresh tokens issued by a separate endpoint

### OAuth2 Scopes
```python
from fastapi import Security
from fastapi.security import OAuth2PasswordBearer, SecurityScopes

oauth2_scheme = OAuth2PasswordBearer(
    tokenUrl="token",
    scopes={"items:read": "Read items", "items:write": "Create and edit items"},
)

async def get_user_with_scopes(security_scopes: SecurityScopes, token: Annotated[str, Depends(oauth2_scheme)]):
    ...  # compare security_scopes.scopes with token scopes

@app.get("/items", dependencies=[Security(get_user_with_scopes, scopes=["items:read"])])
async def read_items(): ...
```

### Current User Dependency
A dependency that decodes the token and returns the user, reused across protected routes (see example above).

### Role-Based Access Control
```python
def require_role(*roles: str):
    async def checker(user: Annotated[User, Depends(get_current_user)]):
        if user.role not in roles:
            raise HTTPException(status_code=403, detail="Insufficient permissions")
        return user
    return checker

@app.delete("/users/{user_id}", dependencies=[Depends(require_role("admin"))])
async def delete_user(user_id: int): ...
```

### API Key Authentication
```python
from fastapi.security import APIKeyHeader

api_key_header = APIKeyHeader(name="X-API-Key")

async def verify_api_key(key: Annotated[str, Depends(api_key_header)]):
    if key != "expected-key":
        raise HTTPException(status_code=403, detail="Invalid API key")

@app.get("/data", dependencies=[Depends(verify_api_key)])
async def data(): ...
```

### HTTP Basic Authentication
```python
import secrets
from fastapi.security import HTTPBasic, HTTPBasicCredentials

basic = HTTPBasic()

@app.get("/admin")
async def admin(credentials: Annotated[HTTPBasicCredentials, Depends(basic)]):
    ok = secrets.compare_digest(credentials.username, "admin") and \
         secrets.compare_digest(credentials.password, "s3cret")
    if not ok:
        raise HTTPException(status_code=401, headers={"WWW-Authenticate": "Basic"})
    return {"user": credentials.username}
```

### Third-Party Authentication
Delegating login to external identity providers.
- Google
- GitHub
- Auth0
- Keycloak
- Firebase

### CORS Middleware
```python
from fastapi.middleware.cors import CORSMiddleware

app.add_middleware(
    CORSMiddleware,
    allow_origins=["https://app.example.com"],
    allow_credentials=True,
    allow_methods=["GET", "POST", "PUT", "DELETE"],
    allow_headers=["Authorization", "Content-Type"],
)
```

### HTTPS Redirect Middleware
```python
from fastapi.middleware.httpsredirect import HTTPSRedirectMiddleware
app.add_middleware(HTTPSRedirectMiddleware)
```

### Trusted Host Middleware
```python
from fastapi.middleware.trustedhost import TrustedHostMiddleware
app.add_middleware(TrustedHostMiddleware, allowed_hosts=["example.com", "*.example.com"])
```

### Security Best Practices
- Hash passwords with bcrypt or argon2
- Keep secrets out of source control
- Use short-lived access tokens
- Restrict CORS origins
- Validate every input through Pydantic models
- Return generic messages for authentication failures
- Enforce HTTPS

## Middleware

### Middleware Concept
Code that processes every request before it reaches a path operation and every response before it returns to the client.

### Custom Middleware (`@app.middleware("http")`)
```python
import time
import uuid

@app.middleware("http")
async def add_process_time(request: Request, call_next):
    request_id = str(uuid.uuid4())
    start = time.perf_counter()
    response = await call_next(request)
    response.headers["X-Process-Time"] = f"{time.perf_counter() - start:.4f}"
    response.headers["X-Request-ID"] = request_id
    return response
```

### Built-In Middleware
- `CORSMiddleware`
- `GZipMiddleware`
- `HTTPSRedirectMiddleware`
- `TrustedHostMiddleware`
- `SessionMiddleware`

```python
from fastapi.middleware.gzip import GZipMiddleware
app.add_middleware(GZipMiddleware, minimum_size=1000)
```

### Pure ASGI Middleware
A class implementing `__call__(scope, receive, send)`, offering finer control and lower overhead than `@app.middleware("http")`.

### Middleware Execution Order
The last middleware added runs first on the way in and last on the way out.

### Common Middleware Use Cases
- Logging
- Request timing
- Request ID injection
- Authentication
- Compression
- Rate limiting

## Background Tasks

### `BackgroundTasks`
Run work after the response has been sent, without blocking the client.

```python
from fastapi import BackgroundTasks

def write_log(message: str):
    with open("log.txt", "a") as f:
        f.write(message + "\n")

@app.post("/signup")
async def signup(email: str, background_tasks: BackgroundTasks):
    background_tasks.add_task(write_log, f"signup: {email}")
    return {"message": "Signup received"}
```

### Adding Tasks to Responses
Tasks may be added in the path operation function or in dependencies; they all run after the response.

### Tasks in Dependencies
```python
def log_query(background_tasks: BackgroundTasks, q: str | None = None):
    if q:
        background_tasks.add_task(write_log, f"query: {q}")
    return q
```

### Background Tasks and Task Queues
Use a task queue for heavy, long-running, or retryable work.
- Celery
- ARQ
- RQ
- Dramatiq

```python
@app.post("/reports")
async def create_report(user_id: int):
    task = generate_report.delay(user_id)       # Celery task
    return {"task_id": task.id, "status": "queued"}
```

### Scheduled Jobs
```python
from apscheduler.schedulers.asyncio import AsyncIOScheduler

scheduler = AsyncIOScheduler()
scheduler.add_job(cleanup_expired_sessions, "interval", minutes=15)

@asynccontextmanager
async def lifespan(app: FastAPI):
    scheduler.start()
    yield
    scheduler.shutdown()
```

## Async and Concurrency

### `async def` and `def` Path Operations
- Use `async def` when the function awaits async libraries
- Use `def` when the function calls blocking libraries; FastAPI runs it in a thread pool

### Thread Pool Execution for Sync Functions
```python
from fastapi.concurrency import run_in_threadpool

@app.get("/report")
async def report():
    data = await run_in_threadpool(blocking_function)
    return data
```

### Blocking Operations
Calling `time.sleep()` or a synchronous HTTP library inside `async def` blocks the entire event loop. Replace with `await asyncio.sleep()` or async libraries.

```python
# Incorrect: blocks the event loop
@app.get("/bad")
async def bad():
    time.sleep(5)

# Correct
@app.get("/good")
async def good():
    await asyncio.sleep(5)
```

### Async Database Drivers
- asyncpg
- aiomysql
- aiosqlite
- Motor (MongoDB)

### Async HTTP Clients
```python
import httpx

@app.get("/proxy")
async def proxy():
    async with httpx.AsyncClient() as client:
        r = await client.get("https://api.github.com/users/octocat")
    return r.json()
```

### Concurrency Patterns
- `asyncio.gather` for parallel awaits
- Task groups for structured concurrency
- Semaphores to cap simultaneous operations

```python
sem = asyncio.Semaphore(10)

async def limited_fetch(client, url):
    async with sem:
        return await client.get(url)
```

### Performance Considerations
CPU-bound work needs multiple processes (workers or process pools); async helps with I/O-bound work.

## Database Integration

### SQLAlchemy
The standard Python ORM and SQL toolkit.
- Engine: connection source
- Session: unit of work
- Declarative models
- Relationships
- Core and ORM layers
- Async support through `AsyncSession`

### SQLModel
A library by the FastAPI author combining SQLAlchemy and Pydantic in single class definitions.

```python
from sqlmodel import Field, Session, SQLModel, create_engine, select

class Hero(SQLModel, table=True):
    id: int | None = Field(default=None, primary_key=True)
    name: str
    age: int | None = None

engine = create_engine("sqlite:///database.db")
SQLModel.metadata.create_all(engine)

def get_session():
    with Session(engine) as session:
        yield session

@app.post("/heroes", response_model=Hero)
def create_hero(hero: Hero, session: Annotated[Session, Depends(get_session)]):
    session.add(hero)
    session.commit()
    session.refresh(hero)
    return hero

@app.get("/heroes")
def list_heroes(session: Annotated[Session, Depends(get_session)], offset: int = 0, limit: int = 100):
    return session.exec(select(Hero).offset(offset).limit(limit)).all()
```

### Database Session Dependency with SQLAlchemy
```python
from sqlalchemy import create_engine
from sqlalchemy.orm import sessionmaker

engine = create_engine("postgresql://user:pass@localhost/app", pool_size=10, max_overflow=20)
SessionLocal = sessionmaker(bind=engine, autoflush=False, autocommit=False)

def get_db():
    db = SessionLocal()
    try:
        yield db
    finally:
        db.close()
```

### Async SQLAlchemy
```python
from sqlalchemy.ext.asyncio import AsyncSession, async_sessionmaker, create_async_engine
from sqlalchemy import select

engine = create_async_engine("postgresql+asyncpg://user:pass@localhost/app")
AsyncSessionLocal = async_sessionmaker(engine, expire_on_commit=False)

async def get_session():
    async with AsyncSessionLocal() as session:
        yield session

@app.get("/users/{user_id}")
async def read_user(user_id: int, session: Annotated[AsyncSession, Depends(get_session)]):
    result = await session.execute(select(UserModel).where(UserModel.id == user_id))
    user = result.scalar_one_or_none()
    if user is None:
        raise HTTPException(status_code=404, detail="User not found")
    return user
```

### CRUD Operations
```python
def get_user(db: Session, user_id: int):
    return db.get(UserModel, user_id)

def create_user(db: Session, data: UserCreate):
    obj = UserModel(username=data.username, hashed_password=hash_password(data.password))
    db.add(obj)
    db.commit()
    db.refresh(obj)
    return obj

def update_user(db: Session, obj: UserModel, data: UserUpdate):
    for key, value in data.model_dump(exclude_unset=True).items():
        setattr(obj, key, value)
    db.commit()
    db.refresh(obj)
    return obj

def delete_user(db: Session, obj: UserModel):
    db.delete(obj)
    db.commit()
```

### Pydantic and ORM Model Separation
Keep database models and API schemas apart. Use `from_attributes=True` to build schemas from ORM objects.

### Alembic Migrations
```bash
alembic init alembic
alembic revision --autogenerate -m "add users"
alembic upgrade head
alembic downgrade -1
```

### Connection Pooling
Configured on the engine through `pool_size`, `max_overflow`, `pool_timeout`, and `pool_pre_ping`.

### Transactions
```python
try:
    db.add(order)
    db.add(payment)
    db.commit()
except Exception:
    db.rollback()
    raise
```

### NoSQL Integration
- MongoDB (Motor, Beanie, ODMantic)
- Redis
- Elasticsearch

```python
from motor.motor_asyncio import AsyncIOMotorClient

mongo = AsyncIOMotorClient("mongodb://localhost:27017")
collection = mongo.appdb.users

@app.get("/users/{username}")
async def read_user(username: str):
    doc = await collection.find_one({"username": username}, {"_id": 0})
    if not doc:
        raise HTTPException(status_code=404, detail="User not found")
    return doc
```

### Other ORMs
- Tortoise ORM
- Peewee
- Databases library

## WebSockets

### WebSocket Endpoints
A persistent, bidirectional connection between client and server.

```python
from fastapi import WebSocket, WebSocketDisconnect

@app.websocket("/ws")
async def websocket_endpoint(websocket: WebSocket):
    await websocket.accept()
    try:
        while True:
            text = await websocket.receive_text()
            await websocket.send_text(f"Echo: {text}")
    except WebSocketDisconnect:
        print("client disconnected")
```

### Accepting and Closing Connections
`await websocket.accept()` completes the handshake; `await websocket.close(code=1000)` ends the connection.

### Sending and Receiving Messages
- Text: `receive_text`, `send_text`
- JSON: `receive_json`, `send_json`
- Bytes: `receive_bytes`, `send_bytes`

### Connection Managers
```python
class ConnectionManager:
    def __init__(self):
        self.active: list[WebSocket] = []

    async def connect(self, ws: WebSocket):
        await ws.accept()
        self.active.append(ws)

    def disconnect(self, ws: WebSocket):
        self.active.remove(ws)

    async def broadcast(self, message: str):
        for ws in self.active:
            await ws.send_text(message)

manager = ConnectionManager()

@app.websocket("/chat/{client_id}")
async def chat(websocket: WebSocket, client_id: int):
    await manager.connect(websocket)
    try:
        while True:
            data = await websocket.receive_text()
            await manager.broadcast(f"Client {client_id}: {data}")
    except WebSocketDisconnect:
        manager.disconnect(websocket)
        await manager.broadcast(f"Client {client_id} left")
```

### Broadcasting
Sending the same message to all connected clients, as in `broadcast` above. Multi-instance deployments need a shared channel such as Redis Pub/Sub.

### WebSocket Dependencies and Authentication
Dependencies work with WebSocket routes. Pass tokens through query parameters or cookies since browsers cannot set custom headers on WebSocket connections.

### WebSocketDisconnect Handling
Catch `WebSocketDisconnect` to clean up resources when a client leaves.

```bash
# Test with websocat
websocat ws://127.0.0.1:8000/ws
```

## Static Files and Templates

### StaticFiles Mounting
```python
from fastapi.staticfiles import StaticFiles
app.mount("/static", StaticFiles(directory="static"), name="static")
```

### Jinja2 Templates
```python
from fastapi.responses import HTMLResponse
from fastapi.templating import Jinja2Templates

templates = Jinja2Templates(directory="templates")

@app.get("/items/{item_id}", response_class=HTMLResponse)
async def read_item(request: Request, item_id: str):
    return templates.TemplateResponse(request=request, name="item.html", context={"id": item_id})
```

### Serving Frontend Applications
Mount a built single-page application directory with `StaticFiles(directory="dist", html=True)`.

## Documentation and OpenAPI

### Automatic Documentation
- Swagger UI at `/docs`
- ReDoc at `/redoc`
- OpenAPI schema at `/openapi.json`

```bash
curl -s http://127.0.0.1:8000/openapi.json | jq '.paths | keys'
```

### Metadata Customization
```python
tags_metadata = [
    {"name": "users", "description": "Operations with users"},
    {"name": "items", "description": "Manage items"},
]

app = FastAPI(
    title="My API",
    summary="Short summary",
    description="Longer **Markdown** description",
    version="1.0.0",
    terms_of_service="https://example.com/terms",
    contact={"name": "API Team", "email": "api@example.com"},
    license_info={"name": "MIT", "url": "https://opensource.org/licenses/MIT"},
    openapi_tags=tags_metadata,
)
```

### Docstring and Description Support (Markdown)
A function's docstring becomes the operation description in the docs and supports Markdown.

### Examples in Schemas
```python
from fastapi import Body

@app.post("/items")
async def create_item(
    item: Annotated[Item, Body(openapi_examples={
        "normal": {"summary": "Typical item", "value": {"name": "Pen", "price": 5.0}},
        "invalid": {"summary": "Invalid price", "value": {"name": "Pen", "price": "free"}},
    })],
): ...
```

### Customizing OpenAPI Schema
```python
from fastapi.openapi.utils import get_openapi

def custom_openapi():
    if app.openapi_schema:
        return app.openapi_schema
    schema = get_openapi(title="Custom", version="2.0.0", routes=app.routes)
    schema["info"]["x-logo"] = {"url": "https://example.com/logo.png"}
    app.openapi_schema = schema
    return schema

app.openapi = custom_openapi
```

### Disabling or Customizing Docs URLs
```python
app = FastAPI(docs_url=None, redoc_url=None, openapi_url=None)   # disable all (production)
app = FastAPI(docs_url="/documentation")                          # move Swagger UI
```

### Self-Hosting Docs Assets
Serve Swagger UI and ReDoc JavaScript and CSS files locally instead of from a CDN for offline or restricted networks.

### Callbacks and Webhooks Documentation
```python
@app.webhooks.post("new-subscription")
def new_subscription(body: Subscription):
    """Sent to subscribers when a new user subscribes."""
```

### Client Code Generation from OpenAPI
```bash
curl -o openapi.json http://127.0.0.1:8000/openapi.json
npx @openapitools/openapi-generator-cli generate -i openapi.json -g go -o ./go-client
npx @openapitools/openapi-generator-cli generate -i openapi.json -g python -o ./py-client
```

## Configuration and Settings

### Environment Variables
```bash
export DATABASE_URL="postgresql://user:pass@localhost/app"
export SECRET_KEY="$(openssl rand -hex 32)"
```

### `pydantic-settings`
Typed, validated configuration loaded from the environment.

### `.env` Files
```text
DATABASE_URL=postgresql://user:pass@localhost/app
SECRET_KEY=replace-me
DEBUG=false
```

Never commit `.env` files; add them to `.gitignore`.

### Settings Dependency
```python
from functools import lru_cache

@lru_cache
def get_settings() -> Settings:
    return Settings()

@app.get("/info")
async def info(settings: Annotated[Settings, Depends(get_settings)]):
    return {"app_name": settings.app_name}
```

### Environment-Specific Configuration
Load different files or variables for development, testing, staging, and production.

### Secrets Management
Use a secrets manager (AWS Secrets Manager, HashiCorp Vault, Azure Key Vault) instead of plain files in production.

## Testing in FastAPI

### TestClient
Synchronous test client built on httpx that calls the application directly without a running server.

```python
from fastapi.testclient import TestClient
from main import app

client = TestClient(app)

def test_read_root():
    response = client.get("/")
    assert response.status_code == 200
    assert response.json() == {"message": "Hello World"}

def test_create_item():
    response = client.post("/items", json={"name": "Pen", "price": 5.0})
    assert response.status_code == 201
    assert response.json()["name"] == "Pen"

def test_validation_error():
    response = client.post("/items", json={"name": "Pen"})
    assert response.status_code == 422
```

### Async Testing
```python
import pytest
from httpx import ASGITransport, AsyncClient
from main import app

@pytest.mark.anyio
async def test_root():
    async with AsyncClient(transport=ASGITransport(app=app), base_url="http://test") as ac:
        response = await ac.get("/")
    assert response.status_code == 200
```

### Pytest Fundamentals
- Fixtures: reusable setup
- Parametrization: one test, many inputs
- Markers: tagging and selecting tests

```python
import pytest

@pytest.fixture
def client():
    return TestClient(app)

@pytest.mark.parametrize("item_id,expected", [(1, 200), (999, 404)])
def test_get_item(client, item_id, expected):
    assert client.get(f"/items/{item_id}").status_code == expected
```

### Dependency Overrides in Tests
```python
def override_get_db():
    db = TestingSessionLocal()
    try:
        yield db
    finally:
        db.close()

app.dependency_overrides[get_db] = override_get_db
```

### Test Database Setup
Use an in-memory SQLite database or a dedicated test database, creating tables before tests and dropping them after.

### Testing Authentication
```python
def test_protected_route():
    token = client.post("/token", data={"username": "asha", "password": "secret"}).json()["access_token"]
    r = client.get("/users/me", headers={"Authorization": f"Bearer {token}"})
    assert r.status_code == 200

def test_protected_without_token():
    assert client.get("/users/me").status_code == 401
```

### Testing WebSockets
```python
def test_websocket():
    with client.websocket_connect("/ws") as ws:
        ws.send_text("hello")
        assert ws.receive_text() == "Echo: hello"
```

### Testing Background Tasks
Assert side effects after the call, such as a log file written or a mock called.

### Mocking External Services
```python
from unittest.mock import patch

def test_external_call():
    with patch("app.services.fetch_weather", return_value={"temp": 30}):
        r = client.get("/weather?city=Kolkata")
    assert r.json()["temp"] == 30
```

### Coverage Reporting
```bash
pytest --cov=app --cov-report=term-missing
```

## Logging and Monitoring

### Python Logging Module
```python
import logging

logging.basicConfig(level=logging.INFO, format="%(asctime)s %(levelname)s %(name)s %(message)s")
logger = logging.getLogger(__name__)

@app.get("/items/{item_id}")
async def read_item(item_id: int):
    logger.info("Reading item %s", item_id)
    return {"item_id": item_id}
```

### Structured Logging
Emit logs as JSON so aggregation tools can query fields (libraries: `structlog`, `python-json-logger`).

### Request and Response Logging
Implement through middleware, recording method, path, status, duration, and request ID.

### Uvicorn Logging Configuration
```bash
uvicorn main:app --log-level info --access-log
uvicorn main:app --log-config logging.yaml
```

### Metrics with Prometheus
```python
# pip install prometheus-fastapi-instrumentator
from prometheus_fastapi_instrumentator import Instrumentator

Instrumentator().instrument(app).expose(app)   # metrics at /metrics
```

### Tracing with OpenTelemetry
```bash
pip install opentelemetry-distro opentelemetry-exporter-otlp
opentelemetry-bootstrap -a install
opentelemetry-instrument --service_name my-api uvicorn main:app
```

### Error Tracking with Sentry
```python
import sentry_sdk
sentry_sdk.init(dsn="https://<key>@sentry.io/<project>", traces_sample_rate=0.1)
```

### Health Check Endpoints
```python
@app.get("/health/live")
async def live():
    return {"status": "ok"}

@app.get("/health/ready")
async def ready(db: Annotated[Session, Depends(get_db)]):
    db.execute(text("SELECT 1"))
    return {"status": "ready"}
```

## Performance Optimization

### Async Best Practices
Use async drivers, avoid blocking calls in `async def`, and offload CPU-heavy work to workers.

### Response Serialization Speed
- `ORJSONResponse` as default response class
- Pydantic v2 Rust core
- Returning models with `response_model` only when filtering is needed

```python
from fastapi.responses import ORJSONResponse
app = FastAPI(default_response_class=ORJSONResponse)
```

### Caching
- `fastapi-cache2`
- Redis
- In-memory caching with `functools.lru_cache`

```python
from fastapi_cache import FastAPICache
from fastapi_cache.backends.redis import RedisBackend
from fastapi_cache.decorator import cache

@app.get("/expensive")
@cache(expire=60)
async def expensive():
    return {"result": await compute()}
```

### Compression
`GZipMiddleware` or compression at the reverse proxy.

### Pagination
Always cap `limit` using `Query(le=100)`.

### Database Query Optimization
Add indexes, avoid N+1 queries with eager loading (`selectinload`, `joinedload`), and select only required columns.

### Worker Configuration
Match workers to CPU cores; keep each worker's connection pool small enough that total connections stay within database limits.

### Profiling
```bash
pip install pyinstrument py-spy
py-spy top --pid <PID>
```

### Rate Limiting
```python
# pip install slowapi
from slowapi import Limiter, _rate_limit_exceeded_handler
from slowapi.errors import RateLimitExceeded
from slowapi.util import get_remote_address

limiter = Limiter(key_func=get_remote_address)
app.state.limiter = limiter
app.add_exception_handler(RateLimitExceeded, _rate_limit_exceeded_handler)

@app.get("/limited")
@limiter.limit("5/minute")
async def limited(request: Request):
    return {"ok": True}
```

## Deployment

### Production Server Configuration
- Uvicorn alone for simple setups
- Gunicorn with Uvicorn workers for process supervision
- Process managers (systemd, Supervisor)

```ini
# /etc/systemd/system/my-api.service
[Unit]
Description=My FastAPI service
After=network.target

[Service]
User=www-data
WorkingDirectory=/srv/my-api
ExecStart=/srv/my-api/.venv/bin/gunicorn main:app -k uvicorn.workers.UvicornWorker -w 4 -b 127.0.0.1:8000
Restart=always

[Install]
WantedBy=multi-user.target
```

```bash
sudo systemctl daemon-reload
sudo systemctl enable --now my-api
sudo systemctl status my-api
```

### Docker
```dockerfile
FROM python:3.12-slim

WORKDIR /code
COPY requirements.txt .
RUN pip install --no-cache-dir --upgrade -r requirements.txt

COPY ./app ./app

EXPOSE 8000
CMD ["fastapi", "run", "app/main.py", "--port", "8000"]
```

```yaml
# docker-compose.yml
services:
  api:
    build: .
    ports: ["8000:8000"]
    env_file: .env
    depends_on: [db]
  db:
    image: postgres:16
    environment:
      POSTGRES_PASSWORD: example
    volumes: [pgdata:/var/lib/postgresql/data]
volumes:
  pgdata:
```

```bash
docker compose up --build -d
docker compose logs -f api
```

### Reverse Proxies
A reverse proxy handles TLS, compression, static files, and buffering in front of the application.

```nginx
server {
    listen 80;
    server_name api.example.com;

    location / {
        proxy_pass http://127.0.0.1:8000;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }
}
```

When serving under a path prefix, set `--root-path /api` (Uvicorn) or `FastAPI(root_path="/api")`. Use `--proxy-headers` so the app trusts forwarded headers.

### Kubernetes
Deploy containers with `Deployment`, expose with `Service`, and route external traffic with `Ingress` (see the Deployment example in the API Development section).

### Cloud Deployment
- AWS (EC2, ECS, Lambda, App Runner)
- Azure
- Google Cloud Run
- Heroku
- Railway
- Render
- Fly.io
- Vercel

### Serverless
Run the application as a function using an adapter such as Mangum on AWS Lambda.

```python
from mangum import Mangum
handler = Mangum(app)
```

### HTTPS and Certificates
```bash
sudo certbot --nginx -d api.example.com
```

### CI/CD Integration
Run tests, build the image, push to a registry, and deploy automatically on merge.

### Environment Variables in Production
Inject through the platform (container environment, Kubernetes Secrets) rather than files.

### Zero-Downtime Deployment
Rolling updates with readiness probes, or blue-green releases behind a load balancer.

## Advanced FastAPI Topics

### Lifespan State
Share objects created at startup through `app.state` or the lifespan's yielded state.

### Custom Route Classes
A custom `APIRoute` subclass can wrap every request and response in a route.

```python
from fastapi.routing import APIRoute
from typing import Callable

class TimedRoute(APIRoute):
    def get_route_handler(self) -> Callable:
        original = super().get_route_handler()
        async def handler(request: Request):
            response = await original(request)
            response.headers["X-Route"] = self.path
            return response
        return handler

router = APIRouter(route_class=TimedRoute)
```

### Custom OpenAPI Generation
Modify or replace the OpenAPI schema generator, as shown in the OpenAPI section.

### Sub-Application Mounting
Mount independent FastAPI apps, each with its own docs, under distinct prefixes.

### WSGI Application Mounting
```python
from fastapi.middleware.wsgi import WSGIMiddleware
from flask import Flask

flask_app = Flask(__name__)
app.mount("/flask", WSGIMiddleware(flask_app))
```

### GraphQL Integration
```python
import strawberry
from strawberry.fastapi import GraphQLRouter

@strawberry.type
class Query:
    @strawberry.field
    def hello(self) -> str:
        return "Hello"

app.include_router(GraphQLRouter(strawberry.Schema(Query)), prefix="/graphql")
```

### Event Handlers (Startup and Shutdown, legacy)
`@app.on_event("startup")` and `@app.on_event("shutdown")` are older, deprecated in favor of `lifespan`.

### Advanced Dependencies
Parameterized dependencies built from callable classes or closures.

```python
class RoleChecker:
    def __init__(self, allowed: list[str]):
        self.allowed = allowed

    def __call__(self, user: Annotated[User, Depends(get_current_user)]):
        if user.role not in self.allowed:
            raise HTTPException(status_code=403)

admin_only = RoleChecker(["admin"])

@app.get("/admin", dependencies=[Depends(admin_only)])
async def admin(): ...
```

### Generic Response Wrappers
```python
class Envelope(BaseModel, Generic[T]):
    data: T
    message: str = "ok"

@app.get("/users/{id}", response_model=Envelope[UserRead])
async def get_user(id: int): ...
```

### Streaming Request Bodies
```python
@app.post("/ingest")
async def ingest(request: Request):
    total = 0
    async for chunk in request.stream():
        total += len(chunk)
    return {"bytes": total}
```

### Custom JSON Encoders
Control serialization of special types through Pydantic serializers.

```python
from pydantic import field_serializer
from datetime import datetime

class Event(BaseModel):
    at: datetime

    @field_serializer("at")
    def ser_at(self, v: datetime) -> str:
        return v.strftime("%Y-%m-%d %H:%M")
```

### Webhooks Definition
Documented through `app.webhooks`, describing requests the application sends to external URLs.

### Callbacks
Document calls the API makes to a client-provided URL using an `APIRouter` assigned to `callbacks`.

### Behind a Proxy
Configure `root_path` and forwarded headers so generated URLs and docs work correctly.

### Settings and Environment Management at Scale
Layered settings classes, per-environment overrides, and secret stores.

### Plugin and Extension Libraries
- `fastapi-users`: user management and authentication
- `fastapi-pagination`: pagination helpers
- `fastapi-limiter`: rate limiting
- `fastapi-cache`: response caching
- `sqladmin`: admin interface
- `fastapi-utils`: utilities
- `fastapi-mail`: email sending

## Consuming FastAPI from Other Languages

### Go Client for a FastAPI Service
```go
package main

import (
	"bytes"
	"encoding/json"
	"fmt"
	"net/http"
	"net/url"
	"strings"
	"time"
)

type Token struct {
	AccessToken string `json:"access_token"`
	TokenType   string `json:"token_type"`
}

type User struct {
	Username string `json:"username"`
	Role     string `json:"role"`
}

func main() {
	client := &http.Client{Timeout: 5 * time.Second}
	base := "http://127.0.0.1:8000"

	// Login using form-encoded data (OAuth2PasswordRequestForm)
	form := url.Values{"username": {"asha"}, "password": {"secret"}}
	resp, err := client.Post(base+"/token", "application/x-www-form-urlencoded", strings.NewReader(form.Encode()))
	if err != nil {
		panic(err)
	}
	var tok Token
	json.NewDecoder(resp.Body).Decode(&tok)
	resp.Body.Close()

	// Call protected endpoint
	req, _ := http.NewRequest(http.MethodGet, base+"/users/me", nil)
	req.Header.Set("Authorization", "Bearer "+tok.AccessToken)
	resp, err = client.Do(req)
	if err != nil {
		panic(err)
	}
	defer resp.Body.Close()

	var me User
	json.NewDecoder(resp.Body).Decode(&me)
	fmt.Printf("%+v\n", me)

	// POST JSON
	body, _ := json.Marshal(map[string]any{"name": "Pen", "price": 5.0})
	resp2, _ := client.Post(base+"/items", "application/json", bytes.NewReader(body))
	fmt.Println(resp2.StatusCode)
}
```

### Bash Workflow for a FastAPI Service
```bash
# Start
uvicorn main:app --reload &

# Health check
curl -fsS http://127.0.0.1:8000/health/live

# Authenticate
TOKEN=$(curl -s -X POST http://127.0.0.1:8000/token -d "username=asha&password=secret" | jq -r .access_token)

# Create, list, delete
curl -X POST http://127.0.0.1:8000/items -H "Authorization: Bearer $TOKEN" \
  -H "Content-Type: application/json" -d '{"name":"Pen","price":5}'
curl -H "Authorization: Bearer $TOKEN" http://127.0.0.1:8000/items | jq
curl -X DELETE -H "Authorization: Bearer $TOKEN" http://127.0.0.1:8000/items/1 -i

# Run tests
pytest -q
```

## FastAPI Best Practices

### Project Organization
Split code into routers, schemas, models, services, and dependencies.

### Separation of Schemas, Models and Services
Pydantic schemas for the API, ORM models for storage, and services for business logic.

### Type Safety
Annotate every parameter and return value; run `mypy` or `pyright`.

### Dependency Usage
Use dependencies for authentication, database sessions, and settings so tests can override them.

### Async-Appropriate Code
Match `async def` with async libraries and `def` with blocking libraries.

### Consistent Error Handling
Register global handlers that return a single error format.

### Security Hardening
Hash passwords, restrict CORS, limit request sizes, disable docs in production if needed, and keep dependencies updated.

### Testing Discipline
Test routes, validation failures, authentication, and edge cases; override dependencies instead of patching internals.

### Documentation Quality
Add summaries, descriptions, tags, and examples to every operation.

### Versioning Strategy
Mount routers under `/api/v1`, `/api/v2` and keep older versions until deprecation ends.

### Configuration via Environment
Keep configuration outside code, validated by `BaseSettings`.

## FastAPI Ecosystem

### Core Libraries
- Starlette
- Pydantic
- Uvicorn
- SQLModel
- Typer
- HTTPX

### Companion Tools
- Alembic
- SQLAlchemy
- Celery
- Redis
- pytest

### Community Resources
- Official documentation
- GitHub repository
- Discord and discussion forums
- Awesome FastAPI lists

---

# Learning Path Summary

## Foundation Stage
### HTTP and Web Basics
### Python and Type Hints
### JSON and Data Formats

## Core Stage
### REST Principles
### FastAPI Routing and Validation
### Pydantic Models
### Dependency Injection

## Intermediate Stage
### Database Integration
### Authentication and Authorization
### Testing
### Error Handling

## Advanced Stage
### Async and Concurrency
### Background Processing
### Performance Optimization
### Observability

## Production Stage
### Containerization
### CI/CD
### Deployment
### Monitoring and Maintenance