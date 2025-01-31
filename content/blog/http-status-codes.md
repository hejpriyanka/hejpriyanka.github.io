---
title: "Understanding HTTP Status Codes in REST API Design"
date: 2024-01-15
tags: ["API Design", "Web Development", "Best Practices"]
---

## The Language of Web Conversations 🌐

HTTP status codes are the internet's secret handshake. Here's your decoder ring:

### 1xx: Informational
- **100 Continue**: "Keep sending, I'm listening!"

### 2xx: Success
- **200 OK**: "Everything worked perfectly!"
- **201 Created**: "New resource born successfully"
- **204 No Content**: "Request succeeded, nothing to show"

### 3xx: Redirection
- **301 Moved Permanently**: "We've moved houses!"
- **304 Not Modified**: "Cache still fresh ☕"

### 4xx: Client Errors
- **400 Bad Request**: "Check your syntax, friend"
- **401 Unauthorized**: "Who goes there?"
- **404 Not Found**: "Digital ghost town"
- **418 I'm a teapot**: (RFC 2324 Easter egg 🫖)

### 5xx: Server Errors
- **500 Internal Error**: "Our bad!"
- **503 Service Unavailable**: "Try again later"

**Pro Tip:** Always return appropriate status codes with meaningful error messages:
```json
{
  "error": {
    "code": 404,
    "message": "User profile not found",
    "documentation": "/api/docs/errors#404"
  }
}