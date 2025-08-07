# TODO Docker Infrastructure

Ce projet met en place une infrastructure complète pour une application de gestion de tâches (TODO List) à l'aide de Docker Compose. L'architecture comprend un reverse proxy, une base de données, une API REST, ainsi qu'un système de monitoring basé sur Prometheus et Grafana.

---

## 📌 Objectifs pédagogiques

- Déployer une stack multi-services avec Docker Compose
- Gérer les réseaux Docker (isolation et communication ciblée)
- Assurer la persistance des données avec des volumes
- Protéger les secrets via des fichiers `.env`
- Implémenter un reverse proxy dynamique avec Traefik
- Mettre en place un système de monitoring complet (Prometheus, Grafana, Exporters)
- Appliquer les bonnes pratiques Docker (sécurité, structure, optimisation)

---

## 🧱 Architecture réseau (ASCII)

```text
                                [ Client Web ]
                                      |
                                      v
                         ┌────────────────────────────┐
                         │     Reverse Proxy (Traefik)│
                         │      :80, :8080 (dashboard)│
                         └──────────┬─────────────────┘
                                    |
           ┌────────────────────────┴─────────────────────────┐
           │                                                  │
           v                                                  v
  [ API Flask - port 5000 ]                         [ Grafana - port 3000 ]
           |                                                  ^
           v                                                  |
  [ PostgreSQL - port 5432 ]                                  |
           ^                                                  |
           |                        ┌─────────────────────────┼────────────────┐
           |                        |                         |                |
           |             [ PostgreSQL Exporter - 9187 ]   [ Node Exporter - 9100 ]
           |                        |                         |
           └────────────────────────┴──────────────┬──────────┘
                                                  v
                                    [ Prometheus - port 9090 ]
