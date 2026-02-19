# Diagramme — Stored XSS (flux vulnérable)

```mermaid
sequenceDiagram
    autonumber
    participant A as Attaquant
    participant W as Application Web (DVWA)
    participant DB as Base de données
    participant V as Victime (Navigateur)

    A->>W: Envoie un commentaire contenant un payload (ex: <script>alert()</script>)
    W->>DB: Stocke le commentaire tel quel (sans encodage HTML)
    V->>W: Ouvre la page Guestbook (XSS Stored)
    W->>DB: Lit les commentaires stockés
    DB-->>W: Renvoie le commentaire (payload inclus)
    W-->>V: Réponse HTML contenant le payload non encodé
    V->>V: Le navigateur exécute le JavaScript injecté