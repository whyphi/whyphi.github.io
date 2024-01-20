---
title: Applicants 
type: docs
next: docs/zap/listings
prev: docs/zap/
---

WORK IN PROGRESS

## `GET` /applicant/{id}

Retrieves an applicant with given {id}.

<!-- 
#### Parameters

None

#### Returns

Data associated with the given <applicant_id>.


#### Example

```properties
# GET /api/applicants/:applicant_id

curl https://<api_link>/api/applicants/<applicant_id>
```

```json
// THIS IS NOT THE CORRECT RESPONSE
{
  "applicant_id": "123456",
  "first_name": "John",
  "last_name": "Doe",
  "email": "johndoe@example.com",
  "phone": "555-555-5555",
  "address": {
    "street": "123 Main Street",
    "city": "Anytown",
    "state": "CA",
    "zip_code": "12345"
  },
  "resume": "https://example.com/resumes/johndoe_resume.pdf",
}

```
--- -->

## `GET` /applicants

Gets all applicants in the database.

<!-- #### Parameters



#### Returns



#### Example

```properties
# GET /api/applicants/:applicant_id

curl https://<api_link>/api/applicants/<applicant_id>
```

```json
// THIS IS NOT THE CORRECT RESPONSE
{
  "applicant_id": "123456",
  "first_name": "John",
  "last_name": "Doe",
  "email": "johndoe@example.com",
  "phone": "555-555-5555",
  "address": {
    "street": "123 Main Street",
    "city": "Anytown",
    "state": "CA",
    "zip_code": "12345"
  },
  "resume": "https://example.com/resumes/johndoe_resume.pdf",
}

```
--- -->

## `GET` /applicants/{listing_id}

Retrieves all applicant withing a given `listing_id`

<!-- #### Parameters

None

#### Returns

Data associated with the given <applicant_id>.


#### Example

```properties
# GET /api/applicants/:applicant_id

curl https://<api_link>/api/applicants/<applicant_id>
```

```json
// THIS IS NOT THE CORRECT RESPONSE
{
  "applicant_id": "123456",
  "first_name": "John",
  "last_name": "Doe",
  "email": "johndoe@example.com",
  "phone": "555-555-5555",
  "address": {
    "street": "123 Main Street",
    "city": "Anytown",
    "state": "CA",
    "zip_code": "12345"
  },
  "resume": "https://example.com/resumes/johndoe_resume.pdf",
}

```
--- -->