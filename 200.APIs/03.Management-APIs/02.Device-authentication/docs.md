---
title: Device authentication
taxonomy:
    category: docs
api: true
template: docs
---

<a name="overview"></a>
## Overview
An API for device authentication handling.


### Version information
*Version* : 1


### URI scheme
*Host* : mender-device-auth:8080  
*BasePath* : /api/management/v1/devauth/  
*Schemes* : HTTP




<a name="paths"></a>
## Paths
- [GET/devices](#devices-get)
- [GET/devices/count](#devices-count-get)
- [GET/devices/{id}](#devices-id-get)
- [DELETE/devices/{id}](#devices-id-delete)
- [PUT/devices/{id}/auth/{aid}/status](#devices-id-auth-aid-status-put)
- [GET/limits/max_devices](#limits-max_devices-get)
- [DELETE/tokens/{id}](#tokens-id-delete)


___
<a name="devices-get"></a>
### Get a list of tenant's devices.
```
GET /devices
```


#### Description
Provides a list of tenant's devices, with optional device status filter.


#### Parameters

|Type|Name|Description|Schema|Default|
|---|---|---|---|---|
|**Header**|**Authorization**  <br>*required*|Contains the JWT token issued by the User Administration and Authentication Service.|string (Bearer [token])| |
|**Query**|**page**  <br>*optional*|Results page number|number (integer)|`1`|
|**Query**|**per_page**  <br>*optional*|Number of results per page|number (integer)|`20`|


#### Responses

|HTTP Code|Description|Schema|
|---|---|---|
|**200**|An array of devices.  <br>**Headers** :   <br>`Link` (string) : Standard header, we support 'first', 'next', and 'prev'.|< [Device](#device) > array|
|**400**|Missing/malformed request params.|[Error](#error)|
|**500**|Unexpected error|[Error](#error)|
___

<a name="devices-count-get"></a>
### Get a count of devices, optionally filtered by status.
```
GET /devices/count
```


#### Description
Provides a list of devices, optionally filtered by status.


#### Parameters

|Type|Name|Description|Schema|
|---|---|---|---|
|**Header**|**Authorization**  <br>*required*|Contains the JWT token issued by the User Administration and Authentication Service.|string (Bearer [token])|
|**Query**|**status**  <br>*optional*|Device status filter, one of 'pending', 'accepted', 'rejected'. Default is 'all devices'.|string|


#### Responses

|HTTP Code|Description|Schema|
|---|---|---|
|**200**|Device count.|[Count](#count)|
|**400**|Missing/malformed request params.|[Error](#error)|
|**500**|Unexpected error|[Error](#error)|


#### Example HTTP response

##### Response 200
```json
{
  "count" : "42"
}
```
___

<a name="devices-id-get"></a>
### Get a particular device.
```
GET /devices/{id}
```


#### Parameters

|Type|Name|Description|Schema|
|---|---|---|---|
|**Header**|**Authorization**  <br>*required*|Contains the JWT token issued by the User Administration and Authentication Service.|string (Bearer [token])|
|**Path**|**id**  <br>*required*|Device identifier|string|


#### Responses

|HTTP Code|Description|Schema|
|---|---|---|
|**200**|Device found.|[Device](#device)|
|**404**|Device not found.|[Error](#error)|
|**500**|Unexpected error|[Error](#error)|
___

<a name="devices-id-delete"></a>
### Decommission device
```
DELETE /devices/{id}
```


#### Parameters

|Type|Name|Description|Schema|
|---|---|---|---|
|**Header**|**Authorization**  <br>*required*|Contains the JWT token issued by the User Administration and Authentication Service.|string (Bearer [token])|
|**Path**|**id**  <br>*required*|Device identifier.|string|


#### Responses

|HTTP Code|Description|Schema|
|---|---|---|
|**204**|Device decommissioned.|No Content|
|**404**|Device not found|[Error](#error)|
|**500**|Internal server error.|[Error](#error)|
___

<a name="devices-id-auth-aid-status-put"></a>
### Update the device authentication set status
```
PUT /devices/{id}/auth/{aid}/status
```


#### Description
Sets the status of a authentication data set of selected value.

All possible transitions are valid.


#### Parameters

|Type|Name|Description|Schema|
|---|---|---|---|
|**Header**|**Authorization**  <br>*required*|Contains the JWT token issued by the User Administration and Authentication Service.|string (Bearer [token])|
|**Path**|**aid**  <br>*required*|Authentication data set identifier.|string|
|**Path**|**id**  <br>*required*|Device identifier.|string|
|**Body**|**status**  <br>*required*|New status.|[Status](#status)|


#### Responses

|HTTP Code|Description|Schema|
|---|---|---|
|**204**|The device authentication data set status was successfully updated.|No Content|
|**400**|Bad request.|[Error](#error)|
|**404**|The device was not found.|[Error](#error)|
|**422**|Request cannot be fulfilled e.g. due to exceeded limit on maximum accepted devices (see error message).|[Error](#error)|
|**500**|Internal server error.|[Error](#error)|


#### Example HTTP request

##### Request body
```json
{
  "status" : "accepted"
}
```
___

<a name="limits-max_devices-get"></a>
### Obtain limit of accepted devices.
```
GET /limits/max_devices
```


#### Parameters

|Type|Name|Description|Schema|
|---|---|---|---|
|**Header**|**Authorization**  <br>*required*|Contains the JWT token issued by the User Administration and<br>Authentication Service.|string (Bearer [token])|


#### Responses

|HTTP Code|Description|Schema|
|---|---|---|
|**200**|Usage statistics and limits.|[Limit](#limit)|
|**500**|Internal server error.|[Error](#error)|


#### Example HTTP response

##### Response 200
```json
{
  "limit" : 123
}
```
___

<a name="tokens-id-delete"></a>
### Delete device token
```
DELETE /tokens/{id}
```


#### Description
Deletes the token, effectively revoking it. The device must
apply for a new one with a new authentication request.
The token 'id' corresponds to the standard 'jti' claim.


#### Parameters

|Type|Name|Description|Schema|
|---|---|---|---|
|**Path**|**id**  <br>*required*|Unique token identifier('jti').|string|


#### Responses

|HTTP Code|Description|Schema|
|---|---|---|
|**204**|The token was successfully deleted.|No Content|
|**404**|The token was not found.|[Error](#error)|
|**500**|Internal server error.|[Error](#error)|




<a name="definitions"></a>
## Definitions

<a name="authset"></a>
### AuthSet
Authentication data set


|Name|Description|Schema|
|---|---|---|
|**id**  <br>*optional*|Authentication data set ID.|string|
|**id_data**  <br>*optional*|Vendor-specific JSON representation of device identity, encrypted with the tenant's public key.<br>In reference implementation, it is a JSON structure with vendor-selected fields, such as MACs, serial numbers, etc.|string|
|**pubkey**  <br>*optional*|The device's public key, generated by the device or pre-provisioned by the vendor.|string|
|**status**  <br>*optional*| |enum (pending, accepted, rejected)|
|**ts**  <br>*optional*|Created timestamp|string (datetime)|


<a name="count"></a>
### Count
Counter type


|Name|Description|Schema|
|---|---|---|
|**count**  <br>*optional*|The count of requested items.|integer|


<a name="device"></a>
### Device

|Name|Description|Schema|
|---|---|---|
|**auth_sets**  <br>*optional*| |< [AuthSet](#authset) > array|
|**created_ts**  <br>*optional*|Created timestamp|string (datetime)|
|**id**  <br>*optional*|Mender assigned Device ID.|string|
|**id_data**  <br>*optional*|Vendor-specific JSON representation of device identity, encrypted with the tenant's public key.<br>In reference implementation, it is a JSON structure with vendor-selected fields, such as MACs, serial numbers, etc.|string|
|**updated_ts**  <br>*optional*|Updated timestamp|string (datetime)|


<a name="error"></a>
### Error
Error descriptor


|Name|Description|Schema|
|---|---|---|
|**error**  <br>*optional*|Description of the error|string|


<a name="limit"></a>
### Limit
Limit definition


|Name|Schema|
|---|---|
|**limit**  <br>*required*|integer|


<a name="status"></a>
### Status
Admission status of the device.


|Name|Schema|
|---|---|
|**status**  <br>*required*|enum (pending, accepted, rejected)|




