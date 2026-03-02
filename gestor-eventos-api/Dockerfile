# ---- Base ----
FROM node:20-alpine AS base
WORKDIR /app

# ---- Deps ----
FROM base AS deps
COPY package*.json ./
RUN npm ci

# ---- Build ----
FROM base AS build
COPY --from=deps /app/node_modules ./node_modules
COPY . .
RUN npm run build

# ---- Run ----
FROM base AS run
ENV NODE_ENV=production
WORKDIR /app

COPY --from=deps /app/node_modules ./node_modules
COPY --from=build /app/dist ./dist
COPY --from=build /app/prisma ./prisma
COPY package*.json ./

EXPOSE 3001

# Ejecuta migraciones al arrancar y luego levanta Nest
CMD ["sh", "-c", "npx prisma migrate deploy && node dist/main.js"]