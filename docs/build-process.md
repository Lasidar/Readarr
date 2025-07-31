# Readarr Build & Deployment Process

## Overview

Readarr uses a multi-stage build process that compiles both the .NET backend and React frontend into distributable packages for various platforms. The build system supports Windows, Linux, macOS, and FreeBSD targets.

## Build Requirements

### Development Environment

- **.NET SDK**: 6.0.427 or later
- **Node.js**: 20.x (managed via Volta)
- **Yarn**: 1.22.19 (package manager)
- **Git**: For version control
- **OS**: Windows, Linux, or macOS

### Build Tools

- **Backend**: MSBuild / dotnet CLI
- **Frontend**: Webpack 5
- **CI/CD**: Azure Pipelines
- **Package Management**: NuGet (backend), Yarn (frontend)

## Build Pipeline

### 1. Frontend Build Process

The frontend uses Webpack for bundling:

```bash
# Install dependencies
yarn install

# Development build with watch mode
yarn start

# Production build
yarn build

# Linting
yarn lint
yarn lint-fix
```

#### Webpack Configuration

Located at `frontend/build/webpack.config.js`:

- **Entry Point**: `frontend/src/index.ts`
- **Output**: `_output/UI/`
- **Features**:
  - TypeScript compilation via Babel
  - CSS Modules with PostCSS
  - Source maps generation
  - Live reload in development
  - Asset optimization in production

#### Build Steps:

1. **TypeScript Compilation**: Babel with TypeScript preset
2. **CSS Processing**: PostCSS with CSS Modules
3. **Asset Handling**: File and URL loaders for fonts/images
4. **Bundle Generation**: Separate chunks for vendor libraries
5. **HTML Generation**: HtmlWebpackPlugin with template

### 2. Backend Build Process

The backend uses MSBuild/.NET CLI:

```bash
# Restore dependencies
dotnet restore src/Readarr.sln

# Build solution
dotnet build src/Readarr.sln -c Release

# Run tests
dotnet test src/Readarr.sln

# Publish for specific runtime
dotnet publish src/Readarr.sln -c Release -r linux-x64
```

#### Project Structure:

- **Solution File**: `src/Readarr.sln`
- **Main Projects**:
  - `NzbDrone.Host` - ASP.NET Core host
  - `NzbDrone.Core` - Business logic
  - `Readarr.Api.V1` - API implementation
  - `NzbDrone.Common` - Shared utilities

### 3. Complete Build Script

The `build.sh` script orchestrates the entire build:

```bash
# Full build
./build.sh

# Platform-specific builds
./build.sh --runtime linux-x64
./build.sh --runtime win-x64
./build.sh --runtime osx-x64
```

#### Build Script Functions:

1. **UpdateVersionNumber**: Sets version in assemblies
2. **Build**: Compiles backend for all platforms
3. **YarnInstall**: Installs frontend dependencies
4. **RunWebpack**: Builds frontend assets
5. **Package**: Creates platform-specific packages
6. **PublishArtifacts**: Prepares release artifacts

## CI/CD Pipeline

### Azure Pipelines Configuration

The `azure-pipelines.yml` defines the CI/CD workflow:

#### Pipeline Stages:

1. **Setup Stage**
   - Set build variables
   - Configure version numbers

2. **Build Stage**
   - **Frontend**: Lint and build UI
   - **Backend**: Build for all platforms
   - **Unit Tests**: Run test suites
   - **Integration Tests**: API and database tests

3. **Package Stage**
   - Create platform packages
   - Generate installers
   - Sign binaries (Windows/macOS)

4. **Release Stage**
   - Upload to GitHub releases
   - Update Docker images
   - Deploy to distribution channels

#### Build Matrix:

```yaml
strategy:
  matrix:
    Windows:
      osName: 'Windows'
      imageName: 'windows-2022'
    Linux:
      osName: 'Linux'
      imageName: 'ubuntu-22.04'
    macOS:
      osName: 'macOS'
      imageName: 'macOS-13'
```

## Deployment Configurations

### 1. Development Environment

```bash
# Backend (with hot reload)
dotnet watch run --project src/NzbDrone.Host

# Frontend (with webpack-dev-server)
yarn start

# Full stack development
./build.sh --dev
```

### 2. Docker Deployment

While no Dockerfile is present in the repository, Readarr supports Docker deployment:

```dockerfile
# Example Docker run command
docker run -d \
  --name=readarr \
  -e PUID=1000 \
  -e PGID=1000 \
  -e TZ=America/New_York \
  -p 8787:8787 \
  -v /path/to/config:/config \
  -v /path/to/books:/books \
  -v /path/to/downloads:/downloads \
  --restart unless-stopped \
  lscr.io/linuxserver/readarr:develop
```

### 3. Manual Installation

#### Linux:
```bash
# Extract package
tar -xzf Readarr.linux.tar.gz

# Set permissions
chmod +x Readarr

# Run application
./Readarr
```

#### Windows:
- Run the installer (`.exe`)
- Or extract portable version
- Run `Readarr.exe`

#### macOS:
- Mount the `.dmg` file
- Drag Readarr.app to Applications
- Or use the `.tar.gz` package

### 4. Service Installation

#### systemd (Linux):
```ini
[Unit]
Description=Readarr Daemon
After=network.target

[Service]
Type=simple
User=readarr
Group=readarr
ExecStart=/opt/Readarr/Readarr -nobrowser
Restart=on-failure

[Install]
WantedBy=multi-user.target
```

#### Windows Service:
```bash
# Install as service
Readarr.exe /install

# Start service
net start Readarr
```

## Environment Configuration

### 1. Configuration Files

- **Backend Config**: `config.xml` in app data folder
- **Environment Variables**:
  - `READARR_DATA`: Data directory path
  - `READARR_CONFIG`: Config file path
  - `READARR_LOG_LEVEL`: Logging verbosity

### 2. Build-time Configuration

```bash
# Version injection
export READARRVERSION="0.4.19.1"

# Branch configuration
export BUILD_SOURCEBRANCHNAME="develop"

# Runtime selection
export RUNTIME="linux-x64"
```

### 3. Frontend Environment

The frontend build supports environment-specific configs:

```javascript
// webpack.config.js
new webpack.DefinePlugin({
  __DEV__: !isProduction,
  'process.env.NODE_ENV': isProduction ? 
    JSON.stringify('production') : 
    JSON.stringify('development')
})
```

## Build Optimization

### 1. Frontend Optimization

- **Code Splitting**: Separate vendor bundles
- **Tree Shaking**: Remove unused code
- **Minification**: Terser for JavaScript
- **CSS Optimization**: MiniCssExtractPlugin
- **Asset Compression**: Gzip/Brotli support

### 2. Backend Optimization

- **AOT Compilation**: ReadyToRun assemblies
- **Trimming**: Remove unused framework code
- **Single File**: Self-contained executables
- **Platform-specific**: Native dependencies

### 3. Build Cache

- **Yarn Cache**: `$(Pipeline.Workspace)/.yarn`
- **NuGet Cache**: `$(Pipeline.Workspace)/.nuget/packages`
- **Webpack Cache**: In-memory and filesystem

## Release Process

### 1. Version Management

- **Major.Minor.Patch**: Semantic versioning
- **Build Number**: CI counter
- **Branch Suffix**: develop/master/feature

### 2. Release Channels

- **Develop**: Nightly builds
- **Master**: Stable releases
- **Feature**: Preview builds

### 3. Distribution

- **GitHub Releases**: Primary distribution
- **Docker Hub**: Container images
- **Package Managers**: APT/YUM repositories (community)

## Troubleshooting Build Issues

### Common Issues:

1. **Node Version Mismatch**
   ```bash
   # Use Volta to manage Node version
   volta install node@20
   ```

2. **Missing .NET SDK**
   ```bash
   # Install required SDK
   dotnet --list-sdks
   dotnet install sdk 6.0.427
   ```

3. **Frontend Build Failures**
   ```bash
   # Clean and rebuild
   yarn clean
   rm -rf node_modules
   yarn install
   yarn build
   ```

4. **Backend Restore Issues**
   ```bash
   # Clear NuGet cache
   dotnet nuget locals all --clear
   dotnet restore --force
   ```

## Best Practices

1. **Always run tests** before creating pull requests
2. **Use the build script** for consistent builds
3. **Keep dependencies updated** but test thoroughly
4. **Follow branching strategy**: develop → master
5. **Sign commits** for security
6. **Document build changes** in commit messages
7. **Monitor CI pipeline** for build failures
8. **Cache dependencies** to speed up builds