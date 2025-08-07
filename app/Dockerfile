FROM python:3.11-slim

# Crée un utilisateur non-root
RUN useradd -ms /bin/bash appuser

WORKDIR /app

COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt

COPY . .

# Change de propriétaire et passe à l'utilisateur non-root
RUN chown -R appuser:appuser /app
USER appuser

EXPOSE 5000
CMD ["python", "main.py"]
