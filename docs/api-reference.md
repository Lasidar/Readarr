# Readarr API Reference

## Overview

Readarr provides a comprehensive RESTful API for managing your ebook and audiobook library. The API is versioned and currently at v1. All API endpoints are prefixed with `/api/v1/`.

## API Documentation

- **OpenAPI Specification**: Available at `/docs/v1/openapi.json` when running in debug mode
- **Base URL**: `http://localhost:8787/api/v1/` (default)
- **Format**: JSON request/response bodies
- **HTTP Methods**: GET, POST, PUT, DELETE

## Authentication

Readarr supports multiple authentication methods:

### 1. API Key Authentication

The preferred method for API access. You can find your API key in Settings → General → Security.

**Header Authentication:**
```
X-Api-Key: your-api-key-here
```

**Query Parameter Authentication:**
```
GET /api/v1/author?apikey=your-api-key-here
```

### 2. Basic Authentication

If enabled in settings:
```
Authorization: Basic base64(username:password)
```

### 3. Forms Authentication

Used for the web UI, creates a session cookie:
```
POST /login
Content-Type: multipart/form-data

username=your-username&password=your-password&rememberMe=true
```

## Core API Endpoints

### Author Management

#### Get All Authors
```
GET /api/v1/author
```
Returns a list of all authors in your library.

**Response:**
```json
[
  {
    "id": 1,
    "authorName": "Brandon Sanderson",
    "foreignAuthorId": "38550",
    "titleSlug": "brandon-sanderson",
    "status": "continuing",
    "overview": "Author overview...",
    "images": [...],
    "path": "/books/Brandon Sanderson",
    "qualityProfileId": 1,
    "metadataProfileId": 1,
    "monitored": true,
    "tags": []
  }
]
```

#### Get Author by ID
```
GET /api/v1/author/{id}
```

#### Add New Author
```
POST /api/v1/author
Content-Type: application/json

{
  "authorName": "Author Name",
  "foreignAuthorId": "goodreads-id",
  "qualityProfileId": 1,
  "metadataProfileId": 1,
  "path": "/books/Author Name",
  "monitored": true,
  "addOptions": {
    "searchForMissingBooks": true
  }
}
```

#### Update Author
```
PUT /api/v1/author/{id}
Content-Type: application/json
```

#### Delete Author
```
DELETE /api/v1/author/{id}
```

#### Author Lookup
```
GET /api/v1/author/lookup?term=search-term
```
Search for authors by name.

### Book Management

#### Get All Books
```
GET /api/v1/book
```

#### Get Book by ID
```
GET /api/v1/book/{id}
```

#### Update Book
```
PUT /api/v1/book/{id}
```

#### Monitor/Unmonitor Books
```
PUT /api/v1/book/monitor
Content-Type: application/json

{
  "bookIds": [1, 2, 3],
  "monitored": true
}
```

#### Book Lookup
```
GET /api/v1/book/lookup?term=search-term
```

### Calendar

#### Get Calendar Events
```
GET /api/v1/calendar?start=2023-01-01&end=2023-12-31
```
Returns books with release dates in the specified range.

**Query Parameters:**
- `start`: Start date (ISO 8601)
- `end`: End date (ISO 8601)
- `unmonitored`: Include unmonitored books (default: false)

### Queue Management

#### Get Download Queue
```
GET /api/v1/queue
```

**Query Parameters:**
- `page`: Page number (default: 1)
- `pageSize`: Items per page (default: 10)
- `sortKey`: Sort field
- `sortDirection`: asc/desc
- `includeUnknownAuthorItems`: Include items without matched author

#### Get Queue Details
```
GET /api/v1/queue/details
```

#### Remove from Queue
```
DELETE /api/v1/queue/{id}
```

#### Remove Multiple Items
```
DELETE /api/v1/queue/bulk
Content-Type: application/json

{
  "ids": [1, 2, 3]
}
```

### History

#### Get History
```
GET /api/v1/history
```

**Query Parameters:**
- `page`: Page number
- `pageSize`: Items per page
- `sortKey`: Sort field (default: date)
- `sortDirection`: asc/desc
- `eventType`: Filter by event type

#### Get History Since Date
```
GET /api/v1/history/since?date=2023-01-01T00:00:00Z
```

#### Get Author History
```
GET /api/v1/history/author?authorId=1
```

### Wanted Books

#### Get Missing Books
```
GET /api/v1/wanted/missing
```

**Query Parameters:**
- `page`: Page number
- `pageSize`: Items per page
- `sortKey`: Sort field
- `monitored`: Filter by monitored status

#### Get Cutoff Unmet
```
GET /api/v1/wanted/cutoff
```
Returns books that haven't met the quality cutoff.

### System

#### Get System Status
```
GET /api/v1/system/status
```

**Response:**
```json
{
  "version": "0.4.19.1",
  "buildTime": "2023-01-01T00:00:00Z",
  "isDebug": false,
  "isProduction": true,
  "isAdmin": false,
  "isUserInteractive": true,
  "startupPath": "/app",
  "appData": "/config",
  "osName": "Linux",
  "osVersion": "5.15.0",
  "isMonoRuntime": false,
  "isMono": false,
  "isLinux": true,
  "isOsx": false,
  "isWindows": false,
  "mode": "console",
  "branch": "develop",
  "authentication": "forms",
  "sqliteVersion": "3.39.2",
  "urlBase": "",
  "runtimeVersion": "6.0.11",
  "runtimeName": ".NET"
}
```

#### Get Disk Space
```
GET /api/v1/diskspace
```

#### Get Health Check
```
GET /api/v1/health
```

#### Get Logs
```
GET /api/v1/log
```

**Query Parameters:**
- `page`: Page number
- `pageSize`: Items per page
- `level`: Log level filter (trace/debug/info/warn/error/fatal)

### Download Clients

#### Get All Download Clients
```
GET /api/v1/downloadclient
```

#### Test Download Client
```
POST /api/v1/downloadclient/test
Content-Type: application/json

{
  "name": "SABnzbd",
  "enable": true,
  "protocol": "usenet",
  "priority": 1,
  "fields": [...]
}
```

### Indexers

#### Get All Indexers
```
GET /api/v1/indexer
```

#### Test Indexer
```
POST /api/v1/indexer/test
Content-Type: application/json
```

### Notifications

#### Get All Notifications
```
GET /api/v1/notification
```

#### Test Notification
```
POST /api/v1/notification/test
Content-Type: application/json
```

### Profiles

#### Get Quality Profiles
```
GET /api/v1/qualityprofile
```

#### Get Metadata Profiles
```
GET /api/v1/metadataprofile
```

### Tags

#### Get All Tags
```
GET /api/v1/tag
```

#### Create Tag
```
POST /api/v1/tag
Content-Type: application/json

{
  "label": "audiobook"
}
```

### Commands

#### Execute Command
```
POST /api/v1/command
Content-Type: application/json

{
  "name": "RefreshAuthor",
  "authorId": 1
}
```

**Available Commands:**
- `ApplicationUpdate`
- `AuthorSearch`
- `BookSearch`
- `RefreshAuthor`
- `RefreshBook`
- `RenameAuthor`
- `RenameFiles`
- `RescanFolders`
- `RssSync`
- `Backup`
- `MissingBookSearch`

#### Get Command Status
```
GET /api/v1/command/{id}
```

## Error Handling

The API uses standard HTTP status codes:

- **200 OK**: Success
- **201 Created**: Resource created
- **204 No Content**: Success with no response body
- **400 Bad Request**: Invalid request
- **401 Unauthorized**: Authentication required
- **403 Forbidden**: Access denied
- **404 Not Found**: Resource not found
- **422 Unprocessable Entity**: Validation error
- **500 Internal Server Error**: Server error

**Error Response Format:**
```json
{
  "message": "Validation failed",
  "errors": [
    {
      "propertyName": "Path",
      "errorMessage": "Path does not exist"
    }
  ]
}
```

## Rate Limiting

Currently, Readarr does not implement rate limiting on the API. However, it's recommended to:
- Implement reasonable delays between requests
- Use bulk endpoints where available
- Cache responses when appropriate

## Pagination

List endpoints support pagination through query parameters:

- `page`: Page number (1-based)
- `pageSize`: Items per page
- `sortKey`: Field to sort by
- `sortDirection`: Sort direction (asc/desc)

**Paginated Response Headers:**
```
X-Page: 1
X-Page-Size: 10
X-Total-Records: 100
X-Sort-Key: authorName
X-Sort-Direction: asc
```

## WebSocket / SignalR

For real-time updates, connect to the SignalR hub:

**Endpoint:** `/signalr/messages`

**Authentication:** Required (use API key as query parameter)

**Events:**
- Book updates
- Download progress
- System messages
- Health check updates

## Best Practices

1. **Always use HTTPS** in production environments
2. **Store API keys securely** and rotate them periodically
3. **Handle errors gracefully** and implement retry logic
4. **Use appropriate HTTP methods** (GET for read, POST for create, PUT for update, DELETE for remove)
5. **Include proper Content-Type headers** for requests with bodies
6. **Implement caching** for frequently accessed data
7. **Use bulk operations** when modifying multiple resources
8. **Monitor API usage** to identify potential issues

## API Versioning

The current API version is v1. Future versions will be available at different URL paths (e.g., `/api/v2/`). 

Version compatibility:
- Minor version updates maintain backward compatibility
- Major version updates may include breaking changes
- Deprecated endpoints will be marked in responses before removal