# Database Schema

## Azure Cosmos DB Schema for OrderEvent

For the `OrderEvent` data model, we will use an Azure Cosmos DB container (which is analogous to a collection in other NoSQL databases).

**Container Name:** `OrderEvents`

**Partition Key:** `/userId`
*   **Rationale:** Partitioning by `userId` will optimize queries that retrieve events for a specific user, which is a common access pattern (e.g., displaying an order history for a particular shop employee). This aligns with FR8 and FR9.

**Indexing Policy:** Default (automatic indexing for all properties). Custom indexing will be applied as needed to optimize specific query patterns, especially for `type` and `createdAt` for filtering and sorting.

**Example OrderEvent Document Structure:**

```json
{
  "id": "a1b2c3d4-e5f6-7890-1234-567890abcdef",
  "userId": "user123",
  "type": "Purchase",
  "description": "User purchased item XYZ from location A",
  "eventPayload": {
    "productId": "P12345",
    "quantity": 1,
    "price": 99.99,
    "locationId": "LOC001"
  },
  "createdAt": "2026-01-30T14:30:00Z",
  // Cosmos DB internal properties (e.g., _ts, _rid, _self, _etag) are automatically added
}
```
