from clockss doc
================
CLOCKSS and LOCKSS??? CLOCKSS has very clearly written documentation.

https://documents.clockss.org/index.php?title=Special%3APrefixIndex&prefix=CLOCKSS&namespace=0

### https://documents.clockss.org/index.php?title=LOCKSS:_Basic_Concepts
The LOCKSS system is sometimes criticized as providing only bit-level preservation, but this is a misunderstanding. The system employs exactly the same techniques (and in most cases exactly the same software tools) as other preservation systems, including:
  Format identification, using FITS software https://documents.clockss.org/index.php?title=LOCKSS:_Format_Migration#Format_Migration_in_the_CLOCKSS_Archive
  Format verification, using FITS software.
  Format migration, see LOCKSS: Format Migration.
  Metadata creation, see LOCKSS: Extracting Bibliographic Metadata and LOCKSS: Metadata Database.

The difference between LOCKSS and most other preservation systems lies not in what techniques are employed but in when those techniques are employed. In the interest of economy, the LOCKSS system stores only the original bits, and delays all operations on them except integrity checking as long as possible. So, for example, unlike systems that preemptively migrate formats in bulk that are not yet obsolete into formats that are presumed to be less obsolete, thereby consuming processing resources, and store both the original and the migrated copies, thereby consuming storage resources, LOCKSS migrates formats only of individual files, and only when a read's request indicates that migration of that file is necessary. The migrated version is discarded when no longer needed to save on storage. This capability was demonstrated in 2005 but has remained unused in practice because the formats of content preserved in the LOCKSS system are not going obsolete.
