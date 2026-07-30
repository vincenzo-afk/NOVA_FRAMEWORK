# Backup

Automatic local backup on a rolling schedule plus before every schema migration; optional encrypted remote backup, opt-in only, per `docs/29-product/privacy.md`. Backups are verified (test-restored) periodically, not just written and assumed good.

## Deletion propagation to backups

A record deleted via `docs/04-memory/memory-garbage-collection.md` is
removed immediately from the live store and from every backup taken
from that point forward. It is not surgically removed from
already-existing backup snapshots taken before the deletion — those
age out naturally as the rolling schedule replaces them, per this
document's retention window. This means a deleted record can still
exist in an old backup snapshot until that snapshot's natural rotation,
which is the honest bound `docs/29-product/privacy.md`'s "purges it,
including from backups" commitment actually means: purged from the live
system and all future backups immediately, and from historical backups
within one full rotation of the backup retention window, not
instantaneously from every snapshot ever taken.
