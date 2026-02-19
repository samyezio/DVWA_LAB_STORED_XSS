
```md
# Diagramme — Défense en profondeur contre XSS

```mermaid
flowchart TD
    A[Entrée utilisateur] --> B{Validation côté serveur}
    B -->|OK| C[Stockage en DB]
    C --> D[Encodage à l'affichage<br/>(output encoding)]
    D --> E[Réponse HTML sûre]

    E --> F{CSP activée ?}
    F -->|Oui| G[Le navigateur bloque beaucoup de scripts injectés]
    F -->|Non| H[Plus de risque si un inject passe]

    E --> I[Cookies: HttpOnly/SameSite/Secure<br/>réduisent l'impact]