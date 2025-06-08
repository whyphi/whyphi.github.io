---
title: Structure
type: docs
next: onboarding/zap/5-env-vars
prev: onboarding/zap/3-db-migrations
weight: 4
---

In AWS Chalice, the application itself is served under the root directory in a file called `app.py`. All other subdirectories and modules need to be created under a directory called `chalicelib`.

### Exploring `chalicelib`

Within the `chalicelib` directory, many directories exist. Let's now see the purpose of each directories:

#### `chalicelib/api`

This is where all API endpoints are created in our backend application. We leverage [blueprints](https://aws.github.io/chalice/topics/blueprints.html) to organize our application into logical components.

To create your own blueprint and API endpoints, create a new file in api, intialize a new blueprint object, and create your endpoints:
<img src="/images/zap/new_api.png" width="600">

To ensure that your API gets registered to the chalice application, go to `app.py`, import the blueprint and register it on `app` object:
<img src="/images/zap/register_api.png" width="600">

#### `chalicelib/models`

Models is where we create the data structure and types for data that the application works with. To ensure that our data is consistent throughout our application, we utilize [Pydantic](https://docs.pydantic.dev/latest/), a data validation library for Python.

If there is a need to create new data types for new features, initialize a new model through Pydantic, and you can use it for validation purposes.

#### `chalicelib/services`

For Zap, we attempt to use a layered architecture. The general schema of layers on a backend application is shown below:
<img src="/images/zap/layer_schema.jpeg" width="450">

The `services` directory acts as the service layer for Whyphi, in which it contains business logic and handles data manipulations. "Generally services contain information that is related to their domain.

For example, if we have "Mail Service" we expect that sending/receiving emails happens there like in a real life. The same is fair for the codebase. Services (and their methods) handle the business logic which means that they are responsible for transforming data, performing additional actions (like asking the repository for additional data or another service for processing some logic for it)."

To learn more: https://dev.to/blindkai/backend-layered-architecture-514h

To create a new service module, create a new file called `{service_name}Service.py` and create a class. Then, create an instance of that class at the bottom of the file, so we can import the instance directly from other files. Here is an example of `ApplicantService.py`:

```Python
# chalicelib/services/ApplicantService.py

class ApplicantService:
    def __init__(self):
        self.table_name = "apps"

    def get(self, id: str):
        data = db.get_item(table_name=self.table_name, key={"applicantId": id})
        return data

    def get_all(self):
        data = db.get_all(table_name=self.table_name)
        return data

    def get_all_from_listing(self, id: str):
        data = db.get_applicants(table_name=self.table_name, listing_id=id)
        return data

applicant_service = ApplicantService()
```

#### `chalicelib/modules`

For Whyphi, we take advantage of a lot of microservices and cloud applications. This means that we need different functionalities for each service to handle the data and/or logic. Thus, for each microservice or application, we would create a file and put them in `modules.`

For example, in Whyphi, we use MongoDB and AWS S3. Each microservice would have its own file with specific functionalities that we would use. For those services, we would create the following files:

- `chalicelib/modules/s3.py`
- `chalicelib/modules/mongodb.py`
