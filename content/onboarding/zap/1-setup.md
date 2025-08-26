---
title: Setup
type: docs
next: onboarding/zap/2-development
prev: onboarding/zap
weight: 1
---

This guide walks you through installing **Python** and **Pipenv**, configuring **AWS credentials**, and setting up **Supabase** and **MinIO** locally with **Docker**.

## Python (and Pipenv)

Zap is built using **Python 3.9**. Ensure it's installed:

```bash
python3 --version
```

To manage dependencies, we use [Pipenv](https://pipenv.pypa.io/en/latest/), a virtual environment tool.

Install it via pip:

```bash
pip install --user pipenv
```

Verify installation:

```bash
pipenv --version
```

## AWS CLI & Credentials

Zap uses multiple AWS services. Before interacting with them:

{{% steps %}}

### Installation

Follow directions at [AWS CLI](https://aws.amazon.com/cli/)

### Configure AWS CLI

```bash
aws configure
```

Ask a member of the PCT Tech Team for the credentials.

{{% details title="How to confirm your AWS credentials" closed="true" %}}

Run the following:

```bash
cat ~/.aws/credentials
```

Verify that the access keys match the IAM console on [AWS](https://us-east-1.console.aws.amazon.com/iam/home#/security_credentials). Or simply confirm with the PCT Tech Team member who gave you them.

**Do not share your credentials with anyone.**

{{% /details %}}

Example config:

```
AWS Access Key ID [****************]: YOUR_ACCESS_KEY
AWS Secret Access Key [****************]: YOUR_SECRET_KEY
Default region name [us-east-1]: us-east-1
Default output format [json]: json
```

{{% /steps %}}

## Supabase

As of `dev/v4.0`, Zap uses [Supabase](https://supabase.com/) for its database, replacing DynamoDB and MongoDB. You’ll run a local Supabase stack using Docker.

{{% steps %}}

### Installation

- Install [Docker Desktop](https://docs.docker.com/desktop/)
- Install [Supabase CLI](https://supabase.com/docs/guides/local-development#install-the-cli)

**Important:** Do _not_ run `supabase init`. Supabase is already initialized in the `zap` repo.

### Navigate to the `zap` directory

```bash
cd zap
```

### Start the Supabase local stack

```bash
supabase start
```

If successful, you’ll see URLs and keys printed to your terminal:

```
Started supabase local development setup.

         API URL: http://127.0.0.1:54321
     GraphQL URL: http://127.0.0.1:54321/graphql/v1
  S3 Storage URL: http://127.0.0.1:54321/storage/v1/s3
          DB URL: postgresql://postgres:postgres@127.0.0.1:54322/postgres
      Studio URL: http://127.0.0.1:54323
      JWT secret: ...
        anon key: ...
service_role key: ...
```

### Create `.env.local`:

Copy your `API URL` and `anon key` from above into the following file.

```env
# zap/.env.local
SUPABASE_URL=<API URL>
SUPABASE_KEY=<anon key>
```

{{< callout type="warning" >}}
This file must NOT be committed to source control.
{{< /callout >}}

### Stop Supabase

When you are done using the backend, be sure to stop Supabase.

```bash
supabase stop
```

{{% details title="Do I really need to run `supabase stop`?" closed="true" %}}

Technically, you can quit Docker Desktop directly to stop the services, but running `supabase stop` is safer to avoid corruption or orphaned containers.

Just make sure to **fully quit** Docker Desktop or your CPU will want to kill you 😡.

<img src="/images/zap/quit_docker_desktop.png" width="300">

{{% /details %}}

{{% /steps %}}

## MinIO

Following the `dev/v4.0` release with our **local-first** development strategy, all development images are now stored in a local S3 [emulator](https://en.wikipedia.org/wiki/Emulator) called [MinIO](https://www.min.io/). Fortunately, it is fairly  straightforward and only requires a few commands.

{{% steps %}}

### Installation

- Install [Docker Desktop](https://docs.docker.com/desktop/)
- See [Docker Compose](https://docs.docker.com/compose/) documentation for more information
- Install [MinIO Client (mc)](https://github.com/minio/mc?tab=readme-ov-file#minio-client-quickstart-guide) (if using Mac, I'd suggest installing with [Homebrew](https://brew.sh/) -> [reference](https://github.com/minio/mc?tab=readme-ov-file#homebrew))

### Navigate to `zap` directory

```bash
cd zap
```

### Start MinIO

```bash
docker compose -d     # -d is used to run in "detached" mode without taking over the terminal
```

If successfuly, you'll see a message that looks like the following:

```bash
~ docker compose up -d
[+] Running 2/2
 ✔ Network zap_default  Created                                                                                           0.0s 
 ✔ Container minio      Started 
```

### Add MinIO server to `mc`

This command only needs to be run once when using MinIO for the WhyPhi.

```bash
mc alias set local http://localhost:9000 minio minio123
```

### Create Bucket and Add Permissions

Run these commands to create the bucket and allow public access to the bucket

```bash
mc mb local/whyphi-zap                        # create bucket
mc anonymous set download local/whyphi-zap    # set permissions
```

### View MinIO Data

To view your locally emulated data, MinIO offers a dashboard that can be found here:

[http://localhost:9001](http://localhost:9001)

Use the `MINIO_ROOT_USER` and `MINIO_ROOT_PASSWORD` found in `zap/docker-compose.yml` to login.

### Stop MinIO 

To stop MinIO simply run:

```bash
docker compose down
```

If you also want to remove all locally stored S3 data:

```bash
docker compose down -v    # CAUTION: this command removes all local S3 data
```

{{% /steps %}}