# Build-nuxtjs

การสร้างเว็บด้วย **Nuxt.js** และ deploy ด้วย **Docker** โดยใช้ **Nginx** เป็น reverse proxy

---

## 🧱 ขั้นตอนที่ 1: สร้างโปรเจกต์ Nuxt.js

```bash
npx nuxi init my-nuxt-app
cd my-nuxt-app
npm install
```

ทดสอบรันโปรเจกต์:

```bash
npm run dev
```

---

## 🛠️ ขั้นตอนที่ 2: สร้างไฟล์ `nuxt.config.ts`

ตรวจสอบว่าไฟล์ `nuxt.config.ts` มีการตั้งค่า `ssr` และ `target` ที่เหมาะสม:

```ts
export default defineNuxtConfig({
  ssr: true,
  app: {
    baseURL: '/',
  },
})
```

---

## 📦 ขั้นตอนที่ 3: สร้าง Dockerfile

```Dockerfile
# Dockerfile
FROM node:20-alpine AS builder

WORKDIR /app

COPY . .

RUN npm install
RUN npm run build

# Production image
FROM nginx:stable-alpine

COPY --from=builder /app/.output/public /usr/share/nginx/html

COPY nginx.conf /etc/nginx/conf.d/default.conf

EXPOSE 80

CMD ["nginx", "-g", "daemon off;"]
```

---

## 🌐 ขั้นตอนที่ 4: สร้างไฟล์ `nginx.conf`

```nginx
server {
  listen 80;
  server_name localhost;

  location / {
    root /usr/share/nginx/html;
    index index.html;
    try_files $uri $uri/ /index.html;
  }
}
```

---

## 🧪 ขั้นตอนที่ 5: Build และ Run Docker

```bash
docker build -t nuxt-nginx-app .
docker run -p 8080:80 nuxt-nginx-app
```

เปิดเบราว์เซอร์ที่ `http://localhost:8080` เพื่อดูผลลัพธ์

---

## ✅ สรุป

คุณจะได้เว็บ Nuxt.js ที่ build แล้วและเสิร์ฟผ่าน Nginx ด้วย Docker ซึ่งเหมาะสำหรับการ deploy แบบ production

ถ้าคุณต้องการเพิ่มฟีเจอร์เช่น SSR, API, หรือใช้ Docker Compose ก็สามารถขยายต่อได้ครับ

ต้องการให้ช่วยสร้างไฟล์ทั้งหมดเป็น zip หรือมีส่วนไหนอยากปรับแต่งเพิ่มเติมไหมครับ?
