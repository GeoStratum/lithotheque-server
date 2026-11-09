# Dictionnaire des Codes d'Erreurs du Serveur & Guide de Résolution

Ce document recense l'intégralité des codes d'erreurs normalisés émis par **Lithotheque Base Camp Hub** (dans les journaux `systemd` / Windows Event Log et dans les réponses JSON de l'API REST), ainsi que la procédure immédiate de résolution opérationnelle.

---

## 1. Format Standard des Réponses d'Erreurs API

Lorsqu'une requête échoue, le serveur retourne une structure JSON standardisée :

```json
{
  "code": "ERR_LIC_2004",
  "message": "Limite de jauge de pairs simultanés atteinte pour cette licence",
  "resolution": "Augmentez le quota de terminaux mobiles autorisés ou contactez GeoStratum pour étendre votre licence.",
  "status": 403
}
```

---

## 2. Nomenclature & Catalogue des Erreurs

### A. Configuration & Initialisation Système (`ERR_CFG_1000+`)

| Code | Message | Cause Principale | Procédure de Résolution |
| :--- | :--- | :--- | :--- |
| `ERR_CFG_1001` | *Format de configuration JSON invalide* | Erreur de syntaxe dans `config.json` (virgule, accolade manquante). | Valider la syntaxe JSON avec un linter (`jq . config.json`). |
| `ERR_CFG_1002` | *Fichier de configuration introuvable* | Le paramètre `--config` pointe vers un chemin inexistant. | Vérifier que `/etc/litho-hub/config.json` ou `C:\ProgramData\Lithotheque Hub\config.json` existe. |
| `ERR_CFG_1003` | *Permissions insuffisantes sur le répertoire de données* | L'utilisateur exécutant le service n'a pas les droits en écriture sur `data_dir`. | Sous Linux : `chown -R root:root /etc/litho-hub/data && chmod 750 /etc/litho-hub/data`. |
| `ERR_CFG_1004` | *Port réseau déjà alloué* | Le port `8080` (HTTP) ou `51820` (WireGuard UDP) est déjà utilisé par un autre service. | Exécuter `ss -tulpn \| grep 8080` ou `netstat -ano \| findstr 8080` et réassigner le port dans `config.json`. |

---

### B. Contrôle de Licence Hors-Ligne Ed25519 (`ERR_LIC_2000+`)

| Code | Message | Cause Principale | Procédure de Résolution |
| :--- | :--- | :--- | :--- |
| `ERR_LIC_2001` | *Fichier de licence introuvable* | Le fichier `license.key` est absent au démarrage. | Déposer le fichier de licence fourni par GeoStratum dans le dossier racine du serveur. |
| `ERR_LIC_2002` | *Signature Ed25519 corrompue ou invalide* | Le fichier a été altéré manuellement ou provient d'une clé privée non maîtresse. | Remplacer le fichier par l'original émis cryptographiquement avec `master_private.key`. |
| `ERR_LIC_2003` | *Licence expirée* | La date de validité `expires_at` est échue (licences temporaires de terrain). | Renouveler la licence de mission géologique ou solliciter une licence perpétuelle. |
| `ERR_LIC_2004` | *Quota maximal de terminaux mobiles atteint* | Nombre de smartphones Android connectés simultanément supérieur à `max_peers`. | Déconnecter les sessions inactives ou augmenter le quota matériel de la licence. |

---

### C. Base de Données SQLite WAL & Stockage (`ERR_DB_3000+`)

| Code | Message | Cause Principale | Procédure de Résolution |
| :--- | :--- | :--- | :--- |
| `ERR_DB_3001` | *Échec d'initialisation de la base SQLite* | Fichier de base SQLite verrouillé par un processus tiers ou corrompu. | Vérifier qu'aucun autre exécutable n'ouvre `litho_hub.db` en mode exclusif. |
| `ERR_DB_3002` | *Timeout de verrouillage SQLite (Busy)* | Concurrence d'écriture extrême sans mode WAL activé. | S'assurer que les pragmas `journal_mode=WAL` sont bien appliqués au montage. |
| `ERR_DB_3003` | *Échec de la sauvegarde à chaud VACUUM INTO* | Espace disque insuffisant dans le dossier `backups` ou droits en écriture refusés. | Libérer de l'espace sur la partition et vérifier les quotas disque. |
| `ERR_DB_3004` | *Espace disque saturé* | Le disque hôte est plein suite à l'accumulation de photos terrain. | Purger les conteneurs temporaires ou étendre le volume de stockage. |

---

### D. Réseau & Synchronisation WireGuard (`ERR_NET_4000+`)

| Code | Message | Cause Principale | Procédure de Résolution |
| :--- | :--- | :--- | :--- |
| `ERR_NET_4001` | *Échec initialisation WireGuard UDP* | Impossible d'ouvrir le socket UDP :51820. | Vérifier que le module noyau WireGuard est présent ou autoriser l'implémentation user-space. |
| `ERR_NET_4002` | *Privilèges réseau insuffisants* | Absence de `CAP_NET_ADMIN` sous Linux ou droits administrateur sous Windows. | Vérifier les directives `AmbientCapabilities` dans le fichier `systemd`. |
| `ERR_NET_4003` | *Conteneur .litho corrompu* | Le transfert de synchronisation mobile a été interrompu en cours de route. | Relancer la synchronisation depuis l'application mobile Lithothèque (reprise sur erreur). |
| `ERR_NET_4004` | *Incohérence d'empreinte SHA-256* | Altération de données lors du transit radio/Wi-Fi de terrain. | Recommencer l'envoi du paquet chiffré depuis le smartphone émetteur. |

---

### E. Moteur de Calcul Géologique & Stéréonet (`ERR_GEO_5000+`)

| Code | Message | Cause Principale | Procédure de Résolution |
| :--- | :--- | :--- | :--- |
| `ERR_GEO_5001` | *Angles structuraux hors limites* | Valeurs reçues : `dip < 0` ou `> 90°`, ou `strike < 0` ou `> 360°`. | Recalibrer la boussole/clinomètre du smartphone ou rectifier la saisie manuelle. |
| `ERR_GEO_5002` | *Rayon de projection nul ou négatif* | Paramètre `radius <= 0` dans la requête de calcul. | Renseigner un rayon positif (valeur par défaut : `95.0`). |
