# CBC orders api

## Examples:

Example | Xml
------- | ---
Inbound example | [inbound_example.xml](https://github.com/CBCMoving/cbc_api/blob/master/inbound_example.xml)
Successful response | [successful_response.xml](https://github.com/CBCMoving/cbc_api/blob/master/successful_response.xml)
Failure response | [failure_response.xml](https://github.com/CBCMoving/cbc_api/blob/master/failure_response.xml)

## Documentation:
### Transmission:
Property | Type | Required | Description
-------- | ---- | -------- | -----------
`TransmmissionID` | STRING | ***Yes*** | Unique identifier for each request (used for debug).
`TransmissionLocalDateTime` | STRING | ***No*** | Local DateTime in ATOM format when xml was generated.
`TransmissionUTCDateTime` | STRING | ***Yes*** | DateTime in ATOM format with UTC+0 offset when xml was generated.

### Authenticate (Only POST way api):
Property | Type | Required | Description
-------- | ---- | -------- | -----------
`UserName` | STRING | ***Yes*** | Username given by CBC administrator.
`PasswordHash` | STRING | ***Yes*** | Hash type is sha1. Separator is `|`. Hashed from all following information: *TransmissionUTCDateTime*, *TransmissionID*, *original password given by CBC administrator*. `sha1(TransmissionUTCDateTime|TransmissionID|password)`

### Control numbers:
Property | Type | Required | Description
-------- | ---- | -------- | -----------
`BOL` | STRING | ***Yes*** | Billing order landing (unique order identifier per company). Length max 100 characters.
`PO` | STRING | ***No*** | Purchase Order Number. It is merely informational. Length max 100 characters.
`SO` | STRING | ***No*** | Sales Order Number. It is merely informational. Length max 100 characters.
`RMA` | STRING | ***No*** | Return Merchandise Authorization Number. It is merely informational. Length max 100 characters.
`OfficeID` | INTEGER | ***Yes*** | Office identifier: 1 - `Portland`, 2 - `Kent`, 3 - `Spokane`.
`Deluxe` | BOOLEAN | ***No*** | Is deluxe order. May be `0` or `1`. Default `0`.

### Shipment:
Property | Type | Required | Description
-------- | ---- | -------- | -----------
`ServiceType` | STRING | ***No*** | Type of service. May be `WG`, `T`, `RC`. *WG - White Glove - Inside delivery to any floor with room of choice, unpack placement, debris removal, and up to 30 min of assembly.* *T - Threshold - Delivered to the threshold, or first dry area.* *RC - Room of Choice - Inside delivery to any floor with room of choice.*
`ServiceDescription` | STRING | ***No*** | Description of service. Length max 1000 characters.
`ShipmentType` | STRING | ***Yes*** | Shipment type. May be `Delivery`, `Pickup`, `Transfer`.

### Dates:
Property | Type | Required | Description
-------- | ---- | -------- | -----------
`ReadyDateTime` | STRING | ***No*** | Ready date time. Format: `Y-m-d H:i A` (*2016-01-15 10:10 AM*)
`DeliveryDate` | STRING | ***No*** | Delivery date. Format: `Y-m-d` (*2016-01-15*)

### ShipFrom:
Property | Type | Required | Description
-------- | ---- | -------- | -----------
`FromName` | STRING | On shipment type: `Pickup` | Customer name. Length max 200 characters.
`FromAddress1` | STRING | On shipment type: `Pickup` | Address1. Length max 300 characters.
`FromAddress2` | STRING | ***No*** | Address2. Length max 300 characters.
`FromCity` | STRING | On shipment type: `Pickup` | City name. Length max 150 characters.
`FromState` | STRING | On shipment type: `Pickup` | Short name of state. Two characters in uppercase.
`FromZip` | INTEGER | On shipment type: `Pickup` | Number length 6 symbols (`123456`)
`FromCountry` | STRING | ***No*** | Name of country.
`FromPhone` | STRING | On shipment type: `Pickup` | Primary phone number. Format: `999-999-9999` or `999-999-9999 9999`
`OtherPhone` | STRING | ***No*** | Other phone number. Format: `999-999-9999` or `999-999-9999 9999`
`FromEmail` | STRING | ***No*** | Email address.