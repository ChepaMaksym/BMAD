# Data Models

## OrderEvent

**Purpose:** Represents a single event related to user activity within the system, capturing essential details for tracking and analysis. This model serves as the single source of truth for all user-generated events.

**Key Attributes:**
-   `id`: `string` (GUID) - A unique identifier for the event.
-   `userId`: `string` - The identifier of the user who generated the event.
-   `type`: `string` - The type of event. Expected values: "PageView", "Click", "Purchase".
-   `description`: `string` - A detailed description of the event.
-   `eventPayload`: `any` (JSON) - A flexible field to store additional, event-specific details.
-   `createdAt`: `string` (ISO 8601 DateTime) - The date and time the event was created.

**TypeScript Interface:**

```typescript
type EventType = "PageView" | "Click" | "Purchase";

interface OrderEvent {
  id: string; // GUID
  userId: string;
  type: EventType;
  description: string;
  eventPayload: any; // Consider a more specific type if schema is known
  createdAt: string; // ISO 8601 DateTime
}
```

## Data Modeling for Other Entities

While `OrderEvent` is the primary model, the system will eventually manage other entities. These will follow similar data modeling principles, leveraging Azure Cosmos DB for persistence.

**General Approach:**
-   **Identify Entities:** Each significant business entity (e.g., `User`, `Product`, `Session`) will be identified based on functional requirements.
-   **Document Structure:** For each entity, a clear JSON document structure will be defined, specifying key attributes, their data types, and purpose.
-   **Relationships:** Relationships between entities will be modeled typically through denormalization (embedding related data) where appropriate for read-heavy scenarios, or through explicit references (e.g., `userId` in `OrderEvent`) for more dynamic relationships.
-   **Partitioning:** Each entity's Cosmos DB container will have an optimized partition key defined based on its most frequent query patterns.
-   **Indexing:** Default indexing will be used, with custom indexes added for performance-critical fields as needed.

**Example: User Profile Entity (Conceptual)**

```json
{
  "id": "user123",
  "name": "John Doe",
  "email": "john.doe@example.com",
  "createdDate": "2026-01-01T10:00:00Z",
  "lastLoginDate": "2026-02-02T15:30:00Z",
  "preferences": {
    "theme": "dark",
    "notifications": true
  },
  "addressId": "addr456" // Reference to a separate Address entity if needed
}
```

**TypeScript Interface (Conceptual):**

```typescript
interface UserProfile {
  id: string;
  name: string;
  email: string;
  createdDate: string;
  lastLoginDate: string;
  preferences: {
    theme: string;
    notifications: boolean;
  };
  addressId?: string;
}
```
