---
title: "Building Your First REST API with FastAPI: A Step-by-Step Guide"
date: 2023-10-27T10:00:00+00:00
draft: false
tags: ["fastapi", "python", "api", "rest", "web development"]
categories: ["Web Development", "Python"]
---

# Building Your First REST API with FastAPI: A Step-by-Step Guide

FastAPI is a modern, fast (high-performance) web framework for building APIs with Python 3.7+ based on standard Python type hints. It's incredibly easy to learn and use, and boasts automatic interactive API documentation (Swagger UI) and data validation. This guide will walk you through building a simple REST API with FastAPI, demonstrating its key features.

## What We'll Build

We'll create a simple API that manages a list of "items." It will have endpoints for:

- Creating a new item (POST)
- Retrieving an item by ID (GET)
- Retrieving all items (GET)
- Updating an item (PUT)
- Deleting an item (DELETE)

## Setting Up

First, you'll need to install FastAPI and Uvicorn (an ASGI server):

```bash
pip install fastapi uvicorn

## Creating the API
Create a file named main.py:

from typing import List, Optional
from fastapi import FastAPI, HTTPException
from pydantic import BaseModel

app = FastAPI()

class Item(BaseModel):
    id: int
    name: str
    description: Optional[str] = None

items: List[Item] = []

@app.post("/items/", status_code=201)
async def create_item(item: Item):
    if any(x.id == item.id for x in items):
        raise HTTPException(status_code=400, detail="Item ID already exists")
    items.append(item)
    return item

@app.get("/items/")
async def read_items():
    return items

@app.get("/items/{item_id}")
async def read_item(item_id: int):
    for item in items:
        if item.id == item_id:
            return item
    raise HTTPException(status_code=404, detail="Item not found")

@app.put("/items/{item_id}")
async def update_item(item_id: int, item: Item):
    for i, existing_item in enumerate(items):
        if existing_item.id == item_id:
            items[i] = item
            return item
    raise HTTPException(status_code=404, detail="Item not found")

@app.delete("/items/{item_id}")
async def delete_item(item_id: int):
    for i, item in enumerate(items):
        if item.id == item_id:
            del items[i]
            return {"message": "Item deleted"}
    raise HTTPException(status_code=404, detail="Item not found")

```

## Explanation

- **Item** is a Pydantic model defining the structure of our items. Pydantic handles data validation.
- **items** is a list to store our items (in-memory for this example).
- `@app.post`, `@app.get`, `@app.put`, and `@app.delete` define the API endpoints and their HTTP methods.
- Type hints (e.g., `item: Item`, `item_id: int`) are used for validation and documentation.
- Error handling is done using `HTTPException`.

## Running the API

Start the server using Uvicorn:

```bash
uvicorn main:app --reload
```

The `--reload` flag will automatically restart the server when you make changes to the code.

## Swagger UI

Navigate to [http://127.0.0.1:8000/docs](http://127.0.0.1:8000/docs) in your browser. You'll see the interactive Swagger UI documentation, automatically generated from your code! You can test your API endpoints directly from here.

## Postman Testing

1. Install Postman: Download and install Postman from [https://www.postman.com/](https://www.postman.com/).
2. Import the API: You can manually create requests in Postman, or import the OpenAPI (Swagger) specification from [http://127.0.0.1:8000/openapi.json](http://127.0.0.1:8000/openapi.json).
3. Test the endpoints: Use Postman to send requests to your API endpoints. For example:
    - **POST /items/**: Create a new item by sending a JSON payload in the request body (e.g., `{"id": 1, "name": "My Item", "description": "A cool item"}`).
    - **GET /items/**: Retrieve all items.
    - **GET /items/1**: Retrieve the item with ID 1.
    - **PUT /items/1**: Update the item with ID 1 by sending a new JSON payload.
    - **DELETE /items/1**: Delete the item with ID 1.
