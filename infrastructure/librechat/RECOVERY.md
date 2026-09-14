# Recovery and rollback — NOT TESTED

A source/config backup alone is not a verified application backup.

## Disposable restoration

1. Verify backup checksums and Git bundle. Create an isolated temporary checkout at the recorded tag, with copied configuration and fresh destination data directories/volumes. Never mount the pilot's live data.
2. Create a separate Compose project. Explicitly override all inherited container_name values (project names alone do not isolate fixed upstream names). Use localhost test ports such as 13080/13000 after checking they are free. Verify database/search ports remain unpublished. Use recorded image digests. Disable Skill Sync and all outbound tools in the restored instance.
3. Start only the isolated MongoDB/search containers. Restore using `mongorestore --archive --gzip --drop` INSIDE THE DISPOSABLE MONGO ONLY, feeding the archived dump. Validate exit status and source/destination collection counts.
4. Restore persistent uploads/images/skill and configuration with appropriate ownership. Preserve encryption/JWT secrets privately. Start the isolated app/admin; verify login, both conversations and content, Project membership, file hashes and search. Restart and repeat. Never make AI calls with restored credentials just to test restoration.
5. Record evidence and delete only the explicitly disposable resources after checking their names and paths. Do not run broad prune or volume deletion.

## Rollback

Before deployment, rollback is simply to leave LibreChat stopped; keep prepared files for investigation. Once running, use `docker compose stop` from the verified pilot directory to halt it without deleting data. Do not use down -v.

For an upgrade failure, stop upgraded services, preserve the failed data for diagnosis, restore the last verified backup into fresh isolated data paths, use the previous source commit/config/image digests, validate there, then switch local ports. Do not run an old application against a migrated database without proving compatibility. Re-run all acceptance tests before resuming use.
