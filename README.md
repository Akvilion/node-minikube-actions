# 🚀 Node.js + Minikube Deployment via GitHub Actions

[![Node.js](https://img.shields.io/badge/Node.js-18%2B-339933?logo=node.js&logoColor=white)](https://nodejs.org/)
[![Docker](https://img.shields.io/badge/Docker-Engine-2496ED?logo=docker&logoColor=white)](https://www.docker.com/)
[![Kubernetes](https://img.shields.io/badge/Kubernetes-Minikube-326CE5?logo=kubernetes&logoColor=white)](https://minikube.sigs.k8s.io/)
[![GitHub Actions](https://img.shields.io/badge/GitHub%20Actions-CI%2FCD-2088FF?logo=githubactions&logoColor=white)](https://github.com/features/actions)
[![License](https://img.shields.io/badge/License-MIT-lightgrey)](LICENSE)

> **Мета:** показати, як задеплоїти простий Node.js-додаток на Express у локальний кластер **Minikube**, використовуючи **GitHub Actions** для автоматизації CI/CD.

---

## 🧭 Зміст
- [Структура проєкту](#-структура-проєкту)
- [Попередні вимоги](#️-попередні-вимоги)
- [Локальний запуск](#-локальний-запуск)
- [Робота з Docker](#-робота-з-docker)
- [Ручний деплой у Minikube](#️-ручний-деплой-у-minikube)
- [CI/CD Workflow у GitHub Actions](#-cicd-workflow-у-github-actions)
- [Очищення після тестів](#-очищення-після-тестів)

---

## 📁 Структура проєкту

| Файл | Призначення |
|------|-------------|
| `server.js` | Мінімальний Express-сервер, який відповідає `"Hello World"` на порту `3000`. |
| `package.json` | Опис залежностей і базові скрипти NPM. |
| `Dockerfile` | Інструкції для створення Docker-образу. |
| `k8s-node-app.yaml` | Kubernetes-манифест (Deployment + Service) із плейсхолдером для тега образу. |
| `.github/workflows/deploy-to-minikube-github-actions.yaml` | GitHub Actions workflow, який автоматично збирає й деплоїть застосунок. |

---

## ⚙️ Попередні вимоги
Перед початком переконайся, що встановлено локально:

- 🟩 [Node.js 14+](https://nodejs.org/)
- 🐳 [Docker](https://www.docker.com/)
- ☸️ [Minikube](https://minikube.sigs.k8s.io/docs/start/)
- 🔧 [`kubectl`](https://kubernetes.io/docs/tasks/tools/)

> 💡 **Примітка:** у CI середовищі GitHub використовується офіційний екшен [`medyagh/setup-minikube@v1`](https://github.com/marketplace/actions/setup-minikube), тому встановлювати Minikube вручну не потрібно.

---

## 🧩 Локальний запуск

1. **Встанови залежності:**
   ```bash
   npm install
   ```
2. **Запусти сервер:**
   ```bash
   npm start
   ```
3. **Перевір роботу застосунку:** відкрий [http://localhost:3000](http://localhost:3000) та переконайся, що бачиш текст `Hello World`.

---

## 🐳 Робота з Docker

1. **Створи локальний образ:**
   ```bash
   docker build -t devopshint/node-app:local .
   ```
2. **Запусти контейнер:**
   ```bash
   docker run --rm -p 3000:3000 devopshint/node-app:local
   ```

---

## ☸️ Ручний деплой у Minikube

| Крок | Дія | Команда |
|------|-----|---------|
| 1️⃣ | Запусти Minikube | `minikube start` |
| 2️⃣ | Використай Docker із середовища Minikube | `eval "$(minikube -p minikube docker-env)"` |
| 3️⃣ | Збери образ | `docker build -t devopshint/node-app:local .` |
| 4️⃣ | Задеплой застосунок | `sed "s/IMAGE_TAG/local/g" k8s-node-app.yaml | kubectl apply -f -` |
| 5️⃣ | Переконайся, що деплой завершено | `kubectl rollout status deployment/nodejs-app` |
| 6️⃣ | Отримай URL сервісу | `minikube service nodejs-app --url` |

**Перевірка відповіді:**
```bash
curl "$(minikube service nodejs-app --url | head -n 1)"
```

---

## 🔁 CI/CD Workflow у GitHub Actions

Основні етапи автоматизованого деплою:

1. Клонування репозиторію.
2. Підняття кластера Minikube за допомогою `medyagh/setup-minikube@v1`.
3. Збірка Docker-образу з тегом на основі `GITHUB_SHA`.
4. Підстановка тега у манифест та деплой через `kubectl apply`.
5. Очікування завершення деплою (`kubectl rollout status`).
6. Перевірка відповіді сервісу (`curl` за URL Minikube).

✅ Якщо всі кроки успішні — застосунок збирається, деплоїться та відповідає в новому кластері.
❌ У разі помилки процес зупиняється та позначається як `failed`.

---

## 🧹 Очищення після тестів
Після завершення експериментів видали ресурси:

```bash
kubectl delete -f <(sed "s/IMAGE_TAG/local/g" k8s-node-app.yaml)
minikube stop
```