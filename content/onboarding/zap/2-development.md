---
title: Development
type: docs
next: onboarding/zap/3-db-migrations
prev: onboarding/zap/1-setup
weight: 2
---

### Running Chalice

To turn on the virtual environment using `pipenv`:

```bash
pipenv shell
```

To install all necessary dependencies within `pipenv` from our repo:

```bash
pipenv install
```

To install any additional dependencies within `pipenv`:

```bash
pipenv install {dependency name}
```

> **Note**: If you haven't started the local Supabase instance yet, run:
>
> ```bash
> supabase start
> ```
>
> See the [Setup guide](/onboarding/zap/setup/#supabase) for full instructions.

To enable local server for Chalice:

```bash
chalice local
```

### Exploring & Testing API Endpoints: Tools & Tips

Calling APIs efficiently is essential for developers. While cURL requests are powerful, there are user-friendly alternatives:

1. [Postman](https://www.postman.com/downloads/?)
2. [HTTPie](https://httpie.io/desktop)

Choose a tool that suits your comfort level and workflow. Both Postman and HTTPie streamline API testing and development tasks.
