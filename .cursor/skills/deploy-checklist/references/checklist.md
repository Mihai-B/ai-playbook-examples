# Detailed Deployment Checklist

## Code Quality

- [ ] All unit tests pass (`dotnet test`)
- [ ] No build warnings (`dotnet build -warnaserror`)
- [ ] Code coverage meets threshold (>80%)
- [ ] No TODO/HACK comments in production code paths

## Database

- [ ] All migrations committed to source control
- [ ] Idempotent SQL script generated and reviewed
- [ ] Migration tested against a copy of production data
- [ ] Rollback script prepared and tested
- [ ] No breaking schema changes without two-phase approach

## Configuration

- [ ] All required environment variables documented
- [ ] `appsettings.Production.json` reviewed (no dev/test values)
- [ ] Connection strings use managed identity or Key Vault (no plain-text passwords)
- [ ] Health check endpoints configured (`/healthz`, `/readyz`)

## Security

- [ ] No secrets in source code or config files
- [ ] `dotnet list package --vulnerable` shows no critical vulnerabilities
- [ ] Authentication and authorization enabled on all endpoints
- [ ] CORS policy configured for production origins only
- [ ] Rate limiting enabled on public endpoints
- [ ] HTTPS enforced

## Infrastructure

- [ ] Dockerfile builds successfully
- [ ] Container health check configured
- [ ] Logging configured for structured output (Serilog / Application Insights)
- [ ] Application Insights or equivalent monitoring connected
- [ ] Autoscaling rules reviewed

## Release

- [ ] Version number updated in `.csproj`
- [ ] CHANGELOG updated
- [ ] PR approved by at least one reviewer
- [ ] CI pipeline passes all stages (build, test, security scan)
