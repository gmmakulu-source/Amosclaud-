# GitHub Copilot Agent Template for Amosclaud

## Repository Overview

**Amosclaud** is a comprehensive AI/ML platform and automation system with multi-language support and distributed agent architecture.

## Technology Stack

- **Languages**: Python, JavaScript/Node.js, TypeScript, Shell Scripts, PowerShell
- **Infrastructure**: Docker, Kubernetes, Terraform
- **Deployment**: Railway, Render, Azure, AWS
- **Architecture**: Microservices with API Gateway, Distributed Agents, Serverless Components
- **APIs**: OpenAPI/Swagger, REST, gRPC

## Key Components

### Core Modules
- **amosclaud** - Main application core
- **amosclaud_agent_sdk** - Agent Software Development Kit
- **amosclaud_language** - Language processing module
- **amosclaud_metrics** - Metrics and monitoring
- **amosclaud_model** - ML/AI model management
- **amosclaud_serverless** - Serverless deployment support
- **amoscloud_ai** - Cloud AI integration
- **amoscloud_local_agent** - Local agent execution
- **amosflow** - Workflow orchestration

### Infrastructure Components
- **api-gateway** - API routing and management
- **api_key_manager** - API key and authentication management
- **authentication** - Authentication service
- **database** - Database layer
- **storage** - Data storage services
- **monitoring** - System monitoring
- **metrics-server** - Metrics collection

### User Interface
- **web** - Web dashboard
- **ui** - UI components
- **desktop** - Desktop application
- **android** - Android mobile app
- **app** - Application layer

### DevOps & Deployment
- **deploy** - Deployment scripts
- **deployment_worker** - Deployment worker service
- **docker** - Docker configurations
- **.devcontainer** - Development container setup
- **railway** - Railway deployment config
- **Infrastructure** - Infrastructure as Code

### Development Tools
- **scripts** - Utility scripts
- **cli** - Command-line interface
- **tools** - Development tools
- **tests** - Test suite

## Documentation Files

- **README.md** - Main project documentation
- **AGENTS.md** - Agent system documentation
- **CONTRIBUTING.md** - Contribution guidelines
- **SECURITY.md** - Security documentation
- **SELF_HOSTING.md** - Self-hosting instructions
- **PLATFORM_ARCHITECTURE.md** - Platform architecture details
- **TASK_ROUTER.md** - Task routing system documentation
- **BILLING.md** - Billing information
- **CODE_OF_CONDUCT.md** - Community code of conduct

## Key Configuration Files

- **pyproject.toml** - Python project configuration
- **requirements.txt** - Python dependencies
- **docker-compose.yml** - Docker Compose main config
- **docker-compose.prod.yml** - Production Docker Compose
- **docker-compose.workspace.yml** - Workspace Docker Compose
- **Dockerfile** - Container image definition
- **Makefile** - Build automation
- **main.tf** - Terraform infrastructure

## Deployment Configuration

- **railway.json** - Railway deployment manifest
- **render.yaml** - Render deployment config
- **Procfile** - Process file for Heroku-like deployments
- **Caddyfile** - Web server configuration
- **nginx.conf** - Nginx reverse proxy configuration

## Getting Started

### Installation Scripts
- **install-amosclaud.sh** / **install-amosclaud.ps1** - Installation scripts for Linux/Windows
- **start-amosclaud.bat** / **start-windows.ps1** - Startup scripts
- **start-local.sh** - Local development startup

### Environment Configuration
- **.env.example** - Example environment variables
- **.env.codex.example** - Codex API environment example
- **.env.production.example** - Production environment example
- **.env.workspace.example** - Workspace environment example

## Common Development Tasks

### Running the Application
1. Install dependencies: `pip install -r requirements.txt`
2. Set up environment: Copy and configure `.env.example` to `.env`
3. Run locally: `python main.py` or use `start-local.sh`
4. Run with Docker: `docker-compose up`

### Building & Testing
- Build: `make build`
- Run tests: `pytest tests/`
- Development requirements: `pip install -r requirements-dev.txt`

### Deployment
- Production: `docker-compose -f docker-compose.prod.yml up`
- Self-hosted: `docker-compose -f docker-compose.selfhost.yml up`
- Workspace: `docker-compose -f docker-compose.workspace.yml up`

## Important Notes for Agents

1. **Multi-Language Project**: Handle Python, JavaScript/TypeScript, Shell, and PowerShell code appropriately
2. **Microservices Architecture**: Components are loosely coupled; changes may affect multiple services
3. **Agent System**: Project includes its own agent framework - familiarize yourself with `amosclaud_agent_sdk`
4. **Configuration Management**: Multiple environment examples exist - ensure correct env vars are set
5. **Docker-First Deployment**: Most deployment paths use Docker; test container configurations
6. **API Gateway**: Changes to routes may need API gateway updates
7. **Authentication**: API key and authentication systems are centralized
8. **Scalability**: System supports Kubernetes and serverless deployments

## Repository Structure Pattern

```
root/
├── amosclaud/               # Core application
├── amosclaud_*/             # Feature modules
├── app/web/ui/              # Frontend applications
├── api-gateway/             # API routing
├── scripts/                 # Utilities
├── tests/                   # Test suite
├── deploy/                  # Deployment automation
├── Infrastructure/          # IaC
├── docs/                    # Documentation
├── docker/                  # Container configs
└── config/                  # Configuration files
```

## Code Quality & Standards

- Follow PEP 8 for Python code
- Use TypeScript for JavaScript projects
- Maintain API documentation with OpenAPI specs
- Add tests for new features
- Update CHANGELOG and documentation

## Troubleshooting Common Issues

- **Import errors**: Ensure all dependencies are installed (`pip install -r requirements.txt`)
- **Docker issues**: Check Docker daemon is running and ports aren't in use
- **Environment variables**: Verify `.env` file is properly configured
- **Agent failures**: Check agent SDK documentation and logs
- **API errors**: Review OpenAPI spec and check gateway configuration

## Resources

- Main README: `README.md`
- Architecture details: `PLATFORM_ARCHITECTURE.md`
- Task routing: `TASK_ROUTER.md`
- API specification: `openapi.yaml`
- Agent documentation: `AGENTS.md`

---

**Last Updated**: 2026-07-16  
**Template Version**: 1.0  
**Owner**: Amosclaud Forever

This template helps Copilot agents understand the Amosclaud repository structure and conventions.
