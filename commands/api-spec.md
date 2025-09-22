---
allowed-tools: Read, Edit, MultiEdit, Bash(find:*), Bash(grep:*)
argument-hint: [function-name] [file-path (optional)]
description: Generate TypeScript API specification from function analysis with DTO/Model detection
---

# Generate TypeScript API Specification

Analyzing function: **$1**
${2:+File path: $2}

## Task: Generate Complete API Specification with Model Analysis

${2:+@$2}

Analyze the function "$1" and generate a comprehensive API specification for a TypeScript React frontend application.

### Enhanced Analysis for C#/.NET Backends

First, detect if this is a C# backend and locate related data models:

#### 1. Detect Backend Type

- Check file extension (.cs for C#, .ts/.js for Node.js, .py for Python, .java for Java)
- Look for C# attributes like [HttpGet], [HttpPost], [Route], [FromBody], [FromQuery]
- Identify ASP.NET Core patterns (IActionResult, ActionResult<T>, Task<IActionResult>)

#### 2. Find Related Models (C# Specific)

Search for and analyze:

- **DTOs** (Data Transfer Objects): `*DTO.cs`, `*Dto.cs`, `*RequestDto.cs`, `*ResponseDto.cs`
- **ViewModels**: `*ViewModel.cs`, `*VM.cs`
- **Models/Entities**: `*Model.cs`, `*Entity.cs`
- **DAOs** (Data Access Objects): `*DAO.cs`, `*Dao.cs`
- **Command/Query objects**: `*Command.cs`, `*Query.cs` (for CQRS patterns)

Use these commands to locate models:
!`find . -type f -name "*DTO.cs" -o -name "*Dto.cs" -o -name "*Model.cs" -o -name "*ViewModel.cs" 2>/dev/null | head -20`
!`grep -r "class.*${1/get|create|update|delete/}.*[DTO|Model|ViewModel]" --include="*.cs" . 2>/dev/null | head -10`

#### 3. Analyze Model Structure

For each located model file:

- Read the class definition
- Extract all properties with their types
- Note data annotations ([Required], [MaxLength], [Range], etc.)
- Identify nullable properties (? in C# 8+)
- Map C# types to TypeScript equivalents:
  - `string` → `string`
  - `int/long/decimal/double/float` → `number`
  - `bool` → `boolean`
  - `DateTime` → `string` (ISO 8601 format)
  - `Guid` → `string`
  - `List<T>/IEnumerable<T>` → `T[]`
  - `Dictionary<K,V>` → `Record<K, V>`
  - Custom classes → Interface definitions

### Analysis Requirements

1. **Identify HTTP Method**:
   - For C#: Look for [HttpGet], [HttpPost], [HttpPut], [HttpDelete], [HttpPatch]
   - For Node.js: Check express router methods
   - Function name patterns (get*, fetch*, create*, update*, delete*, remove*)

2. **Determine Endpoint Route**:
   - For C#: Extract from [Route] attribute or controller/action convention
   - Consider [ApiController] route prefix
   - Map route parameters: {id} → :id

3. **Extract Type Information**:
   - **Input**: Analyze method parameters and their attributes
     - [FromBody] → Request body
     - [FromQuery] → Query parameters
     - [FromRoute] → URL parameters
     - [FromForm] → Form data
   - **Output**: Analyze return type
     - ActionResult<T> → Extract T
     - IActionResult with Ok(model) → Analyze model type
     - Task<T> → Extract inner type
   - **Validation**: Extract from data annotations

### Generate the Following Specification

Create a complete TypeScript API specification file with:

#### TypeScript Type Definitions

```typescript
// Request Type (if POST/PUT/PATCH)
interface [FunctionName]Request {
  // All request body parameters with proper types
}

// Query/Params Type (if GET/DELETE)
interface [FunctionName]Params {
  // URL parameters and query strings
}

// Response Type
interface [FunctionName]Response {
  success: boolean;
  data: {
    // Actual response data structure
  };
  meta?: {
    // Pagination or metadata if applicable
  };
}

// Error Response Type
interface [FunctionName]ErrorResponse {
  success: false;
  error: string;
  code: string;
  details?: Record<string, any>;
}
```

#### API Endpoint Documentation

```typescript
/**
 * Endpoint: [METHOD] [ROUTE]
 * Description: [Clear description of what this endpoint does]
 * Authentication: [Required | Optional | None]
 * Rate Limit: [If applicable]
 */
```

#### JSON Examples

```typescript
// Example Request
const exampleRequest: [Type] = {
  // Realistic example values
};

// JSON String Format - Request
const requestJSON = `{
  // Properly formatted JSON string
}`;

// Example Response
const exampleResponse: [Type] = {
  // Realistic response values
};

// JSON String Format - Response
const responseJSON = `{
  // Properly formatted JSON string
}`;
```

#### Parameter Documentation

```typescript
/**
 * Parameter Explanations:
 *
 * REQUEST PARAMETERS:
 * @param {type} paramName - Description, constraints, validation rules
 * @param {type} [optionalParam] - Optional parameters marked with []
 *
 * RESPONSE FIELDS:
 * @field {type} fieldName - What this field represents
 * @field {type} nestedField.property - Nested object documentation
 *
 * VALIDATION RULES:
 * - List all validation requirements
 * - Data format specifications
 * - Business logic constraints
 *
 * ERROR SCENARIOS:
 * - 400: Invalid input parameters
 * - 401: Authentication required
 * - 403: Insufficient permissions
 * - 404: Resource not found
 * - 409: Conflict with existing resource
 * - 500: Internal server error
 */
```

#### React Hook Implementation

```typescript
// Custom React hook for this API endpoint
import { useState, useCallback } from 'react';
import axios, { AxiosError } from 'axios';

export const use[FunctionName] = () => {
  const [loading, setLoading] = useState(false);
  const [data, setData] = useState<[ResponseType] | null>(null);
  const [error, setError] = useState<[ErrorType] | null>(null);

  const [actionName] = useCallback(async (
    params: [RequestType]
  ): Promise<[ResponseType] | null> => {
    setLoading(true);
    setError(null);

    try {
      const response = await axios.[method]<[ResponseType]>(
        '[endpoint]',
        params, // or { params } for GET requests
        {
          headers: {
            'Content-Type': 'application/json',
            // Authorization: `Bearer ${token}` if needed
          }
        }
      );

      setData(response.data);
      return response.data;
    } catch (err) {
      const axiosError = err as AxiosError<[ErrorType]>;
      const errorResponse = axiosError.response?.data || {
        success: false,
        error: 'Network error',
        code: 'NETWORK_ERROR'
      };
      setError(errorResponse);
      return null;
    } finally {
      setLoading(false);
    }
  }, []);

  return { [actionName], data, loading, error };
};
```

### C# to TypeScript Type Mapping Examples

When analyzing C# models, use these mappings:

#### C# DTO Example:

```csharp
public class UserResponseDTO
{
    public Guid Id { get; set; }
    public string Email { get; set; }
    public string? FullName { get; set; }
    public DateTime CreatedAt { get; set; }
    public List<RoleDTO> Roles { get; set; }
    public Dictionary<string, object> Metadata { get; set; }
    public decimal AccountBalance { get; set; }
    public bool IsActive { get; set; }
}
```

#### Generated TypeScript Interface:

```typescript
interface UserResponseDTO {
  id: string; // Guid → string
  email: string;
  fullName?: string; // nullable → optional
  createdAt: string; // DateTime → ISO string
  roles: RoleDTO[];
  metadata: Record<string, any>;
  accountBalance: number; // decimal → number
  isActive: boolean;
}
```

### Backend-Specific Patterns

#### For C# ASP.NET Core:

- Controller methods returning `ActionResult<T>` → Extract T for response type
- `[FromBody] CreateUserDto dto` → CreateUserDto becomes request interface
- `[FromQuery] int? page` → Add to query params interface
- `[Route("api/[controller]")]` → Extract base route
- Data annotations → Validation rules in documentation

#### For Node.js/Express:

- `req.body` → Request interface
- `req.query` → Query params interface
- `req.params` → URL params interface
- `res.json(data)` → Response type from data structure

#### For Java Spring Boot:

- `@RequestBody` → Request interface
- `@PathVariable` → URL params
- `@RequestParam` → Query params
- `ResponseEntity<T>` → Extract T for response type

### Model Discovery Patterns

1. **Look for return type references**:
   - C#: `return Ok(new UserResponseDTO {...})`
   - Java: `return ResponseEntity.ok(userDTO)`
   - TypeScript: `return userService.getUserDTO(id)`

2. **Search in related directories**:
   - `/DTOs`, `/Dto`, `/ViewModels`
   - `/Models`, `/Entities`
   - `/Contracts`, `/ApiModels`
   - `/Commands`, `/Queries` (CQRS)

3. **Follow naming conventions**:
   - Request: `Create[Entity]Request`, `[Entity]CreateDTO`
   - Response: `[Entity]Response`, `[Entity]ResponseDTO`
   - Update: `Update[Entity]Request`, `[Entity]UpdateDTO`

### Naming Conventions

- **Function to Endpoint Mapping**:
  - `GetUserById` / `getUserById` → `GET /api/users/:id`
  - `GetUsers` / `getUsers` → `GET /api/users`
  - `CreateUser` / `createUser` → `POST /api/users`
  - `UpdateUser` / `updateUser` → `PUT /api/users/:id`
  - `PatchUser` / `patchUser` → `PATCH /api/users/:id`
  - `DeleteUser` / `deleteUser` → `DELETE /api/users/:id`
  - `GetUserPosts` / `getUserPosts` → `GET /api/users/:id/posts`
  - `SearchUsers` / `searchUsers` → `GET /api/users/search`

- **Type Naming**:
  - Request: `[FunctionName]Request` or `[FunctionName]Params`
  - Response: `[FunctionName]Response`
  - Error: `[FunctionName]Error`
  - DTO: `[ResourceName]DTO`

### Output Requirements

1. All types must be strictly typed (no `any` unless absolutely necessary)
2. Include realistic example data that would pass validation
3. Document all edge cases and optional parameters
4. Generate complete, runnable code that can be copied directly into a project
5. Follow React and TypeScript best practices
6. Include proper error handling patterns
7. Make the API specification self-documenting and clear
8. **For C# backends**: Always search for and analyze related DTOs/Models
9. **Map types correctly**: Use proper TypeScript equivalents for backend types
10. **Preserve validation rules**: Document constraints from data annotations

Now analyze the provided function, search for related models/DTOs, and generate the complete API specification following this format.
