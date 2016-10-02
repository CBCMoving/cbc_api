# CBC orders EDI
For interaction with CBC API  provided two ways:
* FTP
* POST

#### FTP:
On this way all interaction with company occurs on company's ftp server. Company provides data from ftp account (username,  password, server, workdir). Collecting and processing xml files will be runned across crontab. All xml files in inbound dir will be processing.
> After successful processing xml will be removed from inbound dir on ftp server. Response xml will be uploaded in outbound dir on ftp server.

Example directory tree of ftp server:
* outgoung
* reponses
* statuses
* errors

> Directories: `outgoing`,` responses`, `statuses` is customizable by ftp provider. But `errors/outgoing`, `errors/responses` is required on ftp tree.

`outgoing` contains outbound xml files for processing in cbc.

`responses` contains xml files with result of processing outgoing files. Filename equals outbound filename.

`statuses` in future...

`errors` contains outgoing files with validation error. Invalid file has be moved from outgoing directory.

#### POST:
On this way company give unique url for requests, username, password. Xml data transmitted in body of request with POST method. Also should be transmitted the header: `Content-Type: application/xml`. Response also have xml format. For POST way is required [Authenticate](https://github.com/CBCMoving/cbc_api#authenticate-only-post-way-api).
### Contents:
* [Transmission](https://github.com/CBCMoving/cbc_api#transmission)
* [Authenticate](https://github.com/CBCMoving/cbc_api#authenticate-only-post-way-api)
* [Control numbers](https://github.com/CBCMoving/cbc_api#control-numbers)
* [Shipment](https://github.com/CBCMoving/cbc_api#shipment)
* [Dates](https://github.com/CBCMoving/cbc_api#dates)
* [Ship From](https://github.com/CBCMoving/cbc_api#shipfrom)
* [Ship To](https://github.com/CBCMoving/cbc_api#shipto)
* [Pieces](https://github.com/CBCMoving/cbc_api#pieces)
    * [Barcodes](https://github.com/CBCMoving/cbc_api#barcodes) 
* [Special services](https://github.com/CBCMoving/cbc_api#specialservices)
* [Summary](https://github.com/CBCMoving/cbc_api#symmary)
* [Examples](https://github.com/CBCMoving/cbc_api#examples)

### Transmission ([see](https://github.com/CBCMoving/cbc_api/blob/master/inbound_example.xml#L3)):
Property | Type | Required | Description
-------- | ---- | -------- | -----------
`TransmmissionID` | STRING | ***Yes*** | Unique identifier for each request (used for debug).
`TransmissionLocalDateTime` | STRING | ***No*** | Local DateTime in ATOM format when xml was generated.
`TransmissionUTCDateTime` | STRING | ***Yes*** | DateTime in ATOM format with UTC+0 offset when xml was generated.

### Authenticate (Only POST way api) ([see](https://github.com/CBCMoving/cbc_api/blob/master/inbound_example.xml#L6)):
Property | Type | Required | Description
-------- | ---- | -------- | -----------
`UserName` | STRING | ***Yes*** | Username given by CBC administrator.
`PasswordHash` | STRING | ***Yes*** | Hash type is sha1. Separator is `|`. Hashed from all following information: *TransmissionUTCDateTime*, *TransmissionID*, *original password given by CBC administrator*. `sha1(TransmissionUTCDateTime|TransmissionID|password)`

### Control numbers ([see](https://github.com/CBCMoving/cbc_api/blob/master/inbound_example.xml#L10)):
Property | Type | Required | Description
-------- | ---- | -------- | -----------
`BOL` | STRING | ***Yes*** | Billing order landing (unique order identifier per company) (Max length ***100*** characters).
`PO` | STRING | ***No*** | Purchase Order Number. It is merely informational (Max length ***100*** characters).
`SO` | STRING | ***No*** | Sales Order Number. It is merely informational (Max length ***100*** characters).
`RMA` | STRING | ***No*** | Return Merchandise Authorization Number. It is merely informational (Max length ***100*** characters).
`OfficeID` | INTEGER | ***Yes*** | Office identifier: 1 - `Portland`, 2 - `Kent`, 3 - `Spokane`.
`Deluxe` | BOOLEAN | ***No*** | Is deluxe order. May be `0` or `1`. Default `0`.

### Shipment ([see](https://github.com/CBCMoving/cbc_api/blob/master/inbound_example.xml#L18)):
Property | Type | Required | Description
-------- | ---- | -------- | -----------
`ServiceType` | STRING | ***No*** | Type of service. May be `WG`, `T`, `RC`. *WG - White Glove - Inside delivery to any floor with room of choice, unpack placement, debris removal, and up to 30 min of assembly.* *T - Threshold - Delivered to the threshold, or first dry area.* *RC - Room of Choice - Inside delivery to any floor with room of choice.*
`ServiceDescription` | STRING | ***No*** | Description of service (Max length ***1000*** characters).
`ShipmentType` | STRING | ***Yes*** | Shipment type. May be `Delivery`, `Pickup`, `Transfer`.

### Dates ([see](https://github.com/CBCMoving/cbc_api/blob/master/inbound_example.xml#L21)):
Property | Type | Required | Description
-------- | ---- | -------- | -----------
`ReadyDateTime` | STRING | ***No*** | Ready date time. Format: `Y-m-d H:i A` (*2016-01-15 10:10 AM*)
`DeliveryDate` | STRING | ***No*** | Delivery date. Format: `Y-m-d` (*2016-01-15*)

### ShipFrom ([see](https://github.com/CBCMoving/cbc_api/blob/master/inbound_example.xml#L25)):
> If shipment type is `Puckup`, then  `ShipFrom` information will be saved.

Property | Type | Required | Description
-------- | ---- | -------- | -----------
`FromName` | STRING | On shipment type: `Pickup` | Customer name (Max length ***200*** characters).
`FromAddress1` | STRING | On shipment type: `Pickup` | Address1 (Max length ***300*** characters).
`FromAddress2` | STRING | ***No*** | Address2 (Max length ***300*** characters).
`FromCity` | STRING | On shipment type: `Pickup` | City name (Max length ***150*** characters).
`FromState` | STRING | On shipment type: `Pickup` | Short name of state. Two characters in uppercase.
`FromZip` | INTEGER | On shipment type: `Pickup` | Number length ***6*** symbols (`123456`)
`FromCountry` | STRING | ***No*** | Name of country.
`FromPhone` | STRING | On shipment type: `Pickup` | Primary phone number. Format: `999-999-9999` or `999-999-9999 9999` (Max length ***20*** characters).
`FromPhoneOther` | STRING | ***No*** | Other phone number. Format: `999-999-9999` or `999-999-9999 9999` (Max length ***20*** characters).
`FromEmail` | STRING | ***No*** | Email address (Max length ***200*** characters).

### ShipTo ([see](https://github.com/CBCMoving/cbc_api/blob/master/inbound_example.xml#L37)):
> If shipment type is `Delivery` or `Transfer`, then  `ShipTo` information will be saved.

Property | Type | Required | Description
-------- | ---- | -------- | -----------
`ToName` | STRING | On shipment type: `Delivery` or `Transfer` | Customer name (Max length ***200*** characters).
`ToAddress1` | STRING | On shipment type: `Delivery` or `Transfer` | Address1 (Max length ***300*** characters).
`ToAddress2` | STRING | ***No*** | Address2 (Length max ***300*** characters).
`ToCity` | STRING | On shipment type: `Delivery` or `Transfer` | City name (Max length ***150*** characters).
`ToState` | STRING | On shipment type: `Delivery` or `Transfer` | Short name of state. Two characters in uppercase.
`ToZip` | INTEGER | On shipment type: `Delivery` or `Transfer` | Zip number length ***6*** symbols (`123456`)
`ToCountry` | STRING | ***No*** | Name of country.
`ToPhone` | STRING | On shipment type: `Delivery` or `Transfer` | Primary phone number. Format: `999-999-9999` or `999-999-9999 9999` (Max length ***20*** characters).
`ToPhoneOther` | STRING | ***No*** | Other phone number. Format: `999-999-9999` or `999-999-9999 9999` (Max length ***20*** characters).
`ToEmail` | STRING | ***No*** | Email address (Max length ***200*** characters).

### Pieces ([see](https://github.com/CBCMoving/cbc_api/blob/master/inbound_example.xml#L62)):
> Numbers of pieces may be from 1 to 300. if you upgrade an existing order in which there are missing pieces in xml file, this pieces will be deleted from order.

Property | Type | Required | Description
-------- | ---- | -------- | -----------
`PieceID` | STRING | ***Yes*** | Identifier of piece is unique per company (Max length ***50*** characters). 
`PieceQuantity` | INTEGER | ***Yes*** | Quantity of piece.
`PieceCartons` | INTEGER | ***Yes*** | Quantity of cartons.
`PieceWeight` | INTEGER | ***Yes*** | The width, in inches, of a a piece represented by this line.
`PieceLength` | INTEGER | ***Yes*** | The length, in inches, of a a piece represented by this line.
`PieceHeight` | INTEGER | ***Yes*** | The height, in inches, of a a piece represented by this line.
`PieceCommodity` | STRING | ***No*** | Commodity type (Max length ***300*** characters).
`PieceModel` | STRING | ***No*** | Model of commodity (Max length ***300*** characters)
`PieceDescription` | STRING | ***No*** | Description of piece (Max length ***1000*** characters).
`Barcodes` | [] | ***No*** | List of barcodes. [See below &#8595;](https://github.com/CBCMoving/cbc_api#barcodes-see)

##### Barcodes ([see](https://github.com/CBCMoving/cbc_api/blob/master/inbound_example.xml#L74)):
> Barcodes are not mandatory.

Property | Type | Required | Description
-------- | ---- | -------- | -----------
`BarcodeType` | STRING | ***Yes*** | Type of barcode (Max length ***50*** characters). 
`BarcodeNumber` | STRING | ***Yes*** | Barcode number (Max length ***50*** characters). 

### SpecialServices ([see](https://github.com/CBCMoving/cbc_api/blob/master/inbound_example.xml#L108)):
> In `SpecialService` is sent additional information of order. Such as: **additional services, order notes, special instructions, etc**.

Property | Type | Required | Description
-------- | ---- | -------- | -----------
`ServiceTypeName` | STRING | ***Yes*** | Name of service type (Max length ***300*** characters).
`ServiceTypeDescription` | STRING | ***No*** | Description of service type (Max length ***4000*** characters).

### Symmary ([see](https://github.com/CBCMoving/cbc_api/blob/master/inbound_example.xml#L122)):
> In summary transmitted overall information of all pieces.

Property | Type | Required | Description
-------- | ---- | -------- | -----------
`TotalPieces` | INTEGER | ***Yes*** | Number of pieces.
`TotalCartons` | INTEGER | ***Yes*** | Number of cartons.
`TotalWeight` | INTEGER | ***Yes*** | Overall weight.


## Examples:

Example | Xml
------- | ---
Inbound example | [inbound_example.xml](https://github.com/CBCMoving/cbc_api/blob/master/inbound_example.xml)
Successful response | [successful_response.xml](https://github.com/CBCMoving/cbc_api/blob/master/successful_response.xml)
Failure response | [failure_response.xml](https://github.com/CBCMoving/cbc_api/blob/master/failure_response.xml)
