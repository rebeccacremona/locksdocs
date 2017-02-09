clockss
=======
https://clockss.org/clockss/Home

Should we just join them?

clockss docs rocks!!
----------------------
all pages: https://documents.clockss.org/index.php?title=Special:AllPages

(This is a work in progress)


### https://documents.clockss.org/index.php?title=LOCKSS:_Basic_Concepts
>The LOCKSS system is sometimes criticized as providing only bit-level preservation, but this is a misunderstanding. The system employs exactly the same techniques (and in most cases exactly the same software tools) as other preservation systems, including:
>  Format identification, using FITS software https://documents.clockss.org/index.php?title=LOCKSS:_Format_Migration#Format_Migration_in_the_CLOCKSS_Archive
>  Format verification, using FITS software.
>  Format migration, see LOCKSS: Format Migration.
>  Metadata creation, see LOCKSS: Extracting Bibliographic Metadata and LOCKSS: Metadata Database.
>
>The difference between LOCKSS and most other preservation systems lies not in what techniques are employed but in when those techniques are employed. In the interest of economy, the LOCKSS system stores only the original bits, and delays all operations on them except integrity checking as long as possible. So, for example, unlike systems that preemptively migrate formats in bulk that are not yet obsolete into formats that are presumed to be less obsolete, thereby consuming processing resources, and store both the original and the migrated copies, thereby consuming storage resources, LOCKSS migrates formats only of individual files, and only when a read's request indicates that migration of that file is necessary. The migrated version is discarded when no longer needed to save on storage. This capability was demonstrated in 2005 but has remained unused in practice because the formats of content preserved in the LOCKSS system are not going obsolete.

More on this, likely useful for the discussion with our board about the word "archive", is here: https://documents.clockss.org/index.php?title=LOCKSS:_Format_Migration

### https://documents.clockss.org/index.php?title=CLOCKSS:_Designated_Community

>The CLOCKSS archive is a dark archive. No Consumers "interact with [CLOCKSS] services to find preserved information of interest and to access that information in detail". Content is not disseminated in response to requests from Consumers, but only as a consequence of the CLOCKSS Board declaring a trigger event. It is not disseminated to Consumers, but to re-publishing systems that are not themselves part of the Archive. The dissemination process is a transaction between the CLOCKSS archive and a re-publishing platform; it does not depend on a request from a Consumer nor does it deliver Content Information directly to a Consumer. If any Consumer ever accesses content that was preserved by the CLOCKSS archive, it will be from one of these re-publishing systems. The CLOCKSS archive will not be involved in this transaction, between the eventual Consumer and the re-publishing system, in any way.

>The CLOCKSS Archive delivers services to each of its Interested Parties by providing a sustainable, geographically distributed dark archive that ensures the long-term survival of Web-based, scholarly publications

_this sounds a lot like our idea of what the Perma LOCKSS network will be_

### Hardware, Software, Network, Ops

https://documents.clockss.org/index.php?title=CLOCKSS:_Hardware_and_Software_Inventory
https://documents.clockss.org/images/2/22/CLOCKSS_inventory.png
https://documents.clockss.org/index.php?title=CLOCKSS:_Box_Operations
https://documents.clockss.org/index.php?title=LOCKSS:_Property_Server_Operations

_This setup sounds sophisticated and robust, and these specs sound more modern and realistic than those recommended elsewhere. Let's maintain a spreadsheet like they do. Also, note they use both virtual machines and physical servers._

_The Box Operations wiki page also includes installation instructions, including some details on how to configure RAID, health monitoring, and other details, which differ from those we have seen so far. Let's look at this closely soon._

TODO: look much more closely at their setup and config.

### Security, Reliability, and Maintainability

https://documents.clockss.org/index.php?title=CLOCKSS:_Threats_and_Mitigations

As above, seems like an impressive analysis.

TODO: look much more closely at what they are doing.

### Record Keeping

https://documents.clockss.org/index.php?title=CLOCKSS:_Logging_and_Records

As above, seems impressive.

TODO: look much more closely at what they are doing.

### About Polling and Repair

https://documents.clockss.org/index.php?title=LOCKSS:_Polling_and_Repair_Protocol

_Good to read soon._

### AUs

_CLOCKSS does one AU per year_

https://documents.clockss.org/index.php?title=Definition_of_AIP
>At the start of each new year an AU for the new year's SIPs from each publisher supplying content via file transfer is configured on the production CLOCKSS boxes via the CLOCKSS property server. The AU's plugin is configured to start crawling from the automatically generated manifest page of that year's directory for that publisher on the staging server at regular intervals. Each time the box crawls, it adds content and metadata from newly received SIPs to the box's copy of the AU. In this case the files in the AIPs have names reflecting the files in the SIPs on the staging server. The publisher's names or URLs for these files are contained in, or recoverable from metadata in the SIPs/AIPs.

