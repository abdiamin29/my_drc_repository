# Exercise B: API Reference Entry

## Create a Task

Creates a new task in an existing project. The request must be sent by an authenticated user who has permission to add tasks to the project. The selected assignee must be a member of that project.

### Endpoint

| Item | Value |
|---|---|
| HTTP method | `POST` |
| Path | `/api/v1/projects/{projectId}/tasks` |
| Authentication | Bearer token |
| Content type | `application/json` |
| Success response | `201 Created` |

### Path parameters

| Name | Data type | Required | Description |
|---|---|---|---|
| `projectId` | String (UUID) | Yes | Unique identifier of the project in which the task will be created. |

### Query parameters

This endpoint does not use query parameters.

### Request headers

| Header | Required | Description |
|---|---|---|
| `Authorization` | Yes | Supplies the access token in the format `Bearer <access_token>`. The token identifies the user making the request. |
| `Content-Type` | Yes | Must be `application/json`, because the request body is JSON. |
| `Accept` | Yes | Should be `application/json`, indicating that the client expects a JSON response. |

### Request body

The request body must be a JSON object.

| Name | Data type | Required | Description |
|---|---|---|---|
| `title` | String | Yes | Short name of the task. Must contain 1–120 characters after trimming whitespace. |
| `description` | String | No | Additional details about the task. If supplied, it may contain up to 2,000 characters. |
| `assigneeId` | String (UUID) | Yes | Unique identifier of the user assigned to the task. The user must be a member of the specified project. |
| `dueDate` | String (date, ISO 8601) | Yes | Date by which the task should be completed, formatted as `YYYY-MM-DD`. |
| `priority` | String (enum) | Yes | Task priority. Accepted values are `low`, `medium`, and `high`. |

### Example request

**HTTP request**

```http
POST /api/v1/projects/8f14e45f-ea12-4c9a-9a2b-6f5a1d0c7e31/tasks HTTP/1.1
Host: api.taskmanager.example
Authorization: Bearer YOUR_ACCESS_TOKEN
Content-Type: application/json
Accept: application/json
```

**JSON body**

```json
{
  "title": "Prepare project launch checklist",
  "description": "List the final checks required before the project launch.",
  "assigneeId": "2c1b7a90-6d4e-4f2a-8b31-9a0c5d7e1234",
  "dueDate": "2026-12-15",
  "priority": "high"
}
```

### Successful response

**Status:** `201 Created`

The task was created successfully. The response includes the new task's identifier and its saved details.

```json
{
  "id": "a73d9c21-5e8b-4f10-9c62-1d3a7b8e4f90",
  "projectId": "8f14e45f-ea12-4c9a-9a2b-6f5a1d0c7e31",
  "title": "Prepare project launch checklist",
  "description": "List the final checks required before the project launch.",
  "assignee": {
    "id": "2c1b7a90-6d4e-4f2a-8b31-9a0c5d7e1234",
    "name": "Maya Otieno"
  },
  "dueDate": "2026-12-15",
  "priority": "high",
  "status": "open",
  "createdAt": "2026-11-25T14:15:30Z",
  "updatedAt": "2026-11-25T14:15:30Z"
}
```

### HTTP response codes

| Status code | Meaning | When it occurs |
|---|---|---|
| `201 Created` | Task created | The request is valid and the task has been saved successfully. |
| `400 Bad Request` | Invalid request format | The JSON is malformed or the request cannot be parsed. |
| `401 Unauthorized` | Authentication required | The access token is missing, invalid, or expired. |
| `403 Forbidden` | Permission denied | The user is authenticated but does not have permission to create tasks in this project. |
| `404 Not Found` | Resource not found | The specified project does not exist or is not available to the user. |
| `409 Conflict` | Request conflicts with project state | The project is archived or otherwise configured to reject new tasks. |
| `415 Unsupported Media Type` | Unsupported content type | The request body is not sent as `application/json`. |
| `422 Unprocessable Content` | Validation failed | A required field is missing, a value has the wrong type or format, the title or description exceeds its limit, the priority is not one of the accepted values, the date is invalid, or the assignee is not a member of the project. |
| `429 Too Many Requests` | Rate limit exceeded | The user has sent too many requests within the allowed time period. |
| `500 Internal Server Error` | Server error | An unexpected error occurs while processing the request. |
| `503 Service Unavailable` | Service temporarily unavailable | The API is temporarily unable to process requests, for example during maintenance or an outage. |

### Error response format

For error responses, the API returns a JSON object with an error code and a human-readable message. For example:

```json
{
  "error": {
    "code": "VALIDATION_ERROR",
    "message": "The priority must be low, medium, or high."
  }
}
```

### Notes

- All dates in `dueDate` use the `YYYY-MM-DD` format.
- The API returns timestamps in UTC using ISO 8601 format.
- The server assigns the task ID, initial status, and creation/update timestamps. Clients must not provide these fields when creating a task.
- This reference describes a hypothetical API. The endpoint path, field names, validation rules, and response structure are a proposed design for this exercise.

### Summary

The `POST /api/v1/projects/{projectId}/tasks` endpoint allows an authenticated and authorized user to create a task in a project. It accepts the task title, optional description, assignee ID, due date, and priority, then returns the saved task with a `201 Created` status when successful.
