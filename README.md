# Parking Manager

Application web fullstack de gestion de parkings et de tickets de stationnement.

- **Démo en ligne :** [https://parking.lmp-services.ca](https://parking.lmp-services.ca)
- **Documentation API (Swagger) :** [https://parking-api.lmp-services.ca/docs](https://parking-api.lmp-services.ca/docs)

---

## Stack technique

- **Backend :** NestJS, TypeORM, PostgreSQL 18, bcrypt, JWT (Access & Refresh tokens), Swagger / OpenAPI.
- **Frontend :** Angular (Standalone Components, Signals, Router Guards, Intercepteurs HTTP).
- **Infrastructure :** Docker Compose, Traefik, Dokploy.

---

## Fonctionnalités

- **Authentification & Sécurité :**
  - Inscription (`/api/security/sign-up`) et connexion (`/api/security/sign-in`).
  - Gestion de session par paire de tokens JWT (Access Token 15 min + Refresh Token 7 jours).
  - Protection des routes via `JwtGuard` global avec décorateur `@Public()`.
  - Hachage des mots de passe avec bcrypt.
- **Gestion des parkings :**
  - Consultation de la liste et détails d'un parking.
  - Ajout, modification et suppression de parkings (nom, adresse, capacité, tarif horaire).
  - Clés primaires générées sous format ULID (tri chronologique et non prédictible).
- **Tickets :**
  - Association des tickets aux parkings avec suivi des entrées/sorties et calcul du montant.

---

## Structure du projet

```text
├── api/                  # Backend NestJS
│   ├── src/
│   │   ├── common/       # Filtres d'exceptions, intercepteurs de réponse, config
│   │   ├── module/       # Modules métier (parking, ticket)
│   │   └── security/     # Auth, JWT guard, gestion des tokens, entités users
│   └── test/             # Tests e2e
├── front/                # Frontend Angular
│   └── src/app/
│       ├── feature/auth/ # Pages d'inscription et de connexion
│       ├── parking/      # Pages CRUD parking (liste, création, édition, détail)
│       ├── security/     # Guards de navigation, intercepteur Bearer token
│       └── shared/       # Composants et layouts partagés
└── docker-compose.yml    # Orchestration locale de l'environnement complet
```

---

## Installation et lancement

### Option 1 : Avec Docker (Recommandé)

Prérequis : Docker et Docker Compose installés.

```bash
# Cloner le dépôt
git clone https://github.com/patsonatorEFPL/ProjetWeb_2026.git
cd ProjetWeb_2026

# Lancer la base de données, l'API et le frontend
docker compose up --build
```

- Frontend accessible sur : `http://localhost:4200`
- API accessible sur : `http://localhost:3000/api`
- Swagger UI accessible sur : `http://localhost:3000/docs`

---

### Option 2 : Lancement manuel

#### 1. Base de données PostgreSQL
Créer une base `cb_api` avec un utilisateur disposant des droits de création de tables :
```sql
CREATE DATABASE cb_api;
CREATE USER cb_api WITH PASSWORD 'cb_api';
GRANT ALL PRIVILEGES ON DATABASE cb_api TO cb_api;
```

#### 2. Backend (NestJS)
```bash
cd api
cp .env.example .env
npm install
npm run start:dev
```

#### 3. Frontend (Angular)
```bash
cd front
npm install
npx ng serve
```

---

## Variables d'environnement (Backend)

Fichier `api/.env` :

| Variable | Description | Valeur par défaut |
| :--- | :--- | :--- |
| `PORT` | Port d'écoute du serveur | `3000` |
| `DB_TYPE` | Type de base de données | `postgres` |
| `DB_HOST` | Hôte PostgreSQL | `localhost` |
| `DB_PORT` | Port PostgreSQL | `5432` |
| `DB_USER` | Utilisateur PostgreSQL | `cb_api` |
| `DB_PASSWORD` | Mot de passe PostgreSQL | `cb_api` |
| `DB_DATABASE` | Nom de la base de données | `cb_api` |
| `DB_SYNC` | Synchronisation TypeORM (dev) | `true` |
| `JWT_TOKEN_SECRET` | Clé secrète de signature des Access Tokens | — |
| `JWT_TOKEN_EXPIRE_IN` | Durée de validité des Access Tokens | `15m` |
| `JWT_REFRESH_TOKEN_SECRET` | Clé secrète des Refresh Tokens | — |
| `JWT_REFRESH_TOKEN_EXPIRE_IN` | Durée de validité des Refresh Tokens | `7d` |

---

## API Endpoints

### Sécurité
| Méthode | Route | Accès | Description |
| :--- | :--- | :--- | :--- |
| `POST` | `/api/security/sign-up` | Public | Inscription d'un nouvel utilisateur |
| `POST` | `/api/security/sign-in` | Public | Connexion et génération des tokens |
| `POST` | `/api/security/refresh` | Public | Renouvellement de l'access token |
| `GET` | `/api/security/me` | Authentifié | Profil de l'utilisateur connecté |

### Parkings
| Méthode | Route | Accès | Description |
| :--- | :--- | :--- | :--- |
| `GET` | `/api/parking/list` | Authentifié | Liste de tous les parkings |
| `GET` | `/api/parking/detail/:id` | Authentifié | Détail d'un parking spécifique |
| `POST` | `/api/parking/create` | Authentifié | Création d'un nouveau parking |
| `POST` | `/api/parking/update/:id` | Authentifié | Mise à jour des informations |
| `DELETE` | `/api/parking/delete/:id` | Authentifié | Suppression d'un parking |

### Tickets
| Méthode | Route | Accès | Description |
| :--- | :--- | :--- | :--- |
| `GET` | `/api/ticket/list` | Authentifié | Liste des tickets émis |
