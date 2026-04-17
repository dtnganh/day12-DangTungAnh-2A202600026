# Deployment Information

## Public URLs
- Railway: https://agent-railway-production.up.railway.app
- Render: https://ai-agent-kr7p.onrender.com
- Final Project (Railway): https://day12-final-agent-production.up.railway.app

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

### Final Project (Railway) Health Check
```bash
curl https://day12-final-agent-production.up.railway.app/health
```

### Final Project (Railway) API Test
```bash
curl -X POST https://day12-final-agent-production.up.railway.app/ask \
  -H "X-API-Key: final-secret-key" \
  -H "Content-Type: application/json" \
  -d '{"question":"Hello"}'
```

Expected response example:
{"question":"Hello","answer":"Tôi là AI agent được deploy lên cloud. Câu hỏi của bạn đã được nhận.","model":"gpt-4o-mini"}

## Environment Variables Set
- Railway: PORT, AGENT_API_KEY
- Render: OPENAI_API_KEY, AGENT_API_KEY, ENVIRONMENT, PYTHON_VERSION

## Screenshots
- [Railway deploy](screenshots/railway_deploy.png)
- [Render deploy](screenshots/render_deploy.png)
