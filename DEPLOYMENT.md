# Deployment Information

## Public URLs
- Railway: https://agent-railway-production.up.railway.app
- Render: https://ai-agent-kr7p.onrender.com

## Platform
Railway + Render

## Test Commands

### Railway Health Check
```bash
curl https://agent-railway-production.up.railway.app/health
```

### Railway API Test
```bash
curl -X POST https://agent-railway-production.up.railway.app/ask \
  -H "Content-Type: application/json" \
  -d '{"question":"Hello"}'
```

### Render Health Check
```bash
curl https://ai-agent-kr7p.onrender.com/health
```

### Render API Test
```bash
curl -X POST https://ai-agent-kr7p.onrender.com/ask \
  -H "Content-Type: application/json" \
  -d '{"question":"Hello"}'
```

## Environment Variables Set
- Railway: PORT, AGENT_API_KEY
- Render: OPENAI_API_KEY, AGENT_API_KEY, ENVIRONMENT, PYTHON_VERSION

## Screenshots
- [Railway deploy](screenshots/railway_deploy.png)
- [Render deploy](screenshots/render_deploy.png)
