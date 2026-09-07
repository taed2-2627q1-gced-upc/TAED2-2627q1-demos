# Team object store on the Team VM

This is an **extra demo**, not the canonical M2 path. Canonical DVC and MLflow use a **DagsHub project** ([DVC demo](../dvc-demo.md), [MLflow demo](../mlflow-demo.md)). Use this walkthrough if you want to self-host the DVC remote and OSS MLflow on the **Team VM** instead.

Each team runs one single-node AIStor Free service on its **Team VM**. It stores DVC objects and MLflow artifacts; MLflow run metadata stays in the local SQLite database. Docker here is Team object store infrastructure, not a packaging milestone.

The service is intentionally disposable: there is no off-VM backup or recovery promise. Before every **Milestone**, delete selected MLflow artifacts or local DVC caches if space is tight. Do not manually delete DVC remote objects during the course.

## 1. Start AIStor

Use the checked-in [team object store templates](../../infra/team-object-store/). On the Team VM, copy that directory outside the Git repository, then copy `.env.example` to `.env`. Put the team's AIStor Free `minio.license` beside `compose.yaml`; each team obtains and renews its own licence.

Set long, unique `MINIO_ROOT_USER` and `MINIO_ROOT_PASSWORD` values in `.env`, then start the one container:

```bash
docker compose up -d
docker compose ps
```

AIStor is deliberately pinned to `RELEASE.2025-12-20T04-58-37Z`, the instructor-maintained course release. Do not replace it with `latest` during the edition. The API and console bind only to `127.0.0.1`; they are not directly public.

## 2. Publish HTTPS aliases

Install native Caddy on the Team VM. Derive one alias for each service from the stable public IP, for example:

```text
storage-203-0-113-10.sslip.io
mlflow-203-0-113-10.sslip.io
```

Copy [the Caddy environment example](../../infra/team-object-store/caddy.env.example) to `/etc/caddy/team-object-store.env`, set the two aliases, then install [the Caddyfile template](../../infra/team-object-store/Caddyfile.template) as `/etc/caddy/Caddyfile`. Install [the systemd override](../../infra/team-object-store/caddy.service.override.conf) at `/etc/systemd/system/caddy.service.d/team-object-store.conf`, then reload and restart Caddy:

```bash
sudo systemctl daemon-reload
sudo systemctl restart caddy
```

Caddy terminates TLS on public ports 80 and 443 and proxies to the loopback services. Firewall every raw service port, including 5000, 9000, and 9001.

The AIStor console stays private. Reach it only through an SSH tunnel:

```bash
ssh -L 9001:127.0.0.1:9001 <vm-user>@<team-vm-ip>
```

Then browse to `http://127.0.0.1:9001`. The public MLflow UI/API is intentionally unauthenticated so remote notebooks can log runs; do not use it for sensitive work.

## 3. Create buckets and least-privilege credentials

Using the AIStor client (`mc`) through the loopback API, create exactly two buckets and upload the policies:

```bash
mc alias set team http://127.0.0.1:9000 "$MINIO_ROOT_USER" "$MINIO_ROOT_PASSWORD"
mc mb --ignore-existing team/dvc
mc mb --ignore-existing team/mlflow-artifacts
mc admin policy create team dvc policies/dvc.json
mc admin policy create team mlflow-artifacts policies/mlflow-artifacts.json
```

Create an individual user for every team member and attach the `dvc` policy. Create one `mlflow` service user and attach the `mlflow-artifacts` policy:

```bash
mc admin user add team <access-key> <secret>
mc admin policy attach team dvc --user <team-member-access-key>
mc admin policy attach team mlflow-artifacts --user <mlflow-access-key>
```

The MLflow user must not access `dvc`; a DVC user must not access `mlflow-artifacts`.

Keep the root credential and the MLflow service credential on the Team VM. Give each team member an individual DVC key and rotate a key if it leaks. Never commit any of these values.

## 4. Run MLflow with AIStor artifacts

Create an `mlflow` system user and copy [the systemd unit template](../../infra/team-object-store/mlflow.service.template) to `/etc/systemd/system/mlflow.service`. Copy [the MLflow environment example](../../infra/team-object-store/mlflow.env.example) to `/etc/mlflow/team-object-store.env`, replace its values, and set the real storage alias. The environment file supplies the MLflow-only S3 credential. The unit runs MLflow through `uvx` and pulls in `boto3` so the server can write artifacts to AIStor.

Reload and start it:

```bash
sudo systemctl daemon-reload
sudo systemctl enable --now mlflow
```

MLflow listens only on `127.0.0.1:5000`; Caddy publishes `https://<team-tracking-alias>`. Its `--artifacts-destination s3://mlflow-artifacts` setting proxies artifacts through MLflow, so notebooks need only the tracking URI, not AIStor credentials.

## 5. Configure DVC clients

If you take this extra demo, point DVC at the Team object store **instead of** the DagsHub project. Install DVC's S3 support and use path-style addressing:

```bash
uv add 'dvc[s3]'
dvc remote add -d storage s3://dvc
dvc remote modify storage endpointurl https://storage-203-0-113-10.sslip.io
dvc remote modify storage use_path_style_endpoint true
dvc remote modify --local storage access_key_id <your-individual-dvc-key>
dvc remote modify --local storage secret_access_key <your-individual-dvc-secret>
```

`--local` puts the keys in `.dvc/config.local`, which must stay out of Git. Use `dvc push` and `dvc pull` normally. Do not run remote DVC garbage collection in this course.
