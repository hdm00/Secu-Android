# firebase_check.md — Test d'accès public Firebase
**Application :** com.fpe.comptenickel (Nickel)  
**URL testée :** `https://application-client-nickel.firebaseio.com`  

---

## RÉSULTAT GLOBAL : BASE NON ACCESSIBLE PUBLIQUEMENT

---

## Captures de réponse

### Test 1 — Racine de la base
```
GET https://application-client-nickel.firebaseio.com/.json?shallow=true

HTTP/2 403
x-deny-reason: host_not_allowed
content-type: text/plain

Body : "Host not in allowlist"
```

### Test 2 — Chemins courants testés
```
[403] /.json       => Host not in allowlist
[403] /users.json  => Host not in allowlist
[403] /customers.json => Host not in allowlist
[403] /accounts.json  => Host not in allowlist
[403] /config.json    => Host not in allowlist
```

### Test 3 — Headers complets
```
HTTP/2 403
x-deny-reason: host_not_allowed
content-length: 21
content-type: text/plain
date: Wed, 29 Apr 2026 12:07:41 GMT
```

---

## Analyse du résultat

| Critère | Statut | Détail |
|---------|--------|--------|
| Accès public en lecture | Bloqué | HTTP 403 sur tous les chemins |
| Accès public en écriture | Bloqué | HTTP 403 sur tous les chemins |
| Restriction par domaine (allowlist) | Active | Header `x-deny-reason: host_not_allowed` |
| Firebase App Check | Non confirmé | Peut être App Check ou règle Realtime DB |
| Données exposées | Aucune | Aucun contenu retourné |

---

## Conclusion

> La base n'est pas accessible directement, mais la présénce de l'URL en clair dans l'application est une vulnérabilité qui peut amener à une compromission.
