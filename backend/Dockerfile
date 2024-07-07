# Use an official Python runtime as the base image
FROM python:3.10.13-bullseye

# Set environment variables
ENV PYTHONUNBUFFERED=1 \
    PYTHONDONTWRITEBYTECODE=1 \
    POETRY_VERSION=1.4.2 \
    POETRY_HOME="/opt/poetry" \
    PYSETUP_PATH="/opt/pysetup" 

# Add Poetry to PATH
ENV PATH="$POETRY_HOME/bin:$PATH"

# Install Poetry and dos2unix
RUN curl -sSL https://install.python-poetry.org | python3 - && \
    apt-get update && \
    apt-get install -y dos2unix

# Set the working directory in the container
WORKDIR $PYSETUP_PATH

# Copy the pyproject.toml and poetry.lock files
COPY pyproject.toml poetry.lock ./

# Install project dependencies
RUN poetry install

RUN poetry config virtualenvs.create false

# Copy the rest of the application code
COPY . .

# Convert Windows line endings to Unix
RUN find . -type f -print0 | xargs -0 dos2unix

# Ensure the script is executable
RUN chmod +x ./prestart.sh

# Install wait-for-it
RUN apt-get update && apt-get install -y wait-for-it

# Expose the port that FastAPI will run on
EXPOSE 8000

ENV PYTHONPATH=/opt/pysetup

# Use wait-for-it to wait for the database, then run prestart and start the app
CMD cd /opt/pysetup && \
    wait-for-it ${POSTGRES_SERVER}:${POSTGRES_PORT} -- poetry run bash ./prestart.sh && \
    poetry run uvicorn app.main:app --host 0.0.0.0 --port 8000