# Modbus TCP API Reference

REST API for managing Modbus TCP servers and clients. Designed for MCP tool integration.

**Base URLs:**
- Client Management: `http://10.0.0.34:5001`
- Server Management: `http://10.0.0.34:5002`

---

## Modbus Server API (Port 5002)

### Server Management

| Method | Endpoint | Description |
|--------|----------|-------------|
| GET | `/api/servers` | List all servers |
| POST | `/api/servers` | Create a new server |
| DELETE | `/api/servers/<server_id>` | Delete a server |
| GET | `/api/servers/<server_id>/config` | Get server config |
| PUT | `/api/servers/<server_id>/config` | Update server config |
| POST | `/api/servers/<server_id>/start` | Start server |
| POST | `/api/servers/<server_id>/stop` | Stop server |

#### Create Server
```json
POST /api/servers
{
  "server_id": "ModbusServer1",
  "host": "0.0.0.0",
  "port": 502,
  "unit_id": 1
}
```

### Register Operations

| Method | Endpoint | Description |
|--------|----------|-------------|
| GET | `/api/servers/<server_id>/registers` | Read registers |
| POST | `/api/servers/<server_id>/registers` | Write registers |

#### Read Registers
```bash
GET /api/servers/<server_id>/registers?type=holding&address=0&count=10
```

Query Parameters:
- `type`: `holding`, `input`, `coil`, `discrete`
- `address`: Starting address (0-65535)
- `count`: Number of registers to read

Response:
```json
{
  "success": true,
  "type": "holding",
  "address": 0,
  "count": 10,
  "values": [100, 200, 300, 0, 0, 0, 0, 0, 0, 0]
}
```

#### Write Registers
```json
POST /api/servers/<server_id>/registers
{
  "type": "holding",
  "address": 0,
  "values": [100, 200, 300]
}
```

### Register Types
- `holding` - Holding Registers (read/write, 16-bit)
- `input` - Input Registers (read-only, 16-bit)
- `coil` - Coils (read/write, 1-bit)
- `discrete` - Discrete Inputs (read-only, 1-bit)

### Simulation

| Method | Endpoint | Description |
|--------|----------|-------------|
| GET | `/api/servers/<server_id>/simulation` | Get simulation status |
| POST | `/api/servers/<server_id>/simulation` | Start/configure simulation |
| DELETE | `/api/servers/<server_id>/simulation` | Stop simulation |

### Diagnostics

| Method | Endpoint | Description |
|--------|----------|-------------|
| GET | `/api/servers/<server_id>/diagnostics/packets` | Get packets |
| GET | `/api/servers/<server_id>/diagnostics/statistics` | Get statistics |
| POST | `/api/servers/<server_id>/diagnostics/clear` | Clear packets |
| POST | `/api/servers/<server_id>/diagnostics/clear_statistics` | Clear stats |
| GET | `/api/servers/<server_id>/diagnostics/export` | Export diagnostics |

### Configuration

| Method | Endpoint | Description |
|--------|----------|-------------|
| POST | `/api/config/save` | Save configuration |
| POST | `/api/config/load` | Load configuration |
| GET | `/api/config/list` | List saved configs |
| POST | `/api/config/delete` | Delete a config |

---

## Modbus Client API (Port 5001)

### Client Management

| Method | Endpoint | Description |
|--------|----------|-------------|
| GET | `/api/clients` | List all clients |
| POST | `/api/clients` | Create a new client |
| DELETE | `/api/clients/<client_id>` | Delete a client |
| GET | `/api/clients/<client_id>/status` | Get client status |
| POST | `/api/clients/<client_id>/connect` | Connect to device |
| POST | `/api/clients/<client_id>/disconnect` | Disconnect |

#### Create Client
```json
POST /api/clients
{
  "client_id": "PLC_Main",
  "host": "10.0.0.100",
  "port": 502,
  "unit_id": 1
}
```

### Read/Write Operations

| Method | Endpoint | Description |
|--------|----------|-------------|
| POST | `/api/clients/<client_id>/read` | Read from device |
| POST | `/api/clients/<client_id>/write` | Write to device |

#### Read Registers
```json
POST /api/clients/<client_id>/read
{
  "type": "holding",
  "address": 0,
  "count": 10
}
```

Response:
```json
{
  "success": true,
  "values": [100, 200, 300, 0, 0, 0, 0, 0, 0, 0]
}
```

#### Write Registers
```json
POST /api/clients/<client_id>/write
{
  "type": "holding",
  "address": 0,
  "values": [100, 200, 300]
}
```

### Simulations (Auto-write)

| Method | Endpoint | Description |
|--------|----------|-------------|
| GET | `/api/clients/<client_id>/simulations` | List simulations |
| POST | `/api/clients/<client_id>/simulations/add` | Add simulation |
| POST | `/api/clients/<client_id>/simulations/remove` | Remove simulation |
| POST | `/api/clients/<client_id>/simulations/toggle` | Toggle on/off |

### Monitors (Auto-read)

| Method | Endpoint | Description |
|--------|----------|-------------|
| GET | `/api/clients/<client_id>/monitors` | List monitors |
| POST | `/api/clients/<client_id>/monitors/add` | Add monitor |
| POST | `/api/clients/<client_id>/monitors/remove` | Remove monitor |
| POST | `/api/clients/<client_id>/monitors/toggle` | Toggle on/off |

### Diagnostics

| Method | Endpoint | Description |
|--------|----------|-------------|
| GET | `/api/clients/<client_id>/diagnostics/packets` | Get packets |
| GET | `/api/clients/<client_id>/diagnostics/statistics` | Get statistics |
| POST | `/api/clients/<client_id>/diagnostics/clear` | Clear packets |
| POST | `/api/clients/<client_id>/diagnostics/clear_statistics` | Clear stats |
| GET | `/api/clients/<client_id>/diagnostics/export` | Export diagnostics |

### Configuration

| Method | Endpoint | Description |
|--------|----------|-------------|
| POST | `/api/clients/<client_id>/config/save` | Save config |
| POST | `/api/clients/<client_id>/config/load` | Load config |
| GET | `/api/clients/<client_id>/config/list` | List configs |
| POST | `/api/clients/<client_id>/config/delete` | Delete config |

---

## MCP Tool Mapping

Suggested MCP tools for Modbus operations:

| MCP Tool | API Endpoint | Description |
|----------|--------------|-------------|
| `modbus_list_servers` | GET /api/servers | List configured servers |
| `modbus_list_clients` | GET /api/clients | List configured clients |
| `modbus_read_registers` | POST /api/clients/{id}/read | Read from device |
| `modbus_write_registers` | POST /api/clients/{id}/write | Write to device |
| `modbus_read_coils` | POST /api/clients/{id}/read | Read coils |
| `modbus_write_coils` | POST /api/clients/{id}/write | Write coils |
| `modbus_connect` | POST /api/clients/{id}/connect | Connect to device |
| `modbus_disconnect` | POST /api/clients/{id}/disconnect | Disconnect |
| `modbus_get_status` | GET /api/clients/{id}/status | Get connection status |

---

## Example: Complete Read/Write Workflow

### 1. List available clients
```bash
curl http://10.0.0.34:5001/api/clients
```

### 2. Connect to device
```bash
curl -X POST http://10.0.0.34:5001/api/clients/PLC_Main/connect
```

### 3. Read holding registers 0-9
```bash
curl -X POST http://10.0.0.34:5001/api/clients/PLC_Main/read \
  -H "Content-Type: application/json" \
  -d '{"type": "holding", "address": 0, "count": 10}'
```

### 4. Write values to holding registers
```bash
curl -X POST http://10.0.0.34:5001/api/clients/PLC_Main/write \
  -H "Content-Type: application/json" \
  -d '{"type": "holding", "address": 0, "values": [100, 200, 300]}'
```

### 5. Disconnect
```bash
curl -X POST http://10.0.0.34:5001/api/clients/PLC_Main/disconnect
```

---

## Function Codes Reference

| Function | Code | Type | Description |
|----------|------|------|-------------|
| Read Coils | 01 | coil | Read multiple coils |
| Read Discrete Inputs | 02 | discrete | Read discrete inputs |
| Read Holding Registers | 03 | holding | Read holding registers |
| Read Input Registers | 04 | input | Read input registers |
| Write Single Coil | 05 | coil | Write one coil |
| Write Single Register | 06 | holding | Write one register |
| Write Multiple Coils | 15 | coil | Write multiple coils |
| Write Multiple Registers | 16 | holding | Write multiple registers |
