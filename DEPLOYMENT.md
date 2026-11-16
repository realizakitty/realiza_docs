# Independent repository deployment

All three code repositories build/test independently. Pushes to `release-prod` deploy; `main` is the initial source branch and does not deploy. Manual deployment also requires selecting `release-prod`.

| Repository | HOST variable | PORT variable | DEPLOY_USERNAME secret | Deployment location |
| --- | --- | --- | --- | --- |
| realiza_web | 49.51.204.206 | 22 | website | /home/website/websites/realiza_web/dist |
| realiza_admin | 49.51.204.206 | 22 | website | /home/website/websites/realiza_admin/dist |
| realiza_server | 43.130.58.225 | 22 | gouploader | /home/server/realiza/releases + current |

The owner configures `DEPLOY_KEY` and `DEPLOY_USERNAME` as repository Actions secrets. No secrets are required for the docs repository. Server public keys are fetched at deploy time using ssh-keyscan, following the P2L convention.

Before deployment, install the deployment public key in each target user's authorized_keys. For the API prepare releases/shared directories, populate shared/server.env and shared/config.yml, and install deploy/realiza.service with a restricted realiza runtime user. The API binds 127.0.0.1:8092; configure HTTPS reverse proxy and DNS on the API host. Frontend Nginx must serve the respective dist directory and forward /api (and public-site /share) to the API HTTPS endpoint, preserving Origin and setting the upstream Host and TLS SNI correctly. Configure actual PUBLIC_ORIGIN/ADMIN_ORIGIN in server.env. See realiza_server/deploy templates.

A push to release-prod triggers deployment. The API performs its resumable ID and currency migrations at startup before accepting requests; pushing main alone does not run these migrations. API secrets stay on the server; local Windows P2L config paths are development-only. Keep production MongoDB in its own realiza database and specify super administrators by verified Realiza user ID.

Business IDs use AC (account), WS (wish), HI (help intent), RP (report), and AU
(audit) plus a base-36 Snowflake. Existing public wish/share URLs and email
verification links remain compatible through private migration mappings.
Do not remove these mappings or roll back to an ObjectID-only binary after
conversion. The server release ID_SCHEMA marker blocks unsafe automatic binary
rollback across this schema change. See the server BUSINESS_IDS.md for details.
