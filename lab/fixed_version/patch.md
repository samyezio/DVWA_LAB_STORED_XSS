## Correctif (output encoding)

### Cause
Le contenu utilisateur est affiché sans encodage HTML → le navigateur l’interprète comme du code.

### Localisation du code
Fonction `dvwaGuestbook()` dans `dvwaPage.inc.php`.

### Patch
**Avant (vulnérable - affichage brut) :
```php
$name = $row[0];
$comment = $row[1];

**Après (fix - affichage encodé) :

```php
$name    = htmlspecialchars($row[0], ENT_QUOTES, 'UTF-8');
$comment = htmlspecialchars($row[1], ENT_QUOTES, 'UTF-8');

### Résultat 

Le payload est affiché comme texte (ex: <script>...</script> ou &lt;script&gt;...) et ne s’exécute plus.

## Captures associées

-01_DvwaPage_inc_php_PATH

-02_GUESTBOOK_BEFORE.png

-03_GUESTBOOK_AFTER.png

-04_low_result_after_fix.png

