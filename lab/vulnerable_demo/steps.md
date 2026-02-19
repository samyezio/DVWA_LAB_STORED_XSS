## Stored XSS – Reproduction (DVWA Low)

1. Login DVWA : admin / password
2. DVWA Security → mettre `Low`
3. XSS stored → injecter :
   `<script>alert('Stored XSS')</script>`
4. Recharger la page → popup = preuve de Stored XSS

### Captures associées
- 01_security_low.png
- 02_payload_inserted_XSS.png
- 03_alert_proof.png
