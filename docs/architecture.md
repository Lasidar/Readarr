# Readarr Architecture Documentation

## Overview

Readarr is a Single Page Application (SPA) designed for ebook and audiobook collection management for Usenet and BitTorrent users. The application follows a modern client-server architecture with a React-based frontend and a .NET Core backend.

## High-Level Architecture

```
┌─────────────────────────────────────────────────────────────────┐
│                          Client Browser                          │
│  ┌─────────────────────────────────────────────────────────┐   │
│  │                    React SPA (Frontend)                  │   │
│  │  ├─ Redux State Management                              │   │
│  │  ├─ React Router                                        │   │
│  │  └─ SignalR Client (Real-time updates)                 │   │
│  └─────────────────────────────────────────────────────────┘   │
└─────────────────────────────────────────────────────────────────┘
                                │
                                │ HTTP/WebSocket
                                ▼
┌─────────────────────────────────────────────────────────────────┐
│                      .NET Core Backend                           │
│  ┌─────────────────────────────────────────────────────────┐   │
│  │                    ASP.NET Core Host                     │   │
│  │  ├─ REST API (v1)                                      │   │
│  │  ├─ SignalR Hub (Real-time messaging)                  │   │
│  │  └─ Authentication & Authorization                      │   │
│  └─────────────────────────────────────────────────────────┘   │
│  ┌─────────────────────────────────────────────────────────┐   │
│  │                    Core Business Logic                   │   │
│  │  ├─ Book Management                                     │   │
│  │  ├─ Download Clients                                    │   │
│  │  ├─ Indexers                                           │   │
│  │  └─ Media Management                                    │   │
│  └─────────────────────────────────────────────────────────┘   │
│  ┌─────────────────────────────────────────────────────────┐   │
│  │                    Data Layer                           │   │
│  │  ├─ SQLite Database                                    │   │
│  │  └─ File System Storage                                │   │
│  └─────────────────────────────────────────────────────────┘   │
└─────────────────────────────────────────────────────────────────┘
                                │
                                │ External APIs
                                ▼
┌─────────────────────────────────────────────────────────────────┐
│                    External Services                             │
│  ├─ Metadata Providers (Book information)                       │
│  ├─ Download Clients (SABnzbd, NZBGet, qBittorrent, etc.)     │
│  ├─ Indexers (Usenet/Torrent sources)                          │
│  └─ Calibre Content Server (Optional integration)               │
└─────────────────────────────────────────────────────────────────┘
```

## Frontend Architecture

### Technology Stack
- **Framework**: React 17.0.2
- **State Management**: Redux with Redux Thunk
- **Routing**: React Router v5
- **Build Tool**: Webpack 5
- **Language**: TypeScript/JavaScript
- **Styling**: CSS Modules with PostCSS
- **Real-time Communication**: SignalR Client

### Key Frontend Components

#### 1. Application Entry Points
- `frontend/src/index.ts` - Main entry point
- `frontend/src/bootstrap.tsx` - React bootstrap
- `frontend/src/App/App.js` - Root application component

#### 2. State Management (Redux)
The application uses Redux for centralized state management:

```
frontend/src/Store/
├── Actions/         # Redux action creators
├── Middleware/      # Custom Redux middleware
├── Migrators/       # State migration utilities
├── Selectors/       # Reselect selectors for computed state
└── createAppStore.js # Store configuration
```

#### 3. Feature Modules
The frontend is organized into feature-based modules:

```
frontend/src/
├── Activity/        # Activity monitoring
├── AddAuthor/       # Author addition workflow
├── Author/          # Author management
├── Book/            # Book management
├── Bookshelf/       # Bookshelf views
├── Calendar/        # Release calendar
├── Settings/        # Application settings
├── System/          # System information
└── Wanted/          # Wanted books management
```

#### 4. Shared Components
Reusable UI components and utilities:

```
frontend/src/
├── Components/      # Shared UI components
├── Helpers/         # Utility functions
├── Styles/          # Global styles
└── Utilities/       # Common utilities
```

## Backend Architecture

### Technology Stack
- **Framework**: .NET 6.0 / ASP.NET Core
- **Language**: C#
- **Database**: SQLite (via Dapper ORM)
- **API**: RESTful with OpenAPI/Swagger documentation
- **Real-time**: SignalR for WebSocket communication
- **DI Container**: DryIoc

### Core Projects Structure

```
src/
├── NzbDrone.Core/           # Core business logic
├── NzbDrone.Host/           # ASP.NET Core hosting
├── NzbDrone.Common/         # Common utilities
├── Readarr.Api.V1/          # API v1 implementation
├── Readarr.Http/            # HTTP infrastructure
└── NzbDrone.SignalR/        # SignalR implementation
```

### Key Backend Components

#### 1. API Layer (`Readarr.Api.V1`)
- RESTful API endpoints organized by feature
- OpenAPI specification available at `/docs/v1/openapi.json`
- Versioned API with `V1ApiController` attribute

#### 2. Core Domain (`NzbDrone.Core`)
Key domain areas include:

```
NzbDrone.Core/
├── Authentication/      # User authentication
├── Books/              # Book and author management
├── Download/           # Download client integration
├── Indexers/           # Indexer integration
├── MediaFiles/         # Media file management
├── Metadata/           # Metadata providers
├── Notifications/      # Notification system
└── Profiles/           # Quality and metadata profiles
```

#### 3. Data Access Layer
- **Main Database**: Application data (books, authors, settings)
- **Log Database**: Application logs
- **Cache Database**: Temporary data and caching
- **ORM**: Custom lightweight ORM based on Dapper

## Communication Patterns

### 1. REST API Communication
- Standard HTTP methods (GET, POST, PUT, DELETE)
- JSON request/response format
- API versioning through URL path (`/api/v1/`)

### 2. Real-time Updates (SignalR)
- WebSocket connection for real-time updates
- Hub endpoint: `/signalr/messages`
- Used for:
  - Download progress updates
  - System status changes
  - Background task notifications

### 3. Authentication & Authorization
- **Methods Supported**:
  - API Key (header: `X-Api-Key` or query parameter)
  - Basic Authentication
  - Forms Authentication (for UI)
- **Authorization Policies**:
  - API endpoints require authentication
  - SignalR connections require separate authentication

## External Integrations

### 1. Download Clients
Supports multiple download clients through a plugin architecture:
- SABnzbd
- NZBGet
- qBittorrent
- Deluge
- rTorrent
- Transmission
- uTorrent

### 2. Indexers
Integrates with various indexer types:
- Newznab-compatible indexers
- Torznab-compatible indexers
- Custom indexer implementations

### 3. Metadata Providers
Book metadata sourcing from:
- Goodreads (legacy)
- Open Library (planned transition)
- Custom metadata providers

### 4. Calibre Integration
Optional integration with Calibre Content Server for:
- Library synchronization
- Format conversion
- Metadata management

## Data Flow

### 1. Book Search and Addition Flow
```
User Search → API → MetadataProvider → Search Results
    ↓
User Selection → API → Database Storage
    ↓
Monitoring Enabled → Indexer Search → Download Client
    ↓
Download Complete → Import Service → Media Management
```

### 2. Automated Monitoring Flow
```
Scheduled Task → RSS Sync → Indexer Results
    ↓
Decision Engine → Quality/Profile Check
    ↓
Download Decision → Download Client → Import
```

## Security Considerations

### 1. Authentication
- API key stored in configuration
- Password hashing for user accounts
- Session management for web UI

### 2. Network Security
- HTTPS support with certificate configuration
- CORS policies for API access
- Firewall integration on Windows

### 3. Data Protection
- ASP.NET Core Data Protection API for sensitive data
- Configuration file encryption options

## Scalability and Performance

### 1. Caching Strategy
- In-memory caching for frequently accessed data
- Cache database for persistent caching
- HTTP caching headers for static resources

### 2. Background Tasks
- Scheduled tasks for maintenance
- Queue-based processing for imports
- Parallel processing for file operations

### 3. Database Optimization
- Indexed queries for performance
- Migration system for schema updates
- Separate databases for different concerns

## Development Patterns

### 1. Dependency Injection
- DryIoc container for dependency resolution
- Constructor injection pattern
- Scoped and singleton lifetimes

### 2. Event-Driven Architecture
- Event aggregator pattern for loose coupling
- Domain events for business logic
- SignalR for client notifications

### 3. Repository Pattern
- Abstraction over data access
- Unit of work pattern for transactions
- Query objects for complex queries

## Monitoring and Diagnostics

### 1. Logging
- NLog integration
- Structured logging
- Multiple log targets (file, database, console)

### 2. Health Checks
- System health monitoring
- External service connectivity checks
- Disk space and permission checks

### 3. Metrics
- Download statistics
- Import success/failure rates
- System resource usage

## Future Architecture Considerations

1. **Microservices Migration**: Potential to split monolithic core into services
2. **Container Optimization**: Better Docker support and orchestration
3. **API Gateway**: Centralized API management for multiple services
4. **Event Sourcing**: For better audit trails and system recovery
5. **GraphQL Support**: Alternative to REST for flexible queries