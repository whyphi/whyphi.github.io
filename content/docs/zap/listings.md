---
title: Listings 
type: docs
prev: docs/zap/applicant
---

## `/submit`

### `POST`

Submits an application to a listing.

#### Authentication

None

#### Parameters

- JSON body containing applicant data

#### Returns

Response indicating the success of the application submission.

#### Example

```properties
# POST /api/submit

curl -X POST https://<api_link>/api/submit -H "Content-Type: application/json" -d '{"applicant_data": {...}}'

## `/create`

### `POST`

#### Authentication

- 


#### Parameters


#### Returns



#### Example



## `GET` /listings

#### Authentication

- 


#### Parameters


#### Returns



#### Example

## `GET` /listings/{id}

#### Authentication

- 


#### Parameters


#### Returns



#### Example

## `DELETE` /listings/{id}

#### Authentication

- 


#### Parameters


#### Returns



#### Example

## `PATCH` /listings/{id}/update-field

#### Authentication

- 


#### Parameters


#### Returns



#### Example

## `PATCH` /listings/{id}/toggle/visibility

#### Authentication

- 


#### Parameters


#### Returns



#### Example