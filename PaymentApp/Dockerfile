FROM node:12.16.3-alpine As builder
WORKDIR /usr/src/app
COPY package.json package-lock.json ./
RUN npm install
COPY . .
RUN npm run build --prod

FROM nginx:1.19.10-alpine
COPY --from=builder /usr/src/app/dist/PaymentApp/ /usr/share/nginx/html
