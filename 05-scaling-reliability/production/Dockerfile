# Stateless agent image (production)
FROM python:3.11-slim

WORKDIR /app

# Install dependencies
COPY 05-scaling-reliability/production/requirements.txt /app/requirements.txt
RUN pip install --no-cache-dir -r requirements.txt

# Copy app and utils
COPY 05-scaling-reliability/production/app.py /app/app.py
COPY 05-scaling-reliability/production/utils /app/utils

EXPOSE 8000

CMD ["uvicorn", "app:app", "--host", "0.0.0.0", "--port", "8000"]
