# 🚀 Node.js + Minikube Deployment via GitHub Actions

[![Node.js](https://img.shields.io/badge/Node.js-18+-green?logo=node.js)](https://nodejs.org/)
[![Docker](https://img.shields.io/badge/Docker-Engine-blue?logo=docker)](https://www.docker.com/)
[![Kubernetes](https://img.shields.io/badge/Kubernetes-Minicube-326CE5?logo=kubernetes)](https://minikube.sigs.k8s.io/)
[![GitHub Actions](https://img.shields.io/badge/CI-CD-blue?logo=githubactions)](https://github.com/features/actions)
[![License](https://img.shields.io/badge/license-MIT-lightgrey)](LICENSE)

> **Мета:** показати, як задеплоїти простий Node.js-додаток на Express у локальний кластер **Minikube**, використовуючи **GitHub Actions** для автоматизації CI/CD.

---

## 📁 Структура проєкту

| Файл | Призначення |
|------|--------------|
| `server.js` | Мінімальний Express-сервер, який відповідає `"Hello World"` на порту `3000` |
| `package.json` | Залежності та базові налаштування NPM |
| `Dockerfile` | Інструкції для створення Docker-образу |
| `k8s-node-app.yaml` | Манифест Kubernetes (Deployment + Service) із плейсхолдером для тега |
| `.github/workflows/deploy-to-minikube-github-actions.yaml` | GitHub Actions workflow, який автоматично збирає й деплоїть застосунок |

---

## ⚙️ Попередні вимоги

Перед початком переконайся, що встановлено:

- 🟩 [Node.js 14+](https://nodejs.org/)
- 🐳 [Docker](https://www.docker.com/)
- ☸️ [Minikube](https://minikube.sigs.k8s.io/docs/start/)
- 🔧 [`kubectl`](https://kubernetes.io/docs/tasks/tools/)

> 💡 **Примітка:**  
> У CI середовищі GitHub використовується офіційний екшен  
> [`medyagh/setup-minikube@v1`](https://github.com/marketplace/actions/setup-minikube),  
> тому встановлювати Minikube вручну не потрібно.

---

## 🧩 Локальний запуск

1. **Встанови залежності:**
   ```bash
   npm install
Запусти сервер:

bash
Copy code
npm start
Перевір роботу:
👉 http://localhost:3000
Має відобразитися текст "Hello World".

🐳 Збірка Docker-образу
Створення локального образу:

bash
Copy code
docker build -t devopshint/node-app:local .
Запуск контейнера:

bash
Copy code
docker run --rm -p 3000:3000 devopshint/node-app:local
☸️ Деплой у Minikube вручну
Крок	Команда
1️⃣ Запуск Minikube	minikube start
2️⃣ Використати Docker Minikube	eval "$(minikube -p minikube docker-env)"
3️⃣ Зібрати образ	docker build -t devopshint/node-app:local .
4️⃣ Задеплоїти застосунок	`sed "s/IMAGE_TAG/local/g" k8s-node-app.yaml
5️⃣ Перевірити статус	kubectl rollout status deployment/nodejs-app
6️⃣ Отримати URL сервісу	minikube service nodejs-app --url

Тест відповіді:

bash
Copy code
curl "$(minikube service nodejs-app --url | head -n1)"
🔁 CI/CD Workflow у GitHub Actions
⚙️ Основні кроки
№	Опис дії
1	Клонує репозиторій
2	Піднімає кластер Minikube через medyagh/setup-minikube@v1
3	Збирає Docker-образ з тегом на основі GITHUB_SHA
4	Підставляє тег у манифест і деплоїть через kubectl apply
5	Очікує завершення деплою (kubectl rollout status)
6	Перевіряє роботу сервісу (curl по URL Minikube)

✅ Якщо всі кроки успішні — застосунок збирається, деплоїться й відповідає в новому кластері.
❌ У разі помилки процес зупиняється та позначається як “failed”.

🧹 Очищення після тестів
Після завершення експериментів видали ресурси:

bash
Copy code
kubectl delete -f <(sed "s/IMAGE_TAG/local/g" k8s-node-app.yaml)
minikube stop