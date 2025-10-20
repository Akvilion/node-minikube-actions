# 🐳 Node.js App on Docker & Kubernetes

Цей проєкт — мінімальний приклад Node.js застосунку, який розгортається у Docker-контейнері та Kubernetes-кластері.  
Додаток піднімає простий HTTP-сервер на Express.js, що відповідає `"Hello World"` при запиті на `/`.

---

## ⚙️ Технології

- **Node.js 14**
- **Express.js**
- **Docker**
- **Kubernetes (Deployment + Service)**

---

## 🚀 Як запустити локально

### 1. Запуск у Docker
```bash
docker build -t nodejs-app .
docker run -p 3000:3000 nodejs-app
