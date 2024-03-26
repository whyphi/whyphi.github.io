---
title: Applicants 
type: docs
next: docs/zap/listings
prev: docs/zap/
---

WORK IN PROGRESS

## `/applicant/{id}`

### `GET`

Retrieves an applicant with given `id`.


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
{
    "website": "https://www.example.com",
    "listingId": "c29e03c4-4e7d-497e-a5a9-c05c419ba8eb",
    "colleges": {
        "COM": false,
        "QST": false,
        "CDS": false,
        "CAS": false,
        "Wheelock": false,
        "Sargent": false,
        "Pardee": false,
        "SHA": false,
        "CGS": true,
        "ENG": false,
        "CFA": false,
        "Other": false
    },
    "responses": [
        {
            "question": "Question 1",
            "response": "Response 1"
        },
        {
            "question": "Question 2",
            "response": "Response 2"
        }
    ],
    "lastName": "Smith",
    "linkedin": "https://www.linkedin.com/in/john-smith",
    "dateApplied": "2024-03-25T12:00:00-05:00",
    "email": "john.smith@example.com",
    "firstName": "John",
    "applicantId": "12345678-1234-5678-1234-567812345678",
    "minor": "Spanish",
    "events": {
        "infoSession2": true,
        "professionalPanel": false,
        "infoSession1": true,
        "resumeWorkshop": true,
        "socialEvent": false
    },
    "image": "https://example.com/images/john_smith.jpg",
    "gpa": "3.7",
    "hasGpa": true,
    "gradYear": "2026",
    "resume": "https://example.com/resume/john_smith.pdf",
    "major": "Marketing",
    "gradMonth": "May",
    "phone": "123-456-7890",
    "preferredName": "John"
}


```
---

## `/applicants/{listing_id}`

### `GET`

Retrieves all applicant withing a given `listing_id`.

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
[
  {
    "website": "https://www.example.com",
    "listingId": "c29e03c4-4e7d-497e-a5a9-c05c419ba8eb",
    "colleges": {
        "COM": false,
        "QST": false,
        "CDS": false,
        "CAS": false,
        "Wheelock": false,
        "Sargent": false,
        "Pardee": false,
        "SHA": false,
        "CGS": true,
        "ENG": false,
        "CFA": false,
        "Other": false
    },
    "responses": [
        {
            "question": "Question 1",
            "response": "Response 1"
        },
        {
            "question": "Question 2",
            "response": "Response 2"
        }
    ],
    "lastName": "Smith",
    "linkedin": "https://www.linkedin.com/in/john-smith",
    "dateApplied": "2024-03-25T12:00:00-05:00",
    "email": "john.smith@example.com",
    "firstName": "John",
    "applicantId": "12345678-1234-5678-1234-567812345678",
    "minor": "Spanish",
    "events": {
        "infoSession2": true,
        "professionalPanel": false,
        "infoSession1": true,
        "resumeWorkshop": true,
        "socialEvent": false
    },
    "image": "https://example.com/images/john_smith.jpg",
    "gpa": "3.7",
    "hasGpa": true,
    "gradYear": "2026",
    "resume": "https://example.com/resume/john_smith.pdf",
    "major": "Marketing",
    "gradMonth": "May",
    "phone": "123-456-7890",
    "preferredName": "John"
  },
  ...
]

```
---