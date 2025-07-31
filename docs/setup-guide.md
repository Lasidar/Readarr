# Readarr Setup Guide

## Prerequisites

Before setting up Readarr for development, ensure you have the following installed:

### Required Software

1. **Git** - Version control system
2. **.NET SDK 6.0** - Backend development (6.0.427 or later)
3. **Node.js 20.x** - Frontend development
4. **Yarn 1.22.x** - Package manager for frontend
5. **Visual Studio Code** or **Visual Studio 2022** (recommended IDEs)

### Optional Tools

- **Docker** - For containerized development
- **PostgreSQL** - Alternative to SQLite for testing
- **Volta** - Node.js version management

## Development Environment Setup

### 1. Clone the Repository

```bash
git clone https://github.com/Readarr/Readarr.git
cd Readarr
```

### 2. Install Frontend Dependencies

```bash
# Navigate to the root directory (where package.json is located)
yarn install
```

### 3. Install Backend Dependencies

```bash
# Restore NuGet packages
dotnet restore src/Readarr.sln
```

### 4. Configure Development Environment

Create a development configuration file if needed:

```bash
# Linux/macOS
mkdir -p ~/.config/Readarr
touch ~/.config/Readarr/config.xml

# Windows
mkdir %APPDATA%\Readarr
type nul > %APPDATA%\Readarr\config.xml
```

## Running the Application

### Option 1: Full Stack Development

```bash
# Build and run everything
./build.sh --dev

# Or manually:
# Terminal 1 - Backend
cd src
dotnet run --project NzbDrone.Host

# Terminal 2 - Frontend
yarn start
```

### Option 2: Backend Only

```bash
cd src
dotnet run --project NzbDrone.Host

# With file watching
dotnet watch run --project NzbDrone.Host
```

### Option 3: Frontend Only

```bash
# Development server with hot reload
yarn start

# Or watch mode
yarn watch
```

## Accessing the Application

Once running, access Readarr at:
- **URL**: http://localhost:8787
- **API**: http://localhost:8787/api/v1/
- **API Docs**: http://localhost:8787/docs/v1/openapi.json (debug mode only)

## Configuration

### 1. Initial Setup Wizard

On first launch, you'll be guided through:
1. **Authentication**: Set up username/password or configure authentication method
2. **API Key**: Generated automatically, found in Settings → General
3. **Root Folders**: Configure where books will be stored
4. **Download Clients**: Set up SABnzbd, NZBGet, or torrent clients
5. **Indexers**: Configure Usenet or torrent indexers

### 2. Development Configuration

#### Enable Debug Mode

Edit `config.xml`:
```xml
<Config>
  <LogLevel>Debug</LogLevel>
  <EnableDebug>true</EnableDebug>
  <LaunchBrowser>false</LaunchBrowser>
</Config>
```

#### Configure Ports

```xml
<Config>
  <Port>8787</Port>
  <SslPort>9898</SslPort>
  <UrlBase></UrlBase>
</Config>
```

### 3. Database Configuration

By default, Readarr uses SQLite. Database files are stored in:
- **Linux**: `~/.config/Readarr/`
- **Windows**: `%APPDATA%\Readarr\`
- **macOS**: `~/.config/Readarr/`

Files created:
- `readarr.db` - Main database
- `logs.db` - Log database
- `cache.db` - Cache database

## IDE Setup

### Visual Studio Code

1. Install recommended extensions:
   ```json
   {
     "recommendations": [
       "ms-dotnettools.csharp",
       "dbaeumer.vscode-eslint",
       "esbenp.prettier-vscode",
       "stylelint.vscode-stylelint",
       "ms-vscode.vscode-typescript-tslint-plugin"
     ]
   }
   ```

2. Configure debugging in `.vscode/launch.json`:
   ```json
   {
     "version": "0.2.0",
     "configurations": [
       {
         "name": "Backend",
         "type": "coreclr",
         "request": "launch",
         "preLaunchTask": "build",
         "program": "${workspaceFolder}/src/NzbDrone.Host/bin/Debug/net6.0/Readarr.dll",
         "args": [],
         "cwd": "${workspaceFolder}",
         "console": "internalConsole"
       }
     ]
   }
   ```

### Visual Studio 2022

1. Open `src/Readarr.sln`
2. Set `NzbDrone.Host` as startup project
3. Configure debugging settings in project properties
4. Press F5 to run with debugging

## Testing

### Running Tests

```bash
# All tests
dotnet test src/Readarr.sln

# Specific test project
dotnet test src/NzbDrone.Core.Test/Readarr.Core.Test.csproj

# With coverage
dotnet test src/Readarr.sln --collect:"XPlat Code Coverage"
```

### Frontend Tests

```bash
# Linting
yarn lint

# Fix linting issues
yarn lint-fix

# Style linting
yarn stylelint-linux  # or stylelint-windows
```

## Common Development Tasks

### 1. Adding a New API Endpoint

1. Create controller in `src/Readarr.Api.V1/`
2. Inherit from `RestController<TResource>` or `RestControllerWithSignalR<TResource, TModel>`
3. Add `[V1ApiController]` attribute
4. Implement CRUD operations as needed

Example:
```csharp
[V1ApiController]
public class MyController : RestController<MyResource>
{
    [HttpGet]
    public List<MyResource> GetAll()
    {
        // Implementation
    }
}
```

### 2. Adding a Frontend Component

1. Create component in appropriate feature folder
2. Add TypeScript types if needed
3. Use CSS modules for styling
4. Connect to Redux store if state management needed

Example:
```typescript
// MyComponent.tsx
import React from 'react';
import styles from './MyComponent.css';

interface MyComponentProps {
  title: string;
}

function MyComponent({ title }: MyComponentProps) {
  return <div className={styles.container}>{title}</div>;
}

export default MyComponent;
```

### 3. Database Migrations

1. Create migration in `src/NzbDrone.Core/Datastore/Migration/`
2. Implement `NzbDroneMigrationBase`
3. Use FluentMigrator attributes

Example:
```csharp
[Migration(123)]
public class AddMyTable : NzbDroneMigrationBase
{
    protected override void MainDbUpgrade()
    {
        Create.Table("MyTable")
            .WithColumn("Id").AsInt32().PrimaryKey().Identity()
            .WithColumn("Name").AsString();
    }
}
```

## Troubleshooting

### Common Issues

#### 1. Port Already in Use
```bash
# Find process using port 8787
lsof -i :8787  # Linux/macOS
netstat -ano | findstr :8787  # Windows

# Change port in config.xml or use --port argument
dotnet run --project src/NzbDrone.Host -- --port=8788
```

#### 2. Database Locked
- Stop all Readarr processes
- Delete `*.db-journal` files
- Restart application

#### 3. Frontend Build Errors
```bash
# Clear caches and rebuild
yarn clean
rm -rf node_modules
yarn install
yarn build
```

#### 4. Authentication Issues
- Delete `config.xml` to reset authentication
- Or edit `<AuthenticationMethod>` in config.xml

### Debug Logging

Enable verbose logging:
```xml
<Config>
  <LogLevel>Trace</LogLevel>
</Config>
```

View logs:
- **Console**: When running in terminal
- **File**: In logs folder (`readarr.trace.txt`)
- **UI**: System → Logs

## Production Deployment

### 1. Build for Production

```bash
# Full build
./build.sh

# Platform-specific
./build.sh --runtime linux-x64
```

### 2. Deploy Files

Copy the contents of `_output/` to your production server.

### 3. Configure as Service

See the Build & Deployment documentation for service configuration examples.

### 4. Reverse Proxy Setup

#### Nginx Example:
```nginx
location /readarr {
    proxy_pass http://localhost:8787;
    proxy_set_header Host $host;
    proxy_set_header X-Real-IP $remote_addr;
    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    proxy_set_header X-Forwarded-Proto $scheme;
    proxy_http_version 1.1;
    proxy_set_header Upgrade $http_upgrade;
    proxy_set_header Connection "upgrade";
}
```

## Additional Resources

- **Wiki**: https://wiki.servarr.com/readarr
- **Discord**: https://readarr.com/discord
- **GitHub Issues**: https://github.com/Readarr/Readarr/issues
- **API Documentation**: Available at `/docs/v1/openapi.json` when running

## Contributing

1. Fork the repository
2. Create a feature branch
3. Make your changes
4. Run tests
5. Submit a pull request

See `CONTRIBUTING.md` for detailed guidelines.