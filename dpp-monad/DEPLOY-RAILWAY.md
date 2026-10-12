# Deploy the public Monad testnet demo

The app has two Railway services: an API and a static dashboard. The Envio
indexer is optional; the API falls back to Monad RPC when it is not configured.
The supplied `envio/docker-compose.yml` is for local development and is not a
production Railway service.

## 1. Create the API service

1. Create a Railway project from this GitHub repository.
2. Add a service from the same repository. Leave its root directory at `/`.
3. In service settings, select `dpp-monad/api/railway.json` as the Railway
   config file. The Dockerfile uses the repository root as its build context.
4. Attach a Railway Volume mounted at `/data` so the original proof registry
   survives restarts and deployments.
5. Set these service variables:

   ```text
   RPC_URL=https://testnet-rpc.monad.xyz
   CONTRACT_ADDRESS=0xBCD966873f48563643Bde79760eEC98a567fd7e6
   PRIVATE_KEY=<a dedicated, funded Monad testnet wallet key>
   SQLITE_FILE=/app/Monad_hackathon/sqlite/donation_box.db
   ORIGINAL_RECORDS_FILE=/data/original_records.json
   ```

   Set `ALLOWED_ORIGIN` to the dashboard's HTTPS domain after step 2. Do not
   commit or paste the private key into project files; enter it only in
   Railway's secret variable editor. This wallet can submit transactions, so
   use a dedicated testnet wallet with limited test funds.

6. Generate a public domain for the API. Railway should detect the app's
   `PORT` binding. The `/health` endpoint is used for deployment health checks.

## 2. Create the dashboard service

1. Add a second service from the same GitHub repository and leave its root at
   `/`.
2. Select `dpp-monad/dashboard/railway.json` as that service's config file.
3. Set the build variable `VITE_API_URL` to the full API origin, for example
   `https://your-api.up.railway.app` (no trailing slash).
4. Generate a public domain for the dashboard, then set the API's
   `ALLOWED_ORIGIN` to that exact HTTPS origin and redeploy the API.

## 3. Optional Envio Cloud indexer

Deploy `dpp-monad/envio` through Envio Cloud with this folder as the indexer
directory, connected to the repository and deployment branch. After Envio
provides the GraphQL endpoint, add `ENVIO_GRAPHQL_URL` to the Railway API
service. Leave the `/v1/graphql` suffix off; the API appends it itself. Without
this variable, proof-history lookups use the RPC fallback.

## Data and redeploys

The SQLite demo database is copied into the API image from
`Monad_hackathon/sqlite/donation_box.db`; changes to it require a new deploy.
The writable `original_records.json` is stored on the `/data` volume. To update
the starting SQLite snapshot, commit the intended database change and redeploy.
