# strings_findings.md — Analyse sécurité de strings.xml
**Application :** com.fpe.comptenickel (Nickel)  
**Fichier analysé :** `res/values/strings.xml`  
**Date :** 29/04/2026  
**Analyste :** Audit MobSF

---

## FINDINGS

---

### CRITIQUE — Clé API Google exposée

| Champ | Valeur |
|-------|--------|
| **Clé** | `google_api_key` / `google_crash_reporting_api_key` |
| **Extrait** | `AIzaSyBTgztvImsUfMWDa41PCrDWAj7dmyIDhUg` |
| **Ligne** | 101 & 103 |

**Risque :** Clé Firebase/Google API en clair, réutilisable par un attaquant pour consommer des quotas, accéder aux services Firebase associés (Crashlytics, Auth, Storage), voire exfiltrer des données si les règles Firebase sont permissives.


---

### CRITIQUE — URL Firebase Realtime Database exposée

| Champ | Valeur |
|-------|--------|
| **Clé** | `firebase_database_url` |
| **Extrait** | `https://application-client-nickel.firebaseio.com` |
| **Ligne** | 99 |

**Risque :** URL de la base Firebase en clair. Si les règles de sécurité Firebase ne sont pas correctement configurées (lecture publique), un attaquant peut lire ou écrire des données clients. Corrélé avec F-01 : la clé API permet l'accès direct.

---

### HAUTE — Endpoints API métier exposés en clair

| Champ | Valeur |
|-------|--------|
| **Clés** | `ACCOUNT_ENDPOINT`, `CUSTOMER_AUTHENTICATION_ENDPOINT`, `PERSONAL_SPACE_API_ENDPOINT` |
| **Extrait** | `https://api.nickel.eu/customer-banking-api` — `https://api.nickel.eu/customer-authentication-api` |
| **Lignes** | 3, 4, 7 |

**Risque :** Les URLs des APIs sensibles (authentification client, espace personnel, opérations bancaires) sont listées en clair. Un attaquant peut cartographier l'API backend et cibler directement ces endpoints (fuzzing, attaque par replay, BOLA/IDOR).

---

### HAUTE — Environnement de production déclaré explicitement

| Champ | Valeur |
|-------|--------|
| **Clé** | `ENVIRONMENT` |
| **Extrait** | `production` |
| **Ligne** | 5 |

**Risque :** Confirme à un attaquant qu'il analyse bien le build de production. Facilite le ciblage et exclut tout doute sur l'environnement. Aucun flag debug/staging ne masque la valeur.

---

### HAUTE — Identifiant Pushwoosh exposé

| Champ | Valeur |
|-------|--------|
| **Clé** | `PUSHWOOSH_APPID` |
| **Extrait** | `DDF11-D1BF9` |
| **Ligne** | 8 |

**Risque :** L'App ID Pushwoosh permet à un attaquant d'envoyer des notifications push arbitraires à tous les utilisateurs de l'application (phishing, désinformation, ingénierie sociale) via l'API Pushwoosh si elle n'est pas protégée par un secret serveur.

---

### MOYENNE — Numéro de projet GCM / Sender ID exposé

| Champ | Valeur |
|-------|--------|
| **Clés** | `GCM_PROJECT_NUMBER`, `gcm_defaultSenderId`, `google_app_id` |
| **Extrait** | `246766419677` — `717748501407` — `1:717748501407:android:e0cdeb712...` |
| **Lignes** | 6, 100, 102 |

**Risque :** Le Sender ID GCM/FCM et le Project Number permettent d'identifier le projet Firebase. Combinés à la clé API (F-01), ils permettent l'accès complet aux services Firebase du projet.

---

### MOYENNE — Build ID Crashlytics exposé

| Champ | Valeur |
|-------|--------|
| **Clé** | `com.crashlytics.android.build_id` |
| **Extrait** | `9ad6d741-4e10-4abd-9f12-163a0e3fb0b2` |
| **Ligne** | 73 |

**Risque :** UUID de build Crashlytics en clair. Permet d'identifier précisément la version de build auprès de Firebase/Crashlytics. Peut faciliter le ciblage de versions spécifiques avec des vulnérabilités connues.

---

### MOYENNE — Google Storage Bucket exposé

| Champ | Valeur |
|-------|--------|
| **Clé** | `google_storage_bucket` |
| **Extrait** | `application-client-nickel.appspot.com` |
| **Ligne** | 104 |

**Risque :** Nom du bucket Firebase Storage en clair. Si les règles Storage Firebase ne sont pas correctement configurées, un attaquant peut lister, lire ou écrire des fichiers dans le bucket (documents clients, pièces justificatives…).

---
