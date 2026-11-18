
# Pol-eat-heck — Base de données

## Équipe

| Nom | GitHub | Partie |
|---|---|---|
| YOUNES Ralph | [RalphYounes](https://github.com/RalphYounes) | Base de données |
| SID Ryan | [kyp022](https://github.com/kyp022) | Backend |
| M'HANNIHA Zyad | [AKaz23](https://github.com/AKaz23) | Frontend |

## Dépôts GitHub

| Partie | Nom du dépôt | Lien |
|---|---|---|
| Base de données | 4a-isi-projet-db | [GitHub](https://github.com/RalphYounes/4a-isi-projet-db) |
| Backend | 4a-isi-projet-back | [GitHub](https://github.com/kyp022/4a-isi-projet-back) |
| Frontend | 4a-isi-projet-front-akaz | [GitHub](https://github.com/AKaz23/4a-isi-projet-front-akaz) |

## Diagramme de la base de données (ERD)

On est partis sur **8 tables** pour gérer les utilisateurs, les produits, les menus, les disponibilités, les fermetures et les réservations. Voici notre diagramme :

```mermaid
erDiagram
    USERS ||--o{ RESERVATIONS : passe
    RESERVATIONS ||--|{ RESERVATION_ITEMS : contient
    PRODUCTS |o--o{ RESERVATION_ITEMS : reserve_comme_produit
    MENUS |o--o{ RESERVATION_ITEMS : reserve_comme_menu
    MENUS ||--o{ MENU_PRODUCTS : contient
    PRODUCTS ||--o{ MENU_PRODUCTS : compose
    MENUS ||--o{ MENU_AVAILABILITY : dispose_de

    USERS {
        int id PK
        string username UK
        string password_hash
        string role
        boolean is_blocked
        datetime created_at
    }

    PRODUCTS {
        int id PK
        string name
        string description
        int price_cents
        int stock_quantity
        boolean is_available
        boolean is_active
    }

    MENUS {
        int id PK
        string name
        string description
        int price_cents
        boolean is_active
    }

    MENU_PRODUCTS {
        int menu_id PK,FK
        int product_id PK,FK
        int quantity
    }

    MENU_AVAILABILITY {
        int id PK
        int menu_id FK
        int weekday "nullable"
        date start_date "nullable"
        date end_date "nullable"
    }

    CLOSURE_DAYS {
        date closed_date PK
        string reason "nullable"
    }

    RESERVATIONS {
        int id PK
        int user_id FK
        date pickup_date
        string status
        datetime created_at
    }

    RESERVATION_ITEMS {
        int id PK
        int reservation_id FK
        int product_id FK "nullable"
        int menu_id FK "nullable"
        int quantity
        int unit_price_cents
        string item_name_snapshot
    }
```


Quelques points importants :
- Un menu peut contenir plusieurs produits, d'où la table `MENU_PRODUCTS`.
- Une réservation peut contenir plusieurs produits ou menus. Chaque ligne de `RESERVATION_ITEMS` correspond à **un produit ou un menu**, pas les deux.
- `MENU_AVAILABILITY` sert à gérer les jours et périodes où un menu est proposé.
- `CLOSURE_DAYS` n'a pas de lien direct avec une autre table : les jours de fermeture concernent tout l'établissement.
- Les mots de passe seront stockés sous forme de hash et les utilisateurs bloqués ne pourront pas réserver.

Pour l'instant, c'est le **modèle de la base de données**. La création des tables SQLite et leur utilisation avec SQLModel seront faites à partir de ce schéma.