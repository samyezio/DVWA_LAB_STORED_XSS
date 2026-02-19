
```md
# Diagramme — Correctif par encodage de l’output (htmlspecialchars)

```mermaid
sequenceDiagram
    autonumber
    participant U as Utilisateur
    participant W as Application Web (DVWA patchée)
    participant DB as Base de données
    participant B as Navigateur

    U->>W: Soumet un commentaire (peut contenir des balises)
    W->>DB: Stocke le texte (payload possible)
    B->>W: Ouvre la page Guestbook
    W->>DB: Récupère les commentaires
    DB-->>W: Renvoie les données brutes
    W->>W: Encode l'output (htmlspecialchars)
    W-->>B: Renvoie du HTML sûr (ex: &lt;script&gt;...&lt;/script&gt;)
    B->>B: Affiche le texte, aucune exécution JS