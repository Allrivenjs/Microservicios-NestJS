# Dockerfile para microservicio de pedidos
FROM node:20-alpine
WORKDIR /app
COPY package*.json ./
RUN npm install --production
COPY . .
COPY ./prisma ./prisma
RUN npx prisma generate
EXPOSE 3002
CMD ["npm", "run", "start:prod"] 