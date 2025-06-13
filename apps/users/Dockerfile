# Dockerfile para microservicio de usuarios
FROM node:20-alpine
WORKDIR /app
COPY package*.json ./
RUN npm install --production
COPY . .
COPY ./prisma ./prisma
RUN npx prisma generate
EXPOSE 3001
CMD ["npm", "run", "start:prod"] 