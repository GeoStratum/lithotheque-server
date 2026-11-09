# Stratégie de Sauvegarde & Reprise d'Activité (Disaster Recovery)

Ce document décrit les mécanismes de persistance des données géologiques, la stratégie de sauvegarde sans interruption et les procédures de restauration en cas d'incident matériel.

---

## 1. Modèle de Données & Mode SQLite WAL

Lithotheque Hub stocke l'intégralité du projet géologique unifié (dossiers, échantillons, mesures structurales, documents, couches SIG) dans un fichier SQLite consolidé (`litho_hub.db`).

Le serveur initialise SQLite avec les directives de haute concurrence :
```sql
PRAGMA journal_mode = WAL;
PRAGMA synchronous = NORMAL;
```

### Avantages du mode WAL (Write-Ahead Logging) :
* **Concurrence maximale** : Les géologues peuvent pousser leurs observations de terrain (`INSERT` / `UPDATE`) pendant que d'autres utilisateurs consultent la cartographie ou effectuent des calculs stéréonet (`SELECT`) sans aucun blocage mutuel.
* **Intégrité ACID** : Résistance aux coupures d'alimentation inopinées sur le camp de base.

---

## 2. Sauvegardes à Chaud Automatisées (Zero Data-Loss)

> [!CAUTION]
> **Ne jamais copier directement le fichier `litho_hub.db` avec un simple `cp` ou `copy` pendant que le serveur tourne**, car le fichier `litho_hub.db-wal` contient des transactions non encore vidées sur disque, ce qui peut corrompre la copie.

### Méthode 1 : Sauvegarde Programmée via Script Linux (Cron)
Le script fourni dans [`deploy/backup/backup-litho-db.sh`](file:///d:/Dev/Lithoteque/litho-hub/deploy/backup/backup-litho-db.sh) utilise la commande atomique `sqlite3 .backup` :

```bash
# Rendre le script exécutable
sudo chmod +x /usr/local/bin/backup-litho-db.sh

# Ajouter au crontab (exécution quotidienne à 02h00)
# sudo crontab -e
0 2 * * * /usr/local/bin/backup-litho-db.sh >> /var/log/litho-backup.log 2>&1
```

Le script effectue :
1. Un instantané consistant `litho_hub_backup_YYYYMMDD_HHMMSS.db`.
2. La compression immédiate au format `.gz`.
3. La purge automatique des sauvegardes ayant dépassé la période de rétention (30 jours par défaut).

### Méthode 2 : Sauvegarde Programmée sous Windows (Planificateur de Tâches)
Le script PowerShell [`deploy/backup/backup-litho-db.ps1`](file:///d:/Dev/Lithoteque/litho-hub/deploy/backup/backup-litho-db.ps1) appelle l'endpoint interne sécurisé `/api/admin/backup` :

```powershell
# Enregistrer la tâche planifiée quotidienne à 02h00
$Action = New-ScheduledTaskAction -Execute "PowerShell.exe" -Argument "-ExecutionPolicy Bypass -File C:\ProgramData\Lithotheque Hub\deploy\backup-litho-db.ps1"
$Trigger = New-ScheduledTaskTrigger -Daily -At 2am
Register-ScheduledTask -Action $Action -Trigger $Trigger -TaskName "LithoHub_Daily_Backup" -Description "Sauvegarde à chaud quotidienne de Lithotheque Hub" -User "NT AUTHORITY\SYSTEM"
```

---

## 3. Procédure de Restauration en Cas de Panne

En cas de défaillance matérielle ou de corruption de données :

1. **Arrêter le service Lithotheque Hub** :
   ```bash
   # Sous Linux
   sudo systemctl stop litho-hub

   # Sous Windows
   Stop-Service -Name LithoHubService
   ```

2. **Remplacer le fichier de base de données par la sauvegarde** :
   ```bash
   # Sous Linux
   gunzip -c /var/backups/litho-hub/litho_hub_backup_20261109_020000.db.gz > /etc/litho-hub/data/litho_hub.db
   rm -f /etc/litho-hub/data/litho_hub.db-wal /etc/litho-hub/data/litho_hub.db-shm

   # Vérifier les permissions
   sudo chown -R root:root /etc/litho-hub/data/
   ```

3. **Relancer le service** :
   ```bash
   # Sous Linux
   sudo systemctl start litho-hub

   # Sous Windows
   Start-Service -Name LithoHubService
   ```

4. **Vérifier l'intégrité** :
   Consulter immédiatement l'endpoint de diagnostic :
   ```bash
   curl -s http://127.0.0.1:8080/api/health
   # Réponse attendue : {"status":"ok","timestamp":...}
   ```
