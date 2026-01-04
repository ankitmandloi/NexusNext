# OTA Integration Engine

## Overview
The OTA (Online Travel Agency) Integration Engine is a comprehensive system for connecting hotel properties with multiple OTAs using an adapter pattern. It enables automatic rate synchronization, inventory management, and reservation imports while preventing conflicts with manual bookings.

## Architecture

### Adapter Pattern
The system uses the **Adapter Pattern** to abstract OTA-specific integrations:

```
┌──────────────────────┐
│   OTA Manager        │
│   (otaService.ts)    │
└──────────┬───────────┘
           │
           ├──────────────────────────────────┐
           │                                  │
   ┌───────▼─────────┐            ┌──────────▼─────────┐
   │ Adapter Registry │            │  Base OTA Adapter  │
   └───────┬──────────┘            └──────────┬─────────┘
           │                                  │
   ┌───────▼──────────────────────────────────▼─────────┐
   │                                                     │
┌──▼──────────────┐  ┌──────────────────┐  ┌───────────────┐
│ BookingCom      │  │ MakeMyTrip       │  │ Agoda (Stub)  │
│ Adapter         │  │ Adapter          │  │ Adapter       │
└─────────────────┘  └──────────────────┘  └───────────────┘
```

### Components

#### 1. Base OTA Adapter (`BaseOTAAdapter.ts`)
Abstract base class defining the adapter interface:

**Methods:**
- `testConnection()` - Verify OTA credentials
- `pushRates()` - Sync room rates to OTA
- `pushInventory()` - Sync availability to OTA
- `pullReservations()` - Import reservations from OTA
- `getRoomTypes()` - Fetch OTA room types for mapping

**Utilities:**
- `validateCredentials()` - Validate required credentials
- `createResult()` - Create standardized operation result
- `delay()` - Simulate API latency for development

#### 2. OTA Adapters
Each OTA has a dedicated adapter implementing `IOTAAdapter`:

**Booking.com Adapter** (`BookingComAdapter.ts`)
- Credentials: `hotelId`, `apiKey`
- Simulates Booking.com API v2.1
- 95% success rate for rate sync
- 98% success rate for inventory sync

**MakeMyTrip Adapter** (`MakeMyTripAdapter.ts`)
- Credentials: `propertyId`, `username`, `password`
- Simulates MakeMyTrip API v1.5
- 96% success rate for rate sync
- 97% success rate for inventory sync

**Agoda Adapter** (`AgodaAdapter.ts`)
- **STUB** implementation for future integration
- Placeholder methods showing adapter pattern

#### 3. OTA Adapter Registry (`adapters/index.ts`)
Central registry managing all adapters:

```typescript
OTAAdapterRegistry.initialize();
const adapter = OTAAdapterRegistry.getAdapter('BOOKING_COM');
```

#### 4. OTA Service (`otaService.ts`)
Main service coordinating all OTA operations:
- Channel management (CRUD)
- Connection testing
- Rate & inventory synchronization
- Reservation imports
- Sync logging

## Backend API

### Endpoints

#### Channel Management
```
GET    /api/ota/channels              - List all channels
GET    /api/ota/channels/:id          - Get specific channel
POST   /api/ota/channels              - Create new channel
PUT    /api/ota/channels/:id          - Update channel
DELETE /api/ota/channels/:id          - Delete channel
POST   /api/ota/channels/:id/test     - Test connection
```

#### Synchronization
```
POST /api/ota/channels/:id/sync-rates      - Sync rates
POST /api/ota/channels/:id/sync-inventory  - Sync inventory
POST /api/ota/channels/:id/import-reservation - Import reservation
```

#### Logs & History
```
GET /api/ota/sync-logs                 - Get sync logs
GET /api/ota/imported-reservations     - Get imported reservations
```

### Request Examples

**Create Channel:**
```json
POST /api/ota/channels
{
  "provider": "BOOKING_COM",
  "credentials": {
    "hotelId": "hotel123",
    "apiKey": "your-api-key"
  },
  "mappings": [
    {
      "roomTypeId": "rt_001",
      "otaRoomTypeId": "booking-com-deluxe-001",
      "otaRoomTypeName": "Deluxe Room"
    }
  ],
  "syncSettings": {
    "autoSyncRates": true,
    "autoSyncInventory": true,
    "autoImportReservations": true,
    "syncIntervalMinutes": 60
  }
}
```

**Sync Rates:**
```json
POST /api/ota/channels/:id/sync-rates
{
  "from": "2026-01-15",
  "to": "2026-02-15"
}
```

## Persistence Layer

### Data Files

**otaChannels.json** - Channel configurations
```json
{
  "id": "channel_001",
  "hotelId": "hotel_001",
  "hotelCode": "HTL001",
  "provider": "BOOKING_COM",
  "status": "ACTIVE",
  "credentials": { "hotelId": "...", "apiKey": "..." },
  "mappings": [...],
  "syncSettings": {...},
  "lastSyncAt": "2026-01-03T10:00:00Z",
  "createdAt": "2026-01-01T00:00:00Z",
  "updatedAt": "2026-01-03T10:00:00Z"
}
```

**otaReservationImports.json** - Imported reservation records
```json
{
  "id": "import_001",
  "hotelId": "hotel_001",
  "provider": "BOOKING_COM",
  "otaConfirmationCode": "BDC123456",
  "importedReservationId": "res_001",
  "status": "IMPORTED",
  "importedAt": "2026-01-03T08:00:00Z"
}
```

**otaSyncLogs.json** - Synchronization logs
```json
{
  "id": "log_001",
  "hotelId": "hotel_001",
  "channelId": "channel_001",
  "provider": "BOOKING_COM",
  "syncType": "RATES",
  "status": "SUCCESS",
  "itemsProcessed": 90,
  "itemsFailed": 0,
  "syncedAt": "2026-01-03T10:00:00Z"
}
```

## Frontend

### Services

**otaApi.ts** - API client for OTA operations
```typescript
import { otaApi } from '../services/otaApi';

// Fetch channels
const channels = await otaApi.fetchChannels();

// Test connection
const result = await otaApi.testConnection(channelId);

// Sync rates
const syncLog = await otaApi.syncRates(channelId, {
  from: '2026-01-15',
  to: '2026-02-15'
});
```

### State Management

**otaStore.ts** - Zustand store
```typescript
import { useOTAStore } from '../stores/otaStore';

const {
  channels,
  fetchChannels,
  createChannel,
  syncRates,
  syncInventory
} = useOTAStore();
```

### UI Pages

1. **OTA Configuration Page** - Existing page for channel management
2. **OTA Sync Dashboard** - Monitor sync status and logs
3. **OTA Reservation Imports** - View and manage imported reservations

## Key Features

### 1. Room Type Mapping
Maps internal room types to OTA-specific room types:

```typescript
{
  "roomTypeId": "rt_deluxe_001",        // Internal room type
  "otaRoomTypeId": "booking-com-deluxe-001", // OTA room type
  "otaRoomTypeName": "Deluxe Room"
}
```

### 2. Conflict Detection
- Checks for duplicate OTA confirmation codes
- Prevents overwriting manual reservations
- Tags all OTA reservations with source

### 3. Auto-Synchronization
Channels can be configured for automatic sync:

```typescript
{
  "autoSyncRates": true,
  "autoSyncInventory": true,
  "autoImportReservations": true,
  "syncIntervalMinutes": 60  // Sync every hour
}
```

### 4. Error Handling
- Adapter-level error handling
- Per-item error tracking
- Partial success reporting
- Error logging in sync logs

## Data Flow

### Rate Sync Flow
```
1. User triggers rate sync with date range
2. OTA Service fetches channel config
3. Get appropriate adapter from registry
4. Fetch room types and current rates
5. Prepare rate data with mappings
6. Adapter pushes rates to OTA API
7. Log sync results
8. Update channel last sync time
```

### Reservation Import Flow
```
1. Adapter pulls reservations from OTA
2. Check for duplicate confirmation codes
3. Create/find guest record
4. Find room type mapping
5. Create reservation with "OTA" source
6. Tag with otaReference
7. Log import status
```

## Configuration Example

```typescript
const channel = {
  provider: 'BOOKING_COM',
  credentials: {
    hotelId: 'hotel_123',
    apiKey: 'bdc_api_key_12345'
  },
  mappings: [
    {
      roomTypeId: 'rt_standard',
      otaRoomTypeId: 'bdc-standard-001',
      otaRoomTypeName: 'Standard Room'
    },
    {
      roomTypeId: 'rt_deluxe',
      otaRoomTypeId: 'bdc-deluxe-001',
      otaRoomTypeName: 'Deluxe Room'
    }
  ],
  syncSettings: {
    autoSyncRates: true,
    autoSyncInventory: true,
    autoImportReservations: true,
    syncIntervalMinutes: 60
  }
};
```

## Development vs Production

### Current State (Development)
- **Simulated API Calls**: All adapter methods simulate OTA API responses
- **Mock Data**: Returns realistic mock reservations
- **Configurable Success Rates**: Each adapter has configurable failure rates
- **Delay Simulation**: Simulates network latency

### Production Migration
To integrate with real OTA APIs:

1. **Booking.com**:
   ```typescript
   async pushRates(credentials, mappings, rates) {
     const response = await fetch(`${credentials.endpoint}/rates`, {
       method: 'POST',
       headers: {
         'Authorization': `Bearer ${credentials.apiKey}`,
         'Content-Type': 'application/json'
       },
       body: JSON.stringify(rates)
     });
     return await response.json();
   }
   ```

2. **MakeMyTrip**:
   ```typescript
   async testConnection(credentials) {
     const response = await fetch(`${credentials.endpoint}/auth`, {
       method: 'POST',
       body: JSON.stringify({
         username: credentials.username,
         password: credentials.password
       })
     });
     return response.ok;
   }
   ```

## Adding New OTA

To add a new OTA (e.g., Agoda):

1. **Create Adapter**:
   ```typescript
   // AgodaAdapter.ts
   export class AgodaAdapter extends BaseOTAAdapter {
     provider: OTAProvider = 'AIRBNB'; // Update type if needed
     
     async testConnection(credentials) {
       // Implement Agoda API call
     }
     
     async pushRates(credentials, mappings, rates) {
       // Implement Agoda rate push
     }
     
     // ... implement other methods
   }
   ```

2. **Register Adapter**:
   ```typescript
   // adapters/index.ts
   OTAAdapterRegistry.adapters.set('AGODA', new AgodaAdapter());
   ```

3. **Update Types** (if new provider):
   ```typescript
   // domain.ts
   export type OTAProvider = 
     | "BOOKING_COM" 
     | "MAKEMYTRIP" 
     | "AGODA"  // Add new provider
     | ...;
   ```

## Security

### Credentials Storage
- Credentials stored in JSON files (development)
- Should be encrypted in production
- Never log sensitive credentials
- Use environment variables for API keys

### API Access
- All endpoints require authentication
- User must be logged in
- Credentials validated per-hotel

## Testing

### Manual Testing
```bash
# Start backend
cd backend
npm run dev

# Test connection
POST http://localhost:5000/api/ota/channels/:id/test

# Sync rates
POST http://localhost:5000/api/ota/channels/:id/sync-rates
Body: { "from": "2026-01-15", "to": "2026-02-15" }
```

### Adapter Testing
```typescript
import { BookingComAdapter } from './adapters/BookingComAdapter';

const adapter = new BookingComAdapter();
const result = await adapter.testConnection({
  hotelId: 'test123',
  apiKey: 'test-key'
});

console.log(result); // { success: true, message: '...' }
```

## Performance Considerations

- **Batch Operations**: Sync multiple dates/rooms in single API call
- **Rate Limiting**: Respect OTA API rate limits
- **Async Processing**: Use background jobs for large syncs
- **Caching**: Cache room type mappings
- **Retry Logic**: Implement exponential backoff for failures

## Monitoring

### Sync Logs
Monitor sync operations:
```typescript
const logs = await otaApi.fetchSyncLogs({
  channelId: 'channel_001',
  syncType: 'RATES',
  limit: 50
});
```

### Metrics to Track
- Sync success rate
- Average sync duration
- Failed items count
- API response times
- Duplicate reservation attempts

## Troubleshooting

### Connection Failures
1. Verify credentials are correct
2. Check OTA API endpoint is accessible
3. Review firewall/network settings
4. Check API key permissions

### Sync Failures
1. Check room type mappings
2. Verify date range is valid
3. Review error messages in sync logs
4. Check OTA API rate limits

### Duplicate Reservations
1. Review otaConfirmationCode uniqueness
2. Check import status before reimporting
3. Verify conflict detection logic

## Future Enhancements

1. **Scheduled Sync**: Cron jobs for automatic synchronization
2. **Webhook Support**: Real-time reservation notifications
3. **Multi-property**: Sync across property groups
4. **Advanced Mapping**: Dynamic rate plans and restrictions
5. **Analytics Dashboard**: Sync performance metrics
6. **Bulk Operations**: Sync all channels with one action
7. **Conflict Resolution UI**: Manual conflict resolution tools
8. **API Versioning**: Support multiple OTA API versions

---

**Files Created:**
- `backend/src/services/adapters/BaseOTAAdapter.ts`
- `backend/src/services/adapters/BookingComAdapter.ts`
- `backend/src/services/adapters/MakeMyTripAdapter.ts`
- `backend/src/services/adapters/AgodaAdapter.ts`
- `backend/src/services/adapters/index.ts`
- `src/services/otaApi.ts`
- `src/stores/otaStore.ts`

**Files Enhanced:**
- `backend/src/services/otaService.ts` - Added adapter integration
- `src/services/index.ts` - Export otaApi

**Implementation Complete! ✨**
