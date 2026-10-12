FROM node:22-bookworm-slim

WORKDIR /app

COPY dpp-monad/api/package.json dpp-monad/api/package-lock.json ./api/
RUN npm ci --prefix ./api --omit=dev

COPY dpp-monad/api ./api
COPY dpp-monad/sdk ./sdk
COPY Monad_hackathon/sqlite/donation_box.db ./Monad_hackathon/sqlite/donation_box.db
COPY dpp-monad/api/data/original_records.json ./seed/original_records.json

ENV NODE_ENV=production
ENV SQLITE_FILE=/app/Monad_hackathon/sqlite/donation_box.db
ENV ORIGINAL_RECORDS_FILE=/data/original_records.json

CMD ["sh", "-c", "mkdir -p /data && if [ ! -f /data/original_records.json ]; then cp /app/seed/original_records.json /data/original_records.json; fi && node api/index.js"]
