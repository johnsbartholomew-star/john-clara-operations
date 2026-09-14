# Backup procedure — NOT VERIFIED

Do not call backups verified until a disposable restore has passed RECOVERY.md.

1. Create a timestamped private backup directory outside Git (`umask 077`). Record source tag/commit, operations Git commit, dirty status, Docker/Compose versions and every running service's image ID/RepoDigest. Record port bindings and named/bind volumes, avoiding container environment values.
2. Pause app writes with `docker compose stop api admin-panel`; leave MongoDB running. Run `docker compose exec -T mongodb mongodump --db LibreChat --archive --gzip > "$BACKUP_DIR/mongodb.archive.gz"`. Check command exit and nonempty archive. Do not copy a live MongoDB data directory.
3. Copy local .env, librechat.yaml and override into the private backup. Preserve secrets so encrypted credentials remain decryptable. Archive persistent images/, uploads/ and skill/ while app writes are stopped; inventory any additional configured persistent paths. Back up logs if useful, treating them as private. Search indexes may be rebuilt from MongoDB; document/rehearse reindexing during restore.
4. Use `git bundle create "$BACKUP_DIR/operations.bundle" --all` in the operations checkout after recording dirty changes. GitHub is canonical; backups supplement it. Record upstream source commit and include tracked configuration.
5. Compute SHA-256 checksums for every archive/config/bundle and verify them with a separate read. Encrypt the backup using an existing approved backup mechanism and keep a second private copy; no new cloud destination is authorized here.
6. Restart api/admin even if backup fails, using a shell cleanup trap in any future automated implementation. Mark a failed backup unusable.
7. Perform the disposable restore test. Record evidence, date, backup hashes, source and destination counts, and login/conversation/search/file checks. Only then label this backup VERIFIED.

Before each upgrade: record version/digests; review upstream changelog and breaking changes; take and restore-test backup; update explicit pins; run every baseline test; roll back on failure. No floating-image auto-updates.
