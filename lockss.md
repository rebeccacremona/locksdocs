LOCKSS CentOS 6, custom image installation instructions
-------------------------------------------------------
https://www.lockss.org/docs/LOCKSS-Linux6-Install.pdf

pg 5) 64-bit version of CentOS Basic Server installation

pg 5) We recommend installing LOCKSS on a dedicated workstation, server or virtual machine with the following characteristics:
• A 64-bit capable x86 Intel compatible dual-core CPU. A quad-core CPU is preferred.
• A minimum of 8GB of memory

pg 5) A typical LOCKSS box will have 4 hard drives. Modern 2U servers can hold 8 or 12 hard drives – these are preferable because they can use more disks.

pg 5) [The LOCKSS CentOS6 auto-installation cd] will attempt to partition your disks using software RAID (if you have two or more disks), which improves the availability of your LOCKSS box by storing your data redundantly across multiple hard disks. In some cases the use of software RAID is inappropriate and a modified installation method should be used.

pg 9) If you have SATA disks, check that SATA compatibility is set to AHCI (or "RAID") and not IDE-compatible or PATA emulation. Some BIOS systems may instead indicate a “native” or “normal” or “compatibility” mode for the SATA controller – always choose the SATA mode that is “native” or “normal”. (The compatibility mode on a SATA controller is provided in BIOS for older operating systems that do not support SATA directly)

pg 9) Check that the power setting is set to 'Restore to previous state' -- this allows the LOCKSS box to automatically resume normal operation when power is restored after a power failure.

pg 13) “Enable IPv4 support”, and then hit the space bar on your keyboard to select it. Then hit the down arrow key on the keyboard one more time to move the red cursor to “Enable IPv6 Support” and hit the space bar on the keyboard to turn off all IPv6 support.

pg 20) Ensure that the checkbox next to “System clock uses UTC” is checked and that the correct time zone is set for your LOCKSS box location (_could this be important for any reason?_)

pg 24) The LOCKSS daemon requires that port 9729 is accessible to the internet. Ports 22, 8080, and 8081 are used to administrate and monitor the LOCKSS box. The following steps will limit who can access Port 22 (SSH access). (_they use a script, we almost certainly want to do it more transparently_)

pg 26) We need to know how many data storage areas the Linux Installation allocated for LOCKSS. Type “ls –d /cache*” and hit the enter key. Here’s example output: In this example we see two data storage areas - /cache0 and /cache1. Record your result somewhere – you’ll need this information soon.

pg 28) (_reminds me that we need to be running smpt or something, so we can send mail_)

pg 30) In our example earlier, we recorded two data storage areas, /cache0 and /cache1. To
derive our Content storage directories list, we append “/gamma” to each storage area,
then use a semicolon to separate the first storage area from the second.  (_I don't know why. I don't think we need to do that. What in the world does "gamma" mean in this context? In Docker, I accepted the default. I only used one storage area, however.)

pg 31) Start the LOCKSS daemon by typing “/etc/init.d/lockss start” and hit the enter key. (_This is not what the instructions in our repo's, or their repo's, README says_)


ADPN wiki (Otherwise known as the "in-depth guide to LOCKSS")
------------------
"Tobin Cataldo at ADPN has created an in-depth guide to the LOCKSS software."

### a few links
http://www.adpn.org/wiki/LOCKSS_Basics
(This page not informative to me, but hard to find so included. http://www.adpn.org/wiki/Partitioning_Cache_Data)
(A possibly interesting discussion on the limitations of LOCKSS: http://www.adpn.org/wiki/ADPNet_Configuration_Management)

selection from http://www.adpn.org/wiki/Technical_Information
### recommended  hardware specs:
> Here's what we recommend if you are purchasing a server from a vendor:
> An x86 Intel compatible CPU running at 1GHz or better
> A minimum of 1GB of memory (2GB of memory is recommended)
> A bootable CD or DVD drive
> A motherboard with a SATA disk controller (ideally with support for 6 SATA devices)
> Must support the CentOS v5.4 Linux operating system
> A chassis capable of holding at least 4 2-terabyte hard drives

selections from http://www.adpn.org/wiki/LOCKSS_Software
### LOCKSS Filesystem

> Primary file system distribution of LOCKSS content.
>    /etc/lockss
>    /home/lockss
>    /usr/share/lockss
>    /var/log/lockss
>In the file /etc/lockss/config.dat, the LOCKSS_DISK_PATHS variable enumerates the available disks.
>    /cache0/gamma
>    /cache1/gamma
>    /cache2/gamma
>    /cache3/gamma

### File System Deep Dive
> Access URL http://bpldb.bplonline.org/adpn/load/Cartography/000400-000599/000404/tif/000404.tif
> Cache storage /cache0/gamma/cache/m/bpldb.bplonline.org/http/adpn/load/Cartography/000400-000599/000404/tif/000404.tif/
> In the current 1.x implementation of LOCKSS, there is a direct relationship between the access URL and the file system organization of the cached data.
> Where are the bytes of this tif file?
> /cache0/gamma/cache/m/bpldb.bplonline.org/http/adpn/load/Cartography/000400-000599/000404/tif/000404.tif/#content/current
> current is a raw byte file. If the content is binary, the file is binary. If the content is text, the file is text. In order to understand the type of file current is you should examine current.props.
> /cache0/gamma/cache/m/bpldb.bplonline.org/http/adpn/load/Cartography/000400-000599/000404/tif/000404.tif/#content/current.props describes the bytes (HTTP headers, LOCKSS specific headers)
 ```/cache0/gamma/cache/m
  #au_id_file
  #au_state.xml
  #id_agreement.xml
  #no_au_peers
  #node_props
  #nodestate.xml
  bpldb.bplonline.org/
    http/
      #node_props
      adpn/
        #node_props
        load/
          #node_props
          Cartography/
            #node_props
            000400-000599/
              #agreement
              #node_props
              #content/
              manifest.html/
              000404/
                #agreement
                #node_props
                #content/
                tif/
                  #agreement
                  #node_props
                  #content/
                  000404.tif/
                    #agreement
                    #node_props
                    #content/
                      current
                      current.props
```

### Revisions
> Each revision is stored with a full copy of the bytes. current and current.props are renamed to 1 and 1.props. Each revision can always be retrieved. Only the `current` file participates in preservation activities.
``` /cache0/gamma/cache/m/bpldb.bplonline.org/http/adpn/load/Cartography/000400-000599/000404/tif/000404.tif/
   #content/
     1
     1.props
     2
     2.props
     current
     current.props
```
> See #Content Crawler and Revisions to understand how LOCKSS Web crawler handles files that differ in metadata but are identical.

### Starting and Stopping the LOCKSS daemon
> CentOS 7 Version
> ``` systemctl enable lockss
  systemctl start lockss
 systemctl restart lockss
systemctl stop lockss
```

### Log Rotate
> Logs are in /var/log/lockss
>
> logrotate /etc/logrotate.d/lockss

### Configuration Files
Primary Configuration files
  - http://props.lockss.org:8001/adpn/lockss.xml
  - /etc/lockss/config.dat
  - /home/lockss/local.txt
  - /cache0/gamma/config/expert_config.txt
  - /cache0/gamma/config/au.txt
  - Additional Configuration files
  - /cache0/gamma/config/ui_ip_access.txt
  - /cache0/gamma/config/proxy_ip_access.txt
  - /cache0/gamma/config/content_servers_config.txt
  - Configuration parameters are listed
  - http://www.lockss.org/lockssdoc/gamma/daemon/paramdoc.html
  - http://www.lockss.org/lockssdoc/gamma/daemon/paramdoc.txt

### Crawl Windows
>Crawl windows can be set to control when the LOCKSS daemon should start and end crawls. The LOCKSS Daemon will disallow crawls when outside of the crawl window. The LOCKSS Daemon will also abort in-progress crawls that overrun the window. For tight windows, examine the org.lockss.crawler.startCrawlsInterval parameter and the au_def_pause_time in the plugin definition.
```
<entry>
  <string>au_crawlwindow_ser</string>
  <org.lockss.daemon.CrawlWindows-Interval>
      <start>
        <time>1427940044207</time>
        <timezone>America/Chicago</timezone>
      </start>
     <end>
        <time>1427882384207</time>
        <timezone>America/Chicago</timezone>
     </end>
     <fieldMask>3</fieldMask>
     <timeZoneId>US/Central</timeZoneId>
  </org.lockss.daemon.CrawlWindows-Interval>
 </entry>
 ```

### Content Crawler and Revisions
>[BaseUrlCacher.java](http://lockss.cvs.sourceforge.net/viewvc/lockss/lockss-daemon/src/org/lockss/plugin/base/BaseUrlCacher.java?view=log)
>Depending on AU and plugin definitions, the repository manager will initiate a new content crawl for an existing AU. When examining previously crawled URLs, the LOCKSS crawler does not request a full copy of the remote data. The LOCKSS crawler retrieves the last modified date of the cached content in stored metadata (current.props) and sends that date as an HTTP header (If-Modified-Since) to the remote server. The remote server checks file last modified date against the LOCKSS request HTTP header data and responds with 304 (not-modified) when the file has not changed. In the event the remote server determines the file last modified date is newer than LOCKSS request HTTP header date, the remote server sends the content.
>According to [RepositoryNodeImpl.java](http://lockss.cvs.sourceforge.net/viewvc/lockss/lockss-daemon/src/org/lockss/repository/RepositoryNodeImpl.java?view=log) (see sealNewVersion()), the LOCKSS repository manager only saves crawler content in the cache as a revision if the files are not identical. If files are identical but last modified time is different, the repository manager renames the existing current.props to current.props-[LAST MODIFIED DATE] and the most recent file metadata is saved into current.props. current isn't touched.


LOCKSS UI Guide
---------------
http://metaarchive.org/public/resources/Lockss_UI_Guide.pdf
Looks helpful!


https://plnwiki.lockss.org/
------------------------
(I can't seem to get a list of all the pages on this wiki in the normal way. Here is a list generated by the wiki api. Included for the record; visiting each in turn.)
```
<?xml version="1.0"?>
<api>
  <query>
    <allpages>
      <p pageid="40" ns="0" title="APIs" />
      <p pageid="22" ns="0" title="CGI network" />
      <p pageid="68" ns="0" title="Daemon Status Web Service Java Client" />
      <p pageid="64" ns="0" title="Daemon Status Web Service Python Client" />
      <p pageid="46" ns="0" title="Feature Wish List" />
      <p pageid="80" ns="0" title="Implementation Details" />
      <p pageid="30" ns="0" title="LOCKSS Technical Manual" />
      <p pageid="20" ns="0" title="Lockss-usdocs" />
      <p pageid="1" ns="0" title="Main Page" />
      <p pageid="89" ns="0" title="Minutes 01062015" />
      <p pageid="44" ns="0" title="Minutes 12032013" />
      <p pageid="9" ns="0" title="PLN Community Meeting 2011" />
      <p pageid="17" ns="0" title="PLN Community Meeting 2012" />
      <p pageid="18" ns="0" title="PLN Community Meeting 2012 test" />
      <p pageid="29" ns="0" title="PLN Community Meeting 2013" />
      <p pageid="90" ns="0" title="PLN Community Meeting 2015" />
      <p pageid="99" ns="0" title="PLN Community Meeting 2016" />
      <p pageid="43" ns="0" title="PLN Monthly Meeting Minutes" />
      <p pageid="6" ns="0" title="PLN working groups" />
      <p pageid="19" ns="0" title="PLNs" />
      <p pageid="31" ns="0" title="Plugins/Plugin XML Format" />
      <p pageid="7" ns="0" title="Retrieval-and-access" />
      <p pageid="8" ns="0" title="Retrieval and access" />
      <p pageid="33" ns="0" title="Software that works with LOCKSS" />
      <p pageid="45" ns="0" title="Testing Frameworks" />
      <p pageid="65" ns="0" title="The COUNTER Reports Web Service" />
      <p pageid="49" ns="0" title="The Content Configuration Web Service" />
      <p pageid="81" ns="0" title="The Content Web Service" />
      <p pageid="51" ns="0" title="The Daemon Status Web Service" />
      <p pageid="83" ns="0" title="The Hasher Web Service" />
      <p pageid="50" ns="0" title="The addAuById operation" />
      <p pageid="69" ns="0" title="The addAusByIdList operation" />
      <p pageid="74" ns="0" title="The deactivateAuById operation" />
      <p pageid="75" ns="0" title="The deactivateAusByIdList operation" />
      <p pageid="70" ns="0" title="The deleteAuById operation" />
      <p pageid="71" ns="0" title="The deleteAusByIdList operation" />
      <p pageid="82" ns="0" title="The fetchFile operation" />
      <p pageid="91" ns="0" title="The fetchVersionedFile operation" />
      <p pageid="87" ns="0" title="The getAllAsynchronousHashResults operation" />
      <p pageid="86" ns="0" title="The getAsynchronousHashResult operation" />
      <p pageid="53" ns="0" title="The getAuIds operation" />
      <p pageid="54" ns="0" title="The getAuStatus operation" />
      <p pageid="66" ns="0" title="The getCounterReport operation" />
      <p pageid="67" ns="0" title="The getPlatformConfiguration operation" />
      <p pageid="92" ns="0" title="The getVersions operation" />
      <p pageid="85" ns="0" title="The hashAsynchronously operation" />
      <p pageid="84" ns="0" title="The hash operation" />
      <p pageid="52" ns="0" title="The isDaemonReady operation" />
      <p pageid="57" ns="0" title="The queryAus operation" />
      <p pageid="62" ns="0" title="The queryCrawls operation" />
      <p pageid="58" ns="0" title="The queryPeers operation" />
      <p pageid="55" ns="0" title="The queryPlugins operation" />
      <p pageid="63" ns="0" title="The queryPolls operation" />
      <p pageid="61" ns="0" title="The queryRepositories operation" />
      <p pageid="60" ns="0" title="The queryRepositorySpaces operation" />
      <p pageid="78" ns="0" title="The queryTdbAus operation" />
      <p pageid="76" ns="0" title="The queryTdbPublishers operation" />
      <p pageid="77" ns="0" title="The queryTdbTitles operation" />
      <p pageid="59" ns="0" title="The queryVotes operation" />
      <p pageid="72" ns="0" title="The reactivateAuById operation" />
      <p pageid="73" ns="0" title="The reactivateAusByIdList operation" />
      <p pageid="88" ns="0" title="The removeAsynchronousHashRequest operation" />
      <p pageid="4" ns="0" title="Usdocs" />
      <p pageid="39" ns="0" title="Using the Plug-in tool" />
      <p pageid="48" ns="0" title="Web Services" />
      <p pageid="56" ns="0" title="Web services SQL-like query" />
      <p pageid="47" ns="0" title="WishList" />
    </allpages>
  </query>
</api>
```

LOCKSS Technical Manual
------------------------
https://plnwiki.lockss.org/index.php?title=LOCKSS_Technical_Manual

### Basic PLN infrastructure

>One archival unit is typically a one-year collection (size: 1GB to 20GB). The size of an AU results of a trade-off between the processing overhead required by large AUs and the guarantee that all AUs integrity can be regularly checked in the case of a multitude of small AUs.

_All the other sources I've seen say an AU is usually one volume of a journal._

>The daemon is a java application which collects digital objects through http requests from the original website, store them inside the cache as an Archival Unit, computes an SHA-1 checksum and regularly monitors their integrity by comparing the preserved content with the other caches in the network with a specific voting protocol (LCAP).

>The AUs are collected at various moments in time by the different nodes in the network to reduce the risk of communication issues. The content is regularly recrawled from the original website if it is still available. If a new version of the AU is available, the previous version is kept but only the most recent AUs will be checked for fixity.

>The administrative server: The administrative server is a simple webserver delivering the global configuration (lockss.xml) to the PLN caches composing the network. Local cache settings can however supersede the global configuration

Diagram: https://plnwiki.lockss.org/index.php?title=File:PLN_infrastructure.jpg

### How to install and configure a LOCKSS daemon on a PLN node?
Compare how spare and simple these instructions are, compared to the instructions that came with the Centos6 image. These are closer to those in our Perma README.

### How to replace, upgrade or insert a new node in a PLN already in production?

>When a new cache is inserted in the PLN (due to a new institution contributing to the PLN, a cache repair or a regular 3-year cache disk replacement) and if this new cache shows a valid security certificate, it should directly collect AUs not from the original source (the AU publisher) but from another cache in the PLN configured as a proxy. The idea is that the PLN should survive the institutions, implying that the safe source of information is supposed to be the PLN itself and not the original source which is supposed to be more prone to attacks and less safe than our PLN. This is actually another point of view than "LOCKSS cache can be used as a proxy for the original server at anytime" which assumes that the original source is always the reference and which, in my understanding, should be only used for the GLN and not for PLNs. If I understand well, this behavior should be easy to configure by setting the AU parameter org.lockss.crawler.fetch_from_other_caches_only to true, forcing the cache to collect the AU from other caches.

_Two comments: #1 "regular 3-year cache disk replacement". #2 Hmm. Controversial. And this recommends changing the default setting to its opposite. Default is to only repair from publisher. There are two intermediate options: repair from publisher first and if that fails then from peers, or from peers first and if that fails then try the publisher._

### Securing your PLN
>For the web interfaces, IP filtering is turned on be default, and can be configured through the UI (Admin Access Control, Content Access Control). Stronger security is available (SSL, form authentication with inactivity timeouts, password rotation and strength requirements, etc.) for all but the proxy. The easiest way to enable this is to choose one of the pre-defined policies by setting org.lockss.accounts.policy to one of {BASIC, FORM, SSL, LC}. BASIC just enables the user accounting subsystem and allows you to create additional user accounts; FORM replaces the basic auth method (browser popup) with an HTML form, SSL replaces HTTP with HTTPS, and LC enables the strict Library of Congress security policies (strong passwords, password rotation, session timeouts, auditing of user-initiated events, etc.) When you enable SSL you can either give it a server certificate or let it generate one itself (in which case browsers will produce a scary warning about a self-signed certificate, which users will have to accept. I recommend you use the defaults at first and turn on additional security once you have things working.
>
>LCAP communication is unencrypted by default. If you put the appropriate keystores in /etc/lockss/keys, the startup scripts will automatically enable v3overSSL and sslClientAuth, which will cause all communication to be encrypted and all connections to be verified as coming from a node with a valid key. It's best to leave this until all or most of the nodes are up and running as generating and distributing the key files adds a nuisance factor.


### Potentially Interesting Settings
Complete list of LOCKSS parameters: https://www.lockss.org/lockssdoc/gamma/daemon/paramdoc.html

>org.lockss.auStatus.peerArgeementsUseReputationTransfers   [true]
>Comment:    If true, Peer Agreement tables will take reputation transfers into account.
>Used in:    org.lockss.state.ArchivalUnitStatus

Lots of settings related to "backup emails", which it appears are turned off by default.

>org.lockss.baseau.overrideFetchRateLimiterSource   []
>Comment:    Override fetch rate limiter source for all plugins and AU no matter what else they specify. Can be "au" or "plugin"".
>Used in:    org.lockss.plugin.base.BaseArchivalUnit

_Could a rogue LOCKSS box use the above to DoS Perma? (Maybe that's crazy talk.) Still, we should consider throttling requests at the Perma level._

>org.lockss.baseuc.socketKeepAlive   [false]
>Comment:    If true, use so_keepalive on server connections.
>Used in:    org.lockss.plugin.base.BaseUrlFetcher

_Do we want to set this true_?

>org.lockss.blockHasher.enableLocalHash   [false]
>Comment:    If true, enable local hash generation and verification
>Used in:    org.lockss.hasher.BlockHasher

_Likely worth learning more about this._

>org.lockss.config.clientAuthKeystore   []
>Comment:    If set, the daemon will authenticate itself to the config server using this keystore. The value is the name of the keystore (defined by additional org.lockss.keyMgr.keystore.<id>.xxx parameters (see {@link org.lockss.daemon.LockssKeyStoreManager}), or "lockss-ca", to use the builtin keystore containing the LOCKSS signing cert. Can only be set in platform config.
>Used in:    org.lockss.config.ConfigManager

>org.lockss.config.configFilePath   [config]
>Comment:
>Used in:    org.lockss.config.ConfigManager

>org.lockss.config.expert.allow   [(null)]
>Comment:    If set to a list of regexps, matching parameter names will be allowed to be set in expert config.
>Used in:    org.lockss.config.ConfigManager

_This references changing daemon settings via the web interface. I played with that, using it to turn on subscription-related features. See below. What's the relationship between this whitelist, and the below blacklist?_

>org.lockss.config.expert.deny   [[[pP]assword\b, ^org\.lockss\.platform\., ^org\.lockss\.keystore\., ^org\.lockss\.app\.exit(Once|After|Immediately)$, org\.lockss\.daemon\.groups, org\.lockss\.auxPropUrls, org\.lockss\.localIPAddress, org\.lockss\.localV3Identity, org\.lockss\.localV3Port, org\.lockss\.ui\.subnetMaskBitsError, org\.lockss\.export\.enabled, org\.lockss\.config\.expert\.allow, org\.lockss\.config\.expert\.deny, org\.lockss\.daemon\.testingMode]]
>Comment:    If set to a list of regexps, matching parameter names will not be allowed to be set in expert config.
>Used in:    org.lockss.config.ConfigManager

_Should we harden this further? What's the relationship between this blacklist, and the previously mentioned whitelist?_

>org.lockss.config.maxLogValLen   [2000]
>Comment:    When logging new or changed config, truncate val at this length
>Used in:    org.lockss.config.ConfigManager

_Don't see how this would come up, but whenever I see the word "truncate" I like to take note. This is just for logging._

>org.lockss.config.reloadInterval   [1800000 (30m0s)]
>Comment:
>Used in:    org.lockss.config.ConfigManager
>org.lockss.config.sendVersionEvery   [86400000 (1d0h0m)]
>Comment:
>Used in:    org.lockss.config.ConfigManager

>org.lockss.crawlStatus.keepUrls   [errors, sources, referrers]
>Comment:    Determines which sets/maps of URLs are kept after the crawl ends. (Accumulating lots of URL lists from multiple crawls can cause the daemon to run out of memory.) If the substrings fetched, excluded, parsed, notModified, pending, error, referrers appear in the value of the parameter, the corresponding sets or URLs will be recorded. all causes all sets to be kept.
>Used in:    org.lockss.crawler.CrawlerStatus
>
> (snip)
>
>org.lockss.crawlStatus.recordUrls   [all]
Comment:    Determines which sets/maps of URLs are recorded and which are only counted. (Recording URLs in crawl status takes lots of memory.) If the substrings fetched, excluded, parsed, notModified, pending, error appear in the value of the parameter, the corresponding sets or URLs will be recorded. all causes all sets to be recorded.
Used in:    org.lockss.crawler.CrawlerStatus

_Many more settings about that, especially re: referers. I don't totally get what they are talking about, or why there are so many settings, but since it sounds like this significantly impacts memory (RAM? diskspace? which kind of memory?), we should probably look into it._

>org.lockss.crawler.crawlEndReportEmail   [(null)]
>Comment:    Email address to which crawl-end reports will be sent
>Used in:    org.lockss.crawler.FollowLinkCrawler

>org.lockss.crawler.maxNewContentRate   [1/18h]
>Comment:    Maximum rate at which we will start new content crawls for any particular AU
>Used in:    org.lockss.crawler.CrawlManagerImpl
>
>org.lockss.crawler.maxPluginRegistryNewContentRate   [1/2h]
>Comment:    Maximum rate at which we will start new content crawls for any particular plugin registry
>Used in:    org.lockss.crawler.CrawlManagerImpl
>
>org.lockss.crawler.maxRepairRate   [50/1d]
>Comment:    Maximum rate at which we will start repair crawls for any particular AU
>Used in:    org.lockss.crawler.CrawlManagerImpl

>org.lockss.crawler.minWindowOpenFor   [900000 (15m0s)]
>Comment:    Don't start crawl if window will close before this interval
>Used in:    org.lockss.crawler.CrawlManagerImpl

>org.lockss.crawler.odc.queueRecalcAfterNewAu   [60000 (1m0s)]
>Comment:    Interval after new AU creation to recalc queue. Should be large enough that it only happens once when a batch of AUs is finished.
>Used in:    org.lockss.crawler.CrawlManagerImpl

_That doesn't sound very large_

>org.lockss.crawler.repair_needs_permission   [false]
>Comment:    Sets this to true will cause repairs to require permission
>Used in:    org.lockss.crawler.RepairCrawler

>org.lockss.crawler.restartAfterCrash   [true]
>Comment:    If true, give priority to crawls that were running when daemon died
>Used in:    org.lockss.crawler.CrawlManagerImpl

>org.lockss.crawler.startCrawlsInterval   [3600000 (1h0m0s)]
>Comment:    Interval at which we check AUs to see if they need a new content crawl.
>Used in:    org.lockss.crawler.CrawlManagerImpl

_That's pretty often! Also, the diagram [here](https://plnwiki.lockss.org/index.php?title=File:PLN_infrastructure.jpg) says AU recrawl is only once every three months!! Is that describing something different?_

>org.lockss.crawler.timeout.data   [1800000 (30m0s)]
>Comment:    Amount of time that may elapse without any data being received on an open connection, before the crawler will give up. Should generally be much larger than the connect timeout. The fact that the connection was opened indicates that the server is up (or was up recently), and busy servers or complicated transactions may legitimately take a long time to begin sending data.
>Used in:    org.lockss.crawler.BaseCrawler

_30 min sounds kind of crazy here_

>org.lockss.cron.sleep   [3600000 (1h0m0s)]
>Comment:    Interval at which Cron wakes up to check for runnable tasks
>Used in:    org.lockss.daemon.Cron

>_org.lockss.export.enabled   [false]
>Comment:    Enable AU export from UI. Daemon restart required when set to true, not when set false
>Used in:    org.lockss.servlet.ExportContent

_I wonder if, for our purposes, this might be better than "proxying" content, since we are delivering warcs. Then the curator could download them and replay, rather than.... whatever the "proxy" would do. Less resource intensive?_

>rg.lockss.id.attackDetected   [-500]
>Comment: The ATTACK_DETECTED parameter.
>Used in:    org.lockss.protocol.IdentityManagerImpl
>
>org.lockss.id.spoofDetected   [-30]
>Comment: The SPOOF_DETECTED parameter.
>Used in:    org.lockss.protocol.IdentityManagerImpl

_Hmm. What are those??_

>org.lockss.listHoldings.enablePreserved   [true]
>Comment:    Enable "preserved" option when ListHoldings UI is enabled.
>Used in:    org.lockss.servlet.ListHoldings
>
>org.lockss.listHoldings.enabled   [false]
>Comment:    Enable ListHoldings in UI. Daemon restart required when set to true, not when set false
>Used in:    org.lockss.servlet.ListHoldings
>
>org.lockss.listHoldings.useMetadataForPreserved   [false]
>Comment:    Enable "use metadata" option when "preserved" option is enabled.
>Used in:    org.lockss.servlet.ListHoldings

>org.lockss.mail.enabled   [false]
>Comment:
>Used in:    org.lockss.mail.MailService

_Lots of settings related to mail. I'm suprised this one is false by default. Not sure if that really disables all mail attempts._

> org.lockss.metrics.logMem.interval   [0]
> Comment:    The interval at which memory usage should be logged, or 0 to disable
> Used in:    org.lockss.daemon.SystemMetrics

>org.lockss.pdf.pdfMemoryLimit   [5242880]
>Comment:    Number of megabytes above which a filtered PDF file is transferred from memory to a temporary file. May also be used by other PDF operations that require in-memory processing.
>Used in:    org.lockss.pdf.PdfUtil

_That's a lot of megabytes. Typo? What's reasonable here?_

>org.lockss.platform.remoteConfigFailoverMaxAge   [0]
>Comment:    Maximum acceptable age of a remote config failover file, specified as an integer followed by h, d, w or y for hours, days, weeks and years. Zero means no age limit.
>Used in:    org.lockss.config.ConfigManager

>org.lockss.plugin.acceptExpiredCertificates   [true]
>Comment:    If true, accept plugins signed by otherwise-valid certificates that are expired or not yet valid.
>Used in:    org.lockss.plugin.PluginManager
>
>org.lockss.plugin.allowGlobalAuConfig   [false]
>Comment:    If true, AU configurations may appear in any config file. This was always the case prior to 1.55, setting this true restores that behavior.
>Used in:    org.lockss.plugin.PluginManager

>org.lockss.poll.agreeVerify   [10]
>Comment:
>Used in:    org.lockss.poller.V1Poll

>org.lockss.poll.quorum   [5]
>Comment:
>Used in:    org.lockss.poller.V1PollFactory

Lots of settings about repairs. Definitely want to understand that process better.

>org.lockss.poll.v3.deleteExtraFiles   [false]
>Comment:    If false, just log a message rather than deleting files that are considered to be missing from a majority of peers.
>Used in:    org.lockss.poller.v3.V3Poller

_Hmmm. Why are we deleting things? We might want to toggle this setting._

>org.lockss.proxy.disallowedMethods   [[]]
>Comment:    A semicolon-separated list of HTTP methods that the proxy should never allow
>Used in:    org.lockss.proxy.ProxyManager

_This sounds clear. Disallow everything but GET, right?_

>org.lockss.serveContent.followPublisherRedirect   [Missing]
>Comment:    Determines when to follow redirect returned by publisher. Can be set to one of:
>Never
>Always
>Missing: Only if URL is not present in cache.
>Used in:    org.lockss.servlet.ServeContent

_Interesting if Perma ever moves, and the LOCKSS proxies are in use._

>org.lockss.subscription.enabled   [false]
>Comment:    Indication of whether the subscription subsystem should be enabled.
>Defaults to false. Changes require daemon restart.
>Used in:    org.lockss.subscription.SubscriptionManager
>
>org.lockss.subscription.totalSubscriptionEnabled   [false]
>Comment:    Indication of whether the Total Subscription option is enabled.
Defaults to false.
>
>Used in:    org.lockss.subscription.SubscriptionManager

_These I think we definitely want on. Did so in the Docker version_

There are some settings about what to do if threads run out of memory, whether that should crash the daemon or not. Probably worth reading a bit more about that.

>org.lockss.ui.authType   [Basic]
>Comment:    User authentication type: Basic or Form
>Used in:    org.lockss.servlet.AdminServletManager

>org.lockss.ui.maxUploadFileSize   [500000]
>Comment:    Maximum size of uploaded file accepted
>Used in:    org.lockss.servlet.LockssServlet

_Who is uploading files via the UI?_

>org.lockss.urlconn.serverTrustLevel   [Untrusted]
>Comment:    Determines the required trustworthiness of HTTPS server certificates.
>Set to one of:
>Trusted
>Server certificate must be signed by a known CA.
>SeflSigned
>Server certificate must be self-signed or signed by a known CA.
>Untrusted
>Any server certificate will be accepted.
>Used in:    org.lockss.util.urlconn.HttpClientUrlConnection

_Hmmm._


LOCKSS Daemon release notes, questions and things to note or watch out for
--------------------------------------------------------------------------
https://www.lockss.org/support/use-a-lockss-box/daemon-release-notes/new/

### Daemon 1.70.2

"ServeContent returned 500 in some cases where the publisher served compressed content despite the absence of an Accept-Encoding request header allowing it to do so."

### Daemon 1.69.4

"Subscription Manager supports subscribing to all current and future AUs, or to all current and future AUs belonging to specific publishers. Each publisher on the Title Subscription Management pages now has an Overall Subscription checkbox, and if "org.lockss.subscription.totalSubscriptionEnabled=true, a new Total Subscription option will appear at the top." _We probably want this option. I seem to have successfully set it up in my Docker instance._

"The weight assigned to agreeing/disagreeing URLs in poll tallies may be controlled by plugins with au_url_poll_result_weight, a list of <pattern>,<float>. This does not affect the repair process, but may be useful to give a better indication of the preservation state and inter-peer agreement state of sites where auxiliary files such as CSS and JavaScript are rapidly versioned."

"If an AU has been assigned a crawl priority or its crawls have been disabled (via crawlPriorityAuidMap), that fact will be displayed on the AU status page." _Perhaps good to know that crawls of particular AUs can be stopped._

"hostconfig sets permissions of toplevel cache directories to 750."

### Daemon 1.68.4

"If org.lockss.baseuc.socketKeepAlive is true (default false), SO\_KEEPALIVE will be enabled for crawler TCP connections. This should eliminate daemon exits resulting from hung connections due to server or network outages. The default will be changed to true in an upcoming release." _Doesn't appear to have happened yet_

"Experimental support for using Shibboleth to authorize UI access is available."

"A new Alert, NewFileVersion, is raised when a different version of file that already exists is collected."

"The major causes of temp files accumulating over time have been fixed." _If this is an issue, perhaps we ought to equip each box with a script that regularly cleans temp directories._


### Daemon 1.67.3

"Since release 1.62 the daemon has included support for Proof of Possession and Local polls in addition to the traditional Proof of Retrievability polls. Proof of Retrievability polls hash the entire content of an AU. Proof of Possession polls hash a random sample of an AU’s content and are a less expensive way for peers to prove to other peers that they share the same content for the AU. Local polls compare the computed and stored hashes of the content of an AU as a low-cost hint as to whether the AU needs a Proof of Retrievability poll to repair possible damage.We have tested these new poll types and plan shortly to enable them in the GLN and CLOCKSS networks. To support this 1.67 includes a policy that determines when peers will call these new types of poll."

"The config backup file is now generated periodically at a predictable location on disk and may be fetched or backed up from there. The Backup link in Journal Configuration uses this file if present so doesn’t incur the delay to generate the file. By default the filename is /cache0/gamma/backup/config_backup.zip (where cache0 is the first configured disk). The name may be changed by setting org.lockss.backup.fileName, the directory by setting org.lockss.backup.dir."

"Daily crawl windows were one hour off during daylight savings time."

### Daemon 1.66.3

"The level of trust required to collect files from HTTPS servers can be selected by setting org.lockss.urlconn.serverTrustLevel to one of:
  - Trusted: server certificate must be signed by a known CA.
  - SelfSigned: server certificate must be self-signed or signed by a known CA.
  - Untrusted: any server certificate will be accepted.
The previous behavior was equivalent to SelfSigned. The default is Untrusted, as that’s no less trustworthy than SelfSigned."

"TCP keepalives can be enabled for UI connections by setting org.lockss.ui.keepAlive to true. This will allow long UI operations to complete without connections being closed prematurely. (On most systems it’s also necessary to use sysctl (or modify /etc/sysctl.conf) to reduce net.ipv4.tcp_keepalive_time to less than 60 minutes.)" (see also ### Daemon 1.68.4 note)

### Daemon 1.65.5

"Initial implementation of a Web Services API. Documentation is available at http://plnwiki.lockss.org/wiki/index.php/Web_Services" _There is no text at that URL!!!!!!!!!!!!!!!!!_

"org.lockss.crawler.crawlFromAddr did not take effect." _that setting sounds interesting_

### Daemon 1.63.2

"The subscription manager adds in-range AUs only if they’re still available from the publisher." _as time goes on, we might want to limit certain boxes to certain ranges, so that there disks aren't over-filled, but the network is still backing up everything.

### Daemon 1.62.4

"Incremental improvements in daemon startup time with a large number of AUs." _Good to know this is an issue. I'll try to look into what counts as "large"._

### Daemon 1.61.5

"Subscription Manager. Support has been added for automatically configuring new AUs for selected (“subscribed”) serial publications. It’s disabled by default in this release as there are some unusual numbering schemes present in the GLN that aren’t yet handle correctly. Feedback is welcome: set org.lockss.subscription.enabled=true and restart." _This is very convenient, tried it in Docker. I think we want this in production._

### Daemon 1.60.3

"Memento support has been added.  The daemon implements TimeGate and TimeMap services, allowing historical versions of content to be viewed from Memento-aware browsers." _Interesting_

### Daemon 1.58.3

"Known Problems: The %Full value in Repository Space displays is incorrect (low).  It’s calculated as the free space / total space, instead of free space / usable space." _I don't think I saw a note that this was fixed._

### Daemon 1.56.3

"Per-user build settings should now go in ~/.lockssprops . ~/lockss.properties will continue to work." _Interesting. I wonder what's in there? Do we have those files? Or not, since we aren't building locks, I don't think._

### Daemon 1.54.3

"Crawl rate can be varied by time of day, day of week, etc. Plugins should specify a serialized RateLimiterInfo as the value of au_rate_limiter_info. CrawlWindows.Daily is a simpler way for plugins to specify simple windows. CrawlWindows.Always and CrawlWindows.Never are always-open and never-open windows."

"Poll agreement values are now displayed consistently across DaemonStatus tables. Some scripts may need to be adjusted. For normal output the value is a float between 0.0 and 100.0, followed by “%”. In XML output, raw agreement values (outputVersion=2) are an unadorned float between 0.0 and 1.0."

----
https://www.lockss.org/support/use-a-lockss-box/daemon-release-notes/old/

### Daemon 1.53.3

"The maximum Java heap can be set in /etc/lockss/config.dat, e.g.,: LOCKSS_JAVA_HEAP=2048m
provides a 2GB heap. For 64-bit JVMs it’s recommended this be set to at least 5000m."

"The per-AU new-content crawl interval (nc\_interval param in AU config in the title DB) can be changed on the fly." (from 1.52.3 "The interval at which the daemon checks for new content can be set per AU, by adding the param nc_interval to the AUs config in the title DB. Value should be a time interval (eg, 2w to check every two weeks).")

### Daemon 1.52.3

"The number of simultaneous crawls per plugin/publisher/site can be set with org.lockss.crawler.concurrentCrawlLimitMap, a map from concurrentCrawlPoolKey (fetchRateLimiterKey) to allowed number of simultaneous crawls."

### Daemon 1.44.2

"Keystores may be loaded from a file, resource (jar) or URL, by setting the appropriate one of org.lockss.keyMgr.keystore.id.file, org.lockss.keyMgr.keystore.id.resource or org.lockss.keyMgr.keystore.id.url. See LOCKSS Network Administration for more details."

"org.lockss.plugin.keystore.password is now optional. An alternate plugin keystore may be used by setting just org.lockss.plugin.keystore.location."

### Daemon 1.35.3

"hostconfig no longer erroneously reports that cache dirs aren’t writable." _I think this has reverted. I was gettiing this report in my Docker installation, but to all evidence, everything was fine, and I used CRAZY +rwx for all, and chowned everything, etc._


LOCKSS-O-MATIC
--------------
https://github.com/mjordan/lockss-o-matic
This sounds potentially useful.
"LOCKSS-O-Matic helps automate the ingestion of content into a Private LOCKSS Network (PLN). It also monitors the PLN to ensure that all member boxes are online and operating normally."


Daemon Status Web Service Python Client
---------------------------------------
Could be useful, maybe?
https://plnwiki.lockss.org/index.php?title=Daemon_Status_Web_Service_Python_Client

Automatically Subscribing Boxes to Perma??
-------------------------------------------
I _think_ this means we can make a service that will auto-subscribe our LOCKSS boxes to Perma.
https://plnwiki.lockss.org/index.php?title=The_Content_Configuration_Web_Service
https://plnwiki.lockss.org/index.php?title=Web_services_SQL-like_query

LOCKSS (read-only?) SOAP API
---------------------------
>(Added in LOCKSS v1.61)
>LOCKSS offers two SOAP APIs, DaemonStatusService and CounterReportsService. Information on endpoints and the WSDL files for both are available at http://lockssbox.example.com:8081/ws.
>Everything on port 8081 is protected by username/password and IP whitelisting. Therefore, to access the SOAP APIs you will need to 1) add the IP address of the computer running this script to the "Allow Access" list under "Admin Access Control" in your box's admin UI and 2) tell the SOAP client the username and password of the LOCKSS user. You do not need to (and probably shouldn't) use the admin user's account to access the SOAP service; instead, create a new user and give them only "View debug info" permissions.
>A version of the first script below, plus some additional scripts, are available at https://github.com/mjordan/lockssscripts.

The example URL they give isn't real; will spin up my test instance soon and see what's there.

Reference for understanding plugins
-----------------------------------
https://plnwiki.lockss.org/index.php?title=Plugins/Plugin_XML_Format

Database Schemas?? (Maybe?)
---------------------------
https://github.com/lockss/lockss-daemon/blob/master/doc/daemon/Metadata_Database_Schema.odt
https://github.com/lockss/lockss-daemon/blob/master/doc/daemon/COUNTER_Reports_Database_Schema.odt

Java-style Doc
---------------
https://www.lockss.org/lockssdoc/gamma/daemon/
