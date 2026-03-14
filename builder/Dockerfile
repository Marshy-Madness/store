# Official-style Decky plugin builder (node + pnpm, build then rsync to /out).
# See: https://github.com/SteamDeckHomebrew/decky-plugin-database/tree/main/builder
FROM node:20-alpine

RUN apk add --no-cache rsync git

RUN npm i --location=global pnpm@9

COPY ./entrypoint.sh /entrypoint.sh

RUN chmod +x /entrypoint.sh

ENTRYPOINT [ "/entrypoint.sh" ]
