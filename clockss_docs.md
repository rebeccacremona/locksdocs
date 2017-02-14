clockss docs rocks!!
====================
all pages: https://documents.clockss.org/index.php?title=Special:AllPages

(This is a work in progress)

https://documents.clockss.org/index.php?title=LOCKSS:_Basic_Concepts
---------------------------------------------------------------------
>The LOCKSS system is sometimes criticized as providing only bit-level preservation, but this is a misunderstanding. The system employs exactly the same techniques (and in most cases exactly the same software tools) as other preservation systems, including:
>   -  Format identification, using FITS software https://documents.clockss.org/index.php?title=LOCKSS:_Format_Migration#Format_Migration_in_the_CLOCKSS_Archive
>   -  Format verification, using FITS software.
>   -  Format migration, see LOCKSS: Format Migration.
>   -  Metadata creation, see LOCKSS: Extracting Bibliographic Metadata and LOCKSS: Metadata Database.
>
>The difference between LOCKSS and most other preservation systems lies not in what techniques are employed but in when those techniques are employed. In the interest of economy, the LOCKSS system stores only the original bits, and delays all operations on them except integrity checking as long as possible. So, for example, unlike systems that preemptively migrate formats in bulk that are not yet obsolete into formats that are presumed to be less obsolete, thereby consuming processing resources, and store both the original and the migrated copies, thereby consuming storage resources, LOCKSS migrates formats only of individual files, and only when a read's request indicates that migration of that file is necessary. The migrated version is discarded when no longer needed to save on storage. This capability was demonstrated in 2005 but has remained unused in practice because the formats of content preserved in the LOCKSS system are not going obsolete.

More on this, likely useful for the discussion with our board about the word "archive", is here: https://documents.clockss.org/index.php?title=LOCKSS:_Format_Migration

Hardware, Software, Network, Ops
--------------------------------
_(Why do they have separate "ingest" machines and "production" machines?)_
https://documents.clockss.org/index.php?title=CLOCKSS:_Box_Operations

#### Recommended Specs
<table class="wikitable" style="margin: 0 auto">

<tbody><tr>
<th scope="col"> Item
</th>
<th scope="col"> Ingest
</th>
<th scope="col"> Production
</th>
<th scope="col"> Triggered (VMware)
</th></tr>
<tr>
<td> Chassis
</td>
<td> Supermicro 2U
</td>
<td> Supermicro 4U
</td>
<td> <i>N/A</i>
</td></tr>
<tr>
<td> Disk Bays
</td>
<td> 12
</td>
<td> 24
</td>
<td> <i>N/A</i>
</td></tr>
<tr>
<td> Processor
</td>
<td> AMD Operton 6128 (eight cores)
</td>
<td> Dual Xeon E5504 (eight cores)
</td>
<td> Dual-core CPU
</td></tr>
<tr>
<td> RAM
</td>
<td> 16GB ECC
</td>
<td> 24GB ECC
</td>
<td> 4GB
</td></tr>
<tr>
<td> Disk
</td>
<td> 8 x 3TB SATA 6Gbps 7200RPM
</td>
<td> 12 x 2TB SATA 3Gbps
</td>
<td> 40GB disk
</td></tr>
<tr>
<td> RAID
</td>
<td> LSI Megaraid
</td>
<td> Software (Linux <tt>mdadm</tt>)
</td>
<td> None (Underlying RAID array)
</td></tr>
<tr>
<td> Network
</td>
<td> Onboard dual Gbe
</td>
<td> Onboard dual Gbe
</td>
<td> 10/100Mb NIC
</td></tr>
<tr>
<td> Remote Access
</td>
<td> IPMI
</td>
<td> IPMI
</td>
<td> VMware vSphere Client
</td></tr>
<tr>
<td> Power Supply
</td>
<td> 800W redundant
</td>
<td> 1600W redundant
</td>
<td> <i>N/A</i>
</td></tr>
</tbody></table>

> Currently, all CLOCKSS boxes have at least four spare hotswap disk bays. A natural way to incrementally upgrade the disk capacity of deployed CLOCKSS boxes is to fill these disk bays with the largest disk available (at the moment, 4TB), create a new RAID array and finally to move content from an existing array. The disks comprising the old array can then be removed and the process can be repeated for the remaining RAID arrays.

> All production CLOCKSS boxes were configured so that its operating system filesystem is independent from its content filesystems."

> CLOCKSS virtual machines run on servers running the VMWare vSphere ESXi 5 hypervisor.

#### RAID Configuration
> The CLOCKSS Ingest machines have LSI MegaRAID based hardware RAID controllers and eight 3TB disks. These disks were partitioned into groups of four and put into a RAID5 configuration. Each RAID array then yields approximately 8.1TB of usable space after RAID and EXT4 filesystem overhead. Since the Ingest machines have an extra disk dedicated for the OS (called a "system disk"), the full space of the RAID arrays is dedicated to the LOCKSS daemon. This configuration also isolates problems on the system disk from content storage arrays.

> The CLOCKSS Production machines utilize the Linux kernel's mdadm module to implement software RAID. Similar to the CLOCKSS Ingest machines, the Production machines also have several RAID5 arrays consisting of three or four disks depending on the number of disks in the machine. In contrast to the Ingest machines, these RAID5 arrays are used by both the operating system and the LOCKSS daemon.

#### RAID Health Monitoring
> The most common component failures are disks and it is expected that all CLOCKSS boxes will experience a few disk failures over the course of their service life. The RAID5 arrays employed in CLOCKSS boxes are only able to tolerate one disk failure each. It is thus important that any failed disks be found and replaced as quickly as possible; a double disk failure requires repair from another box via the LOCKSS: Polling and Repair Protocol, which is much slower than RAID rebuild.

> The RAID array health on all CLOCKSS Production boxes are monitored by Nagios via the Nagios Remote Plugin Executor (NRPE). Each machine runs an NRPE daemon and has a copy of a custom plugin we wrote to monitor mdadm RAID arrays. NRPE executes the plugin on behalf of Nagios and returns the result. If Nagios does not receive an OK, it sends an alert to CLOCKSS engineers.

Custom Nagios plugins were written to enable Nagios to monitor the following LOCKSS daemon services:
-  LOCKSS Daemon Version
-  LOCKSS Daemon Uptime
-  LOCKSS Repository Spaces (to monitor disk usage)
-  LOCKSS Web Administrative UI accessibility
-  LCAP Accessibility
Plugins shipped with Nagios allow us to monitor, where applicable:
-  OpenSSH accessibility
-  HTTP/HTTPS accessibility

> CLOCKSS and LOCKSS have a second Nagios instance running in an Amacon EC2 instance located in West Virginia. The second Nagios is not able to monitor CLOCKSS boxes directly. This is by design: We do not want a machine outside of the Stanford network to have the type of access necessary to monitor internal CLOCKSS boxes and processes. Due to this restriction, it is configured only to monitor the CLOCKSS website and the Nagios instance running at Stanford. The second Nagios instance will alert CLOCKSS engineers should any problems occur at Stanford.

### Installation and configuration
Qualitatively different than what we've seen in other places. Centos, Java, Daemon config, debug tools, pln:
[https://documents.clockss.org/index.php?title=CLOCKSS:_Box_Operations#Software_Bringup](https://documents.clockss.org/index.php?title=CLOCKSS:_Box_Operations#Software_Bringup)

### Planned CLOCKSS Box Replacement
> Despite the upgradability of existing hardware, we recognize that, eventually, it will no longer be cost effective or practical to continue to run old hardware due to increases in performance per watt efficiency, disk bandwidth, disk capacity and other measures. Software and content volume is also expected to push hardware towards obsolescence.
>
> Replacing an entire machine is a fairly straightforward process. After purchasing and configuring the machine as described elsewhere in this document, it is brought up alongside the machine it is to replace. To minimize downtime, content is copied in two passes, using rsync. The first pass copies the bulk of the data to the new machine. Once the first pass is complete, the LOCKSS daemon on the old machine is taken offline and its filesystems are placed into read-only mode. The second pass then copies any changes to the content that occurred during the first pass, and verifies the integrity of content from the first pass. If no errors occurred during the two passes, the old machine is taken offline and the new machine takes its place. Subsequent LOCKSS: Polling and Repair Protocol will also verify the integrity of the copy.

### Updates
_I'm not sure we want to do this._

> LOCKSS Daemon Updates: All remote sites are encouraged to setup automatic updates for the LOCKSS daemon package so that updates are installed automatically within a few hours of their release to the CLOCKSS RPM repository. Additionally, all systems administrators receive announcements of newly released daemons through the CLOCKSS Administrators mailing list; systems administrators opting to install updates manually are encouraged to use the announcements as a cue to install updates. We use Nagios to monitor the LOCKSS daemon version of all CLOCKSS boxes. If a CLOCKSS box has not been updated with the latest LOCKSS daemon within a day, we notify its systems administrator(s).

>  System Package Updates: Most CLOCKSS boxes are configured for unattended upgrade, so new upgrades will be installed automatically. All CLOCKSS box systems administrators are encouraged to join the CentOS Announce mailing list to receive real-time annoucements about security updates. If the box in question is not configured for unattended updates:
>
>  -  Security updates should be downloaded and installed as soon as they are announced.
>  -  All other non-critical updates should be installed at least once a quarter.
>
> Anyone can subscribe to the CentOS Announce mailing list by visiting the CentOS-announce Mailing List home page.

> System Updates: During the lifetime of the CLOCKSS program, the CentOS project will release new major versions of their CentOS operating system (e.g., CentOS 5.x to 6.x). All production CLOCKSS boxes were configured so that its operating system filesystem is independent from its content filesystems. The CentOS upgrade path is then relatively simple: Save the LOCKSS daemon configuration, user accounts and OpenSSH keys, unmount and take offline all content filesystems then effectively reinstall CentOS and the LOCKSS daemon environment and restore the CLOCKSS box's configuration.


### https://documents.clockss.org/index.php?title=LOCKSS:_Property_Server_Operations
All of this sounds like overkill for us. Right?

### Interesting for a quick glance 
-  https://documents.clockss.org/index.php?title=CLOCKSS:_Hardware_and_Software_Inventory
-  https://documents.clockss.org/images/2/22/CLOCKSS_inventory.png


CLOCKSS agreement with hosts
----------------------------
from [here](https://documents.clockss.org/index.php?title=CLOCKSS:_Box_Operations)
Requirements for CLOCKSS host sites: A CLOCKSS host site signs an agreement with the CLOCKSS board under which they commit to providing:
-  Rack space in a physically secure location accessible only to authorized personnel.
-  Power.
-  Cooling.
-  Network bandwidth.
-  And to have an in-place disaster recovery plans if these resources fail. The CLOCKSS Executive Director has copies of these agreements.
The CLOCKSS Network Administrator is responsible for all processes involving production and ingest CLOCKSS box hardware and software infrastructure.

Security, Reliability, and Maintainability
-------------------------------------------
https://documents.clockss.org/index.php?title=CLOCKSS:_Threats_and_Mitigations

> Because of its nature as a tightly-controlled dark archive configures the technology in ways that further reduce risk as compared to the Global LOCKSS Network for which the technology was originally designed. The configuration of the CLOCKSS network is described in CLOCKSS: Box Operations but briefly the additional defenses include:
>
> -  Implementing a large number (currently 12) of CLOCKSS boxes each holding the entire content of the archive.
> -  Ensuring that, after an initial period, each CLOCKSS box's operating system is configured to prevent write or administrative access except by staff at the host institution.
> -  Securing communication among authorized CLOCKSS boxes using SSL certificate checks at both ends of each connection.
> -  Preventing dissemination of content from CLOCKSS boxes except during an approved trigger event (see CLOCKSS: Extracting Triggered Content).


Record Keeping
---------------
https://documents.clockss.org/index.php?title=CLOCKSS:_Logging_and_Records

As above, seems impressive.

TODO: look much more closely at what they are doing.

About Polling and Repair
-------------------------
https://documents.clockss.org/index.php?title=LOCKSS:_Polling_and_Repair_Protocol

_Good to read soon._

AUs
---

_CLOCKSS does one AU per year_

https://documents.clockss.org/index.php?title=Definition_of_AIP
>At the start of each new year an AU for the new year's SIPs from each publisher supplying content via file transfer is configured on the production CLOCKSS boxes via the CLOCKSS property server. The AU's plugin is configured to start crawling from the automatically generated manifest page of that year's directory for that publisher on the staging server at regular intervals. Each time the box crawls, it adds content and metadata from newly received SIPs to the box's copy of the AU. In this case the files in the AIPs have names reflecting the files in the SIPs on the staging server. The publisher's names or URLs for these files are contained in, or recoverable from metadata in the SIPs/AIPs.
