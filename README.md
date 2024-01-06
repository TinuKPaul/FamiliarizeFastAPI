# Properties of FastAPI

## 1. Dependency Injection:

Question: 

Explain the concept of dependency injection in FastAPI and how it aids in request handling.

Answer:

Dependency injection in FastAPI involves passing dependencies into functions or classes, aiding in managing and providing required dependencies during request handling.

```python
from fastapi import Depends, FastAPI

app = FastAPI()

def get_db_connection():
    # function to get database connection
    return "Connected to DB"

@app.get("/")
async def root(db: str = Depends(get_db_connection)):
    return {"message": db}
```

## 2. Async and Await:

Question:

What are the advantages of using async and await in FastAPI? How does it differ from synchronous execution?

Answer:

Async and await in FastAPI enable handling multiple requests concurrently without blocking, enhancing scalability compared to synchronous execution.

```python
from fastapi import FastAPI

app = FastAPI()

@app.get("/")
async def root():
    async def some_async_operation():
        return "Async operation result"

    result = await some_async_operation()
    return {"message": result}
```

## 3. Pydantic Models:
Question:

Discuss the role of Pydantic models in FastAPI and their significance in request and response handling.

Answer:

Pydantic models define the structure and types of request and response data in FastAPI, handling serialization, deserialization, and validation.

```python
from pydantic import BaseModel
from fastapi import FastAPI

app = FastAPI()

class Item(BaseModel):
    name: str
    description: str = None
    price: float
    tax: float = None

@app.post("/items/")
async def create_item(item: Item):
    return item
```

## 4. Type Hints:

Question:

How does FastAPI leverage type hints and how does it enhance the development process?

Answer:

FastAPI uses Python's type hinting system to improve code readability and automatic API documentation generation.

```python
from fastapi import FastAPI

app = FastAPI()

@app.get("/")
async def root() -> dict:
    return {"message": "Hello, FastAPI!"}
```

## 5. API Design and Best Practices:

Question:

What principles and practices do you follow to design RESTful APIs using FastAPI?

Answer:

RESTful APIs in FastAPI follow principles such as resource naming, HTTP methods (GET, POST, PUT, DELETE), use of proper status codes, statelessness, etc., to ensure a standardized and predictable API design.

```python
from fastapi import FastAPI

app = FastAPI()

@app.post("/items/")
async def create_item():
    # Create a new item
    return {"message": "Item created successfully"}

@app.get("/items/{item_id}")
async def get_item(item_id: int):
    # Retrieve item by ID
    return {"item_id": item_id, "name": "Sample Item"}
```

## 6. Security and Authentication:

Question:

Explain the steps to implement JWT-based authentication in FastAPI.

Answer:

Implementing JWT-based authentication in FastAPI involves using libraries like PyJWT to issue tokens, validate them, and handle user authentication based on token information.

```python
from fastapi import FastAPI, Depends, HTTPException, status
from fastapi.security import OAuth2PasswordBearer

app = FastAPI()

oauth2_scheme = OAuth2PasswordBearer(tokenUrl="token")

def verify_token(token: str = Depends(oauth2_scheme)):
    # Function to verify JWT token
    if token != "fake-access-token":
        raise HTTPException(status_code=status.HTTP_401_UNAUTHORIZED, detail="Invalid token")
    return token

@app.get("/protected/")
async def protected_route(token: str = Depends(verify_token)):
    return {"message": "Access granted"}
```

## 7. Database Integration and ORM:

Question:

Compare and contrast SQLAlchemy and Tortoise-ORM in the context of FastAPI. When would you use one over the other?

Answer:

SQLAlchemy is a robust SQL toolkit and Object-Relational Mapping (ORM) library, while Tortoise-ORM is an asyncio ORM for Python compatible with FastAPI. The choice depends on project requirements and familiarity.

```python
from fastapi import FastAPI
from tortoise import Tortoise, fields
from tortoise.models import Model

app = FastAPI()

class Product(Model):
    id = fields.IntField(pk=True)
    name = fields.CharField(max_length=255)

async def initialize():
    await Tortoise.init(
        db_url='sqlite://db.sqlite3',
        modules={'models': ['__main__']}
    )
    await Tortoise.generate_schemas()

@app.on_event("startup")
async def startup_event():
    await initialize()

@app.post("/products/")
async def create_product(name: str):
    product_obj = await Product.create(name=name)
    return {"message": "Product created successfully"}
```

## 8. Testing and Quality Assurance:

Question:

How do you approach testing FastAPI applications? Describe your experience with unit testing and integration testing in FastAPI.

Answer:

Testing in FastAPI involves unit tests, integration tests using libraries like pytest, and endpoint tests to ensure the correctness of API behavior.

```python
from fastapi.testclient import TestClient
from myapp.main import app

client = TestClient(app)

def test_read_main():
    response = client.get("/")
    assert response.status_code == 200
    assert response.json() == {"message": "Hello, FastAPI!"}
```

## 9. Deployment and DevOps:

Question:

Walk us through the deployment process of a FastAPI application onto Docker and Kubernetes.

Answer:

Deploying FastAPI apps on Docker and Kubernetes involves creating Docker images, defining Kubernetes manifests, deploying containers, and managing them in a Kubernetes cluster.

```shell
FROM tiangolo/uvicorn-gunicorn-fastapi:python3.9

COPY ./app /app
```

Sample of Dockerfile

```shell
# Use the official Python image as the base image
FROM python:3.9-slim

# Set the working directory inside the container
WORKDIR /app

# Copy the requirements.txt file into the container at /app
COPY ./requirements.txt /app/

# Install any dependencies specified in requirements.txt
RUN pip install --upgrade pip
RUN pip install --no-cache-dir -r requirements.txt

# Copy the entire project directory into the container at /app
COPY src /app/

# Expose the port that FastAPI will run on (default is 8000)
EXPOSE 8000

# Command to run the application
CMD ["uvicorn", "api.main:app", "--host", "0.0.0.0", "--port", "8000"]

```

## 10. Performance Optimization:

Question:

Describe methods to optimize API performance in FastAPI, considering factors like caching, async I/O, and load balancing.

Answer:

Techniques include caching frequently accessed data, leveraging async I/O for non-blocking operations, and employing load balancing to distribute traffic.

```python
from fastapi import FastAPI, BackgroundTasks
import time

app = FastAPI()

def expensive_operation():
    # Simulating a costly operation
    time.sleep(5)

@app.get("/optimized/")
async def optimized_route(background_tasks: BackgroundTasks):
    background_tasks.add_task(expensive_operation)
    return {"message": "Operation started in the background"}
```

## 11. Troubleshooting and Debugging:

Question:

Explain your approach to debugging issues in FastAPI applications. How do you handle error logging and diagnostics?

Answer:

Debugging in FastAPI involves logging errors, using debugging tools like PyCharm, pdb, or integrated debugging in IDEs, and analyzing logs to identify and resolve issues.

```python
from fastapi import FastAPI

app = FastAPI()

@app.get("/debug/")
async def debug_route():
    # Debugging code
    breakpoint()
    return {"message": "Debug route"}
```

## 12. Middleware


Middleware is a software component or a piece of code that sits between different parts of an application, allowing you to intercept, process, or modify incoming HTTP requests, outgoing responses, or both. It acts as a bridge or a layer that can perform various functions before or after a request reaches the application's core logic.

In the context of web development, middleware operates within the request-response cycle, allowing developers to:

1. Process Incoming Requests: Middleware can inspect and potentially modify incoming requests before they reach the main application logic. This might involve authentication, logging, parsing, or modifying request data.

2. Manipulate Outgoing Responses: Similarly, middleware can intercept and modify outgoing responses before they are sent back to the client. This could involve adding headers, transforming data, or handling errors in a specific way.

3. Perform Additional Operations: Middleware can perform various additional operations, such as handling sessions, caching, rate limiting, or any other custom processing required by the application.

Middleware is commonly used in web frameworks and platforms to implement cross-cutting concerns or functionalities that need to be applied uniformly across different parts of the application without explicitly modifying each endpoint or route. It allows for cleaner, more modular code by separating concerns and promoting reusability.

### Example1: basic logging middleware that logs information about incoming requests

```python
from fastapi import FastAPI, Request

app = FastAPI()

# Define middleware function
async def log_requests(request: Request, call_next):
    # Log information about incoming request
    print(f"Received request: {request.method} {request.url}")

    # Continue processing the request
    response = await call_next(request)

    # Log information about outgoing response
    print(f"Sent response: {response.status_code}")

    return response

# Register middleware with FastAPI app
app.middleware('http')(log_requests)

# Define a simple endpoint
@app.get("/")
async def read_root():
    return {"message": "Hello, World!"}
```
### Example 2 : Authentication check 

```python
from fastapi import FastAPI, Request, HTTPException, Depends

app = FastAPI()

# Simulated user database (for demonstration purposes)
fake_users_db = {
    "alice": {
        "username": "alice",
        "password": "password123",
    }
}

# Middleware for authentication check
async def authenticate(request: Request, call_next):
    auth_header = request.headers.get("Authorization")
    if not auth_header or "Bearer" not in auth_header:
        raise HTTPException(status_code=401, detail="Invalid authentication")

    token = auth_header.split("Bearer ")[-1]
    # Validate the token (in a real scenario, this could involve token decoding and verification)
    if token != "valid_token":
        raise HTTPException(status_code=401, detail="Invalid token")

    return await call_next(request)

# Register middleware with FastAPI app
app.middleware('http')(authenticate)

# Protected endpoint
@app.get("/protected")
async def protected_endpoint():
    return {"message": "You have access to the protected resource"}

```

## 13. BackgroundTasks 

In FastAPI, you can implement background tasks using the BackgroundTasks class to run functions in the background while handling a request. This is particularly useful for executing tasks that don't need to block the response to the client. Below is an example demonstrating how to implement background tasks in FastAPI:

```python

from fastapi import FastAPI, BackgroundTasks
import time

app = FastAPI()

def perform_background_task(item_id: int, message: str):
    """
    Simulated background task that takes some time to execute.
    """
    print(f"Background task started for item_id: {item_id}")
    time.sleep(5)  # Simulating a time-consuming task
    print(f"Background task completed for item_id: {item_id}. Message: {message}")

@app.post("/send-task/")
async def send_background_task(item_id: int, message: str, background_tasks: BackgroundTasks):
    """
    Endpoint to trigger a background task.
    """
    # Add the background task to be executed
    background_tasks.add_task(perform_background_task, item_id, message)
    
    return {"message": "Background task has been scheduled"}
```

The function perform_background_task simulates a time-consuming task.

The /send-task/ endpoint uses BackgroundTasks from FastAPI to add a background task (perform_background_task) when it receives a POST request.

When a request is made to /send-task/, it schedules the background task to run asynchronously while the response is sent immediately.

You can test this by sending a POST request to http://127.0.0.1:8000/send-task/ with the item_id and message as parameters. 

The background task will execute without blocking the response to the client.
