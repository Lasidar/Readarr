# Readarr Documentation Suite

Welcome to the comprehensive documentation for Readarr, an ebook and audiobook collection manager for Usenet and BitTorrent users.

## 📚 Documentation Overview

This documentation suite provides detailed information about Readarr's architecture, API interfaces, and build/deployment processes.

### Available Documentation

1. **[Architecture Documentation](architecture.md)**
   - System overview and high-level design
   - Frontend and backend architecture details
   - Communication patterns and data flow
   - External integrations and security considerations

2. **[API Reference](api-reference.md)**
   - Complete REST API documentation
   - Authentication methods
   - Endpoint descriptions with examples
   - Error handling and best practices

3. **[Build & Deployment Process](build-process.md)**
   - Build requirements and pipeline
   - CI/CD configuration
   - Deployment options (Docker, manual, service)
   - Troubleshooting build issues

4. **[Setup Guide](setup-guide.md)**
   - Development environment setup
   - Running the application locally
   - Configuration and testing
   - Common development tasks

## 🏗️ Architecture Summary

Readarr is built as a modern Single Page Application (SPA) with:

- **Frontend**: React 17 with Redux state management
- **Backend**: .NET 6.0 with ASP.NET Core
- **Database**: SQLite (default) with Dapper ORM
- **Real-time**: SignalR for WebSocket communication
- **API**: RESTful API with OpenAPI documentation

## 🔌 Key Features

- **Book Management**: Track and organize ebooks and audiobooks
- **Automated Downloads**: Integration with Usenet and BitTorrent
- **Quality Profiles**: Automatic upgrades based on quality preferences
- **Metadata Management**: Book information from multiple sources
- **Calendar View**: Track upcoming book releases
- **Notifications**: Multiple notification service integrations
- **Multi-platform**: Windows, Linux, macOS, and Docker support

## 🚀 Quick Start

### For Users

1. Download the appropriate package for your platform
2. Extract and run the application
3. Access the web UI at `http://localhost:8787`
4. Complete the setup wizard

### For Developers

```bash
# Clone the repository
git clone https://github.com/Readarr/Readarr.git
cd Readarr

# Install dependencies
yarn install
dotnet restore src/Readarr.sln

# Run the application
./build.sh --dev
```

## 📖 Additional Resources

- **Official Wiki**: https://wiki.servarr.com/readarr
- **Discord Community**: https://readarr.com/discord
- **GitHub Repository**: https://github.com/Readarr/Readarr
- **Support Forum**: https://forums.servarr.com/

## 🤝 Contributing

Readarr is open source and welcomes contributions. Please see the [CONTRIBUTING.md](../CONTRIBUTING.md) file for guidelines.

## ⚠️ Important Notice

As noted in the main README, the Readarr project has been retired due to metadata issues. While the codebase remains available, active development has ceased. Community forks and alternatives are encouraged.

## 📄 License

Readarr is licensed under the GNU General Public License v3.0. See the [LICENSE.md](../LICENSE.md) file for details.

---

*This documentation was generated through automated analysis of the Readarr codebase. While comprehensive, it may not cover every aspect of the system. For the most up-to-date information, please refer to the source code and official resources.*