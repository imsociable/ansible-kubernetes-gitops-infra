# 🚀 GitOps k3s Infrastructure (Cilium + Argo CD)

Zero-touch GitOps-инфраструктура для Kubernetes на базе k3s.  
Проект демонстрирует полный цикл: от пустой VM до управляемого через Git кластера
с наблюдаемой сетью, метриками и логами.

---

## ✨ Overview

По одному запуску Ansible на пустой VM автоматически разворачивается:

- 🧱 k3s (без flannel)
- 🌐 Cilium (CNI)
- 🔁 Argo CD
- 🔐 Sealed Secrets
- 📊 Observability stack (metrics + logs + alerts)

После bootstrap **вся дальнейшая работа ведётся через Git (GitOps)**.

---
Polymarket-Copy-Trading-Bot/
│
├── rust/                    # Rust implementation
│   ├── src/                # Source code
│   ├── docs/               # Documentation
│   ├── README.md           # Rust bot documentation
│   └── Cargo.toml          # Rust dependencies
│
├── python/                  # Python implementation
│   ├── src/                # Source code
│   ├── docs/               # Documentation
│   ├── scripts/            # Utility scripts
│   ├── README.md           # Python bot documentation
│   └── requirements.txt    # Python dependencies
│
└── README.md               # This file
## 🧭 Architecture
VM (Cloud / VPS)
 └─ Ansible
     └─ k3s (без flannel)
         └─ Cilium
             ├─ Hubble (network observability)
             ├─ Argo CD
             │    └─ GitOps repository
             │         └─ Observability stack
             │              ├─ Prometheus (+ exporters)
             │              ├─ Grafana
             │              ├─ Alertmanager
             │              ├─ Loki + Promtail
             │              └─ PostgreSQL (+ metrics backup script)

Инфраструктура разделена на два слоя:

- 🛠 **Bootstrap layer** — всё до Argo CD
- 🔄 **GitOps layer** — всё, чем управляет Argo CD

---

## 🛠 Bootstrap layer (Ansible)

Ansible используется **только для первичного развёртывания**:

- установка k3s с отключённым flannel
- установка Cilium через Helm (zero-touch)
- установка Argo CD
- установка и настройка Sealed Secrets

После bootstrap:
- ❌ ручные `kubectl apply` не используются
- ✅ состояние кластера управляется только через Git

---

## 🔄 GitOps layer (Argo CD)

Argo CD отвечает за:

- синхронизацию состояния кластера из Git
- Helm-деплой с зафиксированными зависимостями (`Chart.lock`)
- self-healing при удалении или изменении ресурсов

Изменения в репозитории автоматически применяются в кластере.

---

## 🌐 Networking

- **Cilium** используется как основной CNI (eBPF)
- Проверено взаимодействие pod-to-pod
- Протестированы Network Policies

---

## 📊 Observability

### 📈 Metrics

- Prometheus:
  - node-exporter
  - blackbox-exporter
  - postgres-exporter

### 🪵 Logs

- Loki + Promtail
- Логи доступны в Grafana (Explore и dashboards)

### 🚨 Alerts

- Alertmanager

### 🗄 Metrics persistence

- PostgreSQL
- Python-скрипт:
  - извлекает сетевые метрики (blackbox ping) из Prometheus
  - сохраняет их в PostgreSQL для долгосрочного анализа и истории

---

## 🧩 Key properties

- ⚙️ zero-touch bootstrap Kubernetes-кластера
- 🧱 чёткое разделение bootstrap / GitOps слоёв
- 📜 декларативное управление инфраструктурой
- ♻️ воспроизводимость окружения
- 🔍 наблюдаемость сети, метрик и логов
- 🧠 возможность менять любой слой без ручных операций на VM

---

## 🧰 Tech stack

- Kubernetes (k3s)
- Cilium,
- Helm
- Argo CD
- Ansible
- Prometheus, Grafana, Alertmanager
- Loki, Promtail
- PostgreSQL
- Python
- Linux

