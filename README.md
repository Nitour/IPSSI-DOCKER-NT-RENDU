# TODO Docker Infrastructure

Ce projet met en place une infrastructure complète pour une application de gestion de tâches (TODO List) à l'aide de Docker Compose. L'architecture comprend un reverse proxy, une base de données, une API REST, ainsi qu'un système de monitoring basé sur Prometheus et Grafana.

---

## Objectifs

- Déployer une stack multi-services avec Docker Compose
- Gérer les réseaux Docker (isolation et communication ciblée)
- Assurer la persistance des données avec des volumes
- Protéger les secrets via des fichiers `.env`
- Implémenter un reverse proxy dynamique avec Traefik
- Mettre en place un système de monitoring complet (Prometheus, Grafana, Exporters)
- Appliquer les bonnes pratiques Docker (sécurité, structure, optimisation)

---

## Architecture réseau (ASCII)

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
```
## Objectifs

| Service             | Description                      | Port interne |
| ------------------- | -------------------------------- | ------------ |
| `app`               | API REST Flask                   | 5000         |
| `db`                | PostgreSQL                       | 5432         |
| `traefik`           | Reverse proxy + dashboard        | 80, 8080     |
| `prometheus`        | Collecte de métriques            | 9090         |
| `grafana`           | Visualisation de métriques       | 3000         |
| `postgres_exporter` | Exporter de métriques PostgreSQL | 9187         |
| `node_exporter`     | Exporter des métriques système   | 9100         |


---

## Structure du projet

```text
todo-docker-infra/
├── .env.example                # Fichier d'exemple pour les variables d'environnement
├── docker-compose.yml         # Définition des services
├── app/                       # Code source de l'application Flask
│   ├── Dockerfile
│   ├── main.py
│   ├── requirements.txt
│   └── config/
│       └── database.py
├── scripts/
│   └── init-db.sql            # Script SQL d'initialisation
├── prometheus/
│   └── prometheus.yml
├── grafana/
│   ├── provisioning/
│   │   ├── datasources/
│   │   │   └── prometheus.yml
│   │   └── dashboards/
│   │       ├── flask_dashboard.json
│   │       └── postgres_dashboard.json
├── traefik/
│   └── traefik.yml
└── README.md
```

## Bonnes pratiques respectés 

```text

| Pratique                                 | Statut | Détail technique                                |
| ---------------------------------------- | ------ | ----------------------------------------------- |
| Réseaux séparés et ciblés                | ✔️     | `backend`, `traefik`, `monitoring`              |
| Secrets via `.env`                       | ✔️     | `.env` ignoré par Git, utilisé par les services |
| Images légères optimisées                | ✔️     | `python:3.11-slim`, `node:18-alpine`, etc.      |
| Healthchecks pour tous les services clés | ✔️     | Définis dans `docker-compose.yml`               |
| Utilisateurs non-root                    | ✔️     | Dockerfile Flask utilise `appuser`              |
| Dashboards Grafana préchargés            | ✔️     | Provisioning automatique                        |
| Monitoring système et base de données    | ✔️     | `node_exporter`, `postgres_exporter`            |
| Routing dynamique par Traefik            | ✔️     | Labels Docker intégrés                          |
| Structure de projet modulaire            | ✔️     | Arborescence propre et extensible               |
| Documentation incluse                    | ✔️     | README explicite et structuré                   |
```