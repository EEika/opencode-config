---
name: dotnet-solution-navigator
description: Navigate and understand .NET solution architecture with Clean Architecture patterns
license: MIT
compatibility: opencode-0.1.0
metadata:
  category: dotnet
  version: 1.0.0
  target_framework: net10.0
  architecture: clean-architecture
---

# .NET Solution Navigator

A practical guide for AI agents to navigate and understand .NET solution architecture, specifically tailored for Clean Architecture projects using ASP.NET Core, CosmosDB, and Camunda/Zeebe.

## 1. Solution Discovery

### Find the Solution File

```bash
# Locate .sln file (usually in root or src/)
find . -name "*.sln" -maxdepth 2
```

### List All Projects

```bash
# Option 1: Using dotnet CLI
dotnet sln list

# Option 2: Parse the .sln file directly
grep "\.csproj" *.sln
```

### Check Target Framework

```bash
# Find target framework version across all projects
find . -name "*.csproj" -exec grep -H "<TargetFramework>" {} \;

# Or for a specific project
grep "<TargetFramework>" path/to/Project.csproj
```

### Project Dependencies

```bash
# See project references
dotnet list <project.csproj> reference

# See package dependencies
dotnet list <project.csproj> package
```

## 2. Clean Architecture Layer Map

Understanding the layered structure of a typical Clean Architecture .NET solution:

### **Api Layer** (Presentation)
- **Location**: `*.Api/` or `*.WebApi/`
- **Contents**: Controllers, Middleware, Filters, Program.cs, Startup configuration
- **Purpose**: HTTP entry point, request/response handling, routing
- **Key Files**:
  - `Program.cs` - Application startup and DI configuration
  - `Controllers/*.cs` - API endpoints
  - `Middleware/*.cs` - Request pipeline components

### **Services Layer** (Application/Business Logic)
- **Location**: `*.Services/` or `*.Application/`
- **Contents**: Business logic, service interfaces, use cases, validators
- **Purpose**: Core application logic, orchestration between layers
- **Patterns**: Service classes implementing business rules, CQRS handlers

### **Repositories Layer** (Infrastructure/Persistence)
- **Location**: `*.Repositories/` or `*.Infrastructure/`
- **Contents**: Data access implementations, CosmosDB clients, external service integrations
- **Purpose**: Data persistence, external dependencies
- **Patterns**: Repository pattern with interfaces in Services, implementations here

### **Models Layer** (Domain/Shared)
- **Location**: `*.Models/` or `*.Domain/` or `*.Contracts/`
- **Contents**: DTOs, entities, enums, value objects, domain models
- **Purpose**: Shared types across layers, data contracts
- **Note**: Often referenced by all other projects

### **Workers Layer** (Background Processing)
- **Location**: `*.Workers/` or `*.BackgroundJobs/`
- **Contents**: Zeebe job workers, hosted services, background tasks
- **Purpose**: Asynchronous processing, workflow automation
- **Patterns**: IHostedService, Zeebe job handlers

## 3. Where to Put New Code (Decision Guide)

| What You're Adding | Where It Goes | Example |
|-------------------|---------------|---------|
| New REST endpoint | `Api/Controllers/` | `UsersController.cs` |
| New business logic | `Services/` | `UserService.cs`, `IUserService.cs` |
| New data access | `Repositories/` | `UserRepository.cs`, `IUserRepository.cs` |
| New DTO/model | `Models/` | `UserDto.cs`, `User.cs` |
| New background job | `Workers/` | `UserSyncWorker.cs` |
| New middleware | `Api/Middleware/` | `AuthenticationMiddleware.cs` |
| New validator | `Services/Validators/` | `UserValidator.cs` |
| Configuration model | `Models/Configuration/` | `CosmosDbSettings.cs` |

## 4. Dependency Injection Registration

### Find DI Configuration

```bash
# Primary location
cat Program.cs | grep -A 20 "ConfigureServices\|var builder"

# Look for extension methods
find . -name "*ServiceCollectionExtensions.cs" -o -name "*ServiceRegistration.cs"
```

### Common Registration Patterns

```csharp
// Scoped (per request) - typical for services, repositories
builder.Services.AddScoped<IUserService, UserService>();

// Singleton (app lifetime) - for clients, configuration
builder.Services.AddSingleton<ICosmosDbClient, CosmosDbClient>();

// Transient (each injection) - for lightweight, stateless services
builder.Services.AddTransient<IValidator<User>, UserValidator>();

// Hosted services (background workers)
builder.Services.AddHostedService<ZeebeWorker>();
```

## 5. Key Files to Read First

When exploring a new .NET project, read these files in order:

1. **`Program.cs`** (or `Startup.cs` in older projects)
   - Understand middleware pipeline, DI setup, app configuration
   - See what services are registered and how

2. **`*.csproj` files**
   - Check .NET version, package dependencies, project references
   - Look for: `<TargetFramework>`, `<PackageReference>`, `<ProjectReference>`

3. **`appsettings.json` / `appsettings.Development.json`**
   - Configuration structure, connection strings, feature flags
   - Environment-specific overrides

4. **`.editorconfig`**
   - Code style rules, naming conventions, formatting preferences
   - Follow these patterns when writing new code

5. **Solution structure** (`*.sln`)
   - Understand project relationships and dependencies
   - Identify test projects, shared libraries

## 6. Common .NET Patterns to Recognize

### Result<T, Error> Pattern
```csharp
// Instead of throwing exceptions, return Result types
public Result<User, Error> GetUser(int id)
{
    if (user == null) return Error.NotFound;
    return user;
}
```

### Repository Pattern
```csharp
// Interface in Services/Application layer
public interface IUserRepository { }

// Implementation in Repositories/Infrastructure layer
public class UserRepository : IUserRepository { }
```

### Primary Constructors (C# 12+)
```csharp
// DI parameters defined in class declaration
public class UserService(IUserRepository repo, ILogger<UserService> logger) : IUserService
{
    // Fields automatically created from constructor params
}
```

## 7. Build & Test Commands

### Build
```bash
# Build entire solution
dotnet build

# Build specific project
dotnet build path/to/Project.csproj

# Build with specific configuration
dotnet build -c Release
```

### Test
```bash
# Run all tests
dotnet test

# Run tests with detailed output
dotnet test --verbosity normal

# Run tests in specific project
dotnet test path/to/Tests.csproj

# Find test projects
find . -name "*.csproj" -exec grep -l "Microsoft.NET.Test.Sdk\|xunit\|NUnit\|MSTest" {} \;
```

### Run
```bash
# Run the API project
dotnet run --project path/to/Api.csproj

# Run with specific environment
ASPNETCORE_ENVIRONMENT=Development dotnet run --project path/to/Api.csproj
```

### Restore & Clean
```bash
# Restore NuGet packages
dotnet restore

# Clean build artifacts
dotnet clean
```

---

**Usage**: Load this skill when working on .NET projects to quickly navigate solution architecture, understand layer responsibilities, and make informed decisions about where new code belongs.
