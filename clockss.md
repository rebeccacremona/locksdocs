clockss
=======
https://clockss.org/clockss/Home

"CLOCKSS is a not-for-profit joint venture between the world’s leading academic publishers and research libraries whose mission is to build a sustainable, international, and geographically distributed dark archive with which to ensure the long-term survival of Web-based scholarly publications for the benefit of the greater global research community"
https://clockss.org/clocksswiki/files/CLOCKSS_Overview_-_basic.pdf

> CLOCKSS has achieved a unique consensus among the world’s leading scholarly publishers, who have agreed to make their content available for free, under a creative commons license, in the event that they can no longer supply it. CLOCKSS is thus the only way to make sure that, no matter what happens in the publishing market, scholarly research will always remain in the hands of scholars.
> 
> The archive is distributed across 12 geopolitically and geographically diverse long-lived steward libraries that have agreed to take on an archival role on behalf of the wider international community, in the United States, Canada, Hong Kong, Japan, Australia, Scotland, and growing. CLOCKSS is committed to the very long term: to become a safe haven for scholarly content from all corners of the world, including underserved scholarly communities and those who cannot afford to archive their materials on their own.

The list of participating publishers (https://clockss.org/clockss/Participating_Publishers) and libraries  (https://clockss.org/clockss/Participating_Libraries) are both impressive

This sounds like it would be affordable:
> The 2017 annual contribution below covers e-journals, e-books, and any other materials you’d like to archive.
>
> Total Publishing Revenue: Under $250,000
> Annual Contribution: $227
>
> Journal front file ingest is $0.25/article, with no charge for the first 100 articles. For e-books and other monographs, frontlist ingest is $3.00/book, with no charge for the first 10 e-books. Backfile articles are $0.15 and backlist books $2.00.

Hmm. Except maybe the setup fee. (But, that fee assumes they are writing the plugin, which we have already: "Initial Set-up Fee Set-up Fee: Minimum of $7,500"

Potential Problems
----------------------------------
### Formats
They focus on journals and books; we don't immediatelly fit their model. Practically, they [require metadata](https://clockss.org/clocksswiki/files/File_Transfer_Guidelines_-_CLOCKSS.pdf) that assumes you are describing a journal or a book (issn/isbn, doi, etc.) If that's a technical requirement, rather than policy, we might not be able to be incorporated, at least without development work on their end.

Some evidence that this is a non-issue: "The 2017 annual contribution below covers e-journals, e-books, and any other materials you’d like to archive." We might fall under the "any other materials category." "CLOCKSS may charge Publisher a one-time set-up fee (“Setup Fee”) to cover technical labor and other associated costs for ingesting a new format... An additional Setup Fee may also be applied after the initial plugin has been created and deployed due to a substantial change in the format of Publisher’s Content" (we have our own plugin, so.....)

But, even if that's the case, perhaps we could contribute that effort (which might make more sense than putting effort into duplicating their tech setup). 

### Governance/Ethics/Agreements
If we got involved, would we be making an agreement consonant with what we've already told our users (especially courts and law firms)? (Compare with metarchive [https://www.metaarchive.org/](https://www.metaarchive.org/), which isn't a perfect fit.)

"Triggered content is made freely available via CLOCKSS host sites to everyone." So maybe we couldn't send them private-by-policy links.

Here is the publisher agreement for 2017: https://clockss.org/clocksswiki/files/2017_CLOCKSS_Participating_Publisher_Agreement_January_26_2017.pdf

The legal experts on the team should definitely be the ones to weigh in here. Context: we are only backing up public, and private-by-policy (like NY Times) links via LOCKSS

-  can we agree to being called a "publisher"? is that consistent with us as distributors of user-supplied content?

- "Once Content is deposited in the Archive, such Archived Content cannot be withdrawn either by Publisher or by any subsequent owners of the Archived Content other than due to the failure by CLOCKSS to abide by the CLOCKSS Bylaws or the winding up, dissolution, liquidation, bankruptcy or other termination of operations of CLOCKSS." That sounds like what we are planning. But see also section E about retractions and how to handle situations where distributing something publically might have legal repercussions.

- "The parties acknowledge that copyrights and other intellectual-property rights in items of Archived Content may be owned in whole or part by third parties, including, without limitation, authors, contributors, editors, illustrators and societies (together, “Content Owners”). The parties further acknowledge that digital information in Archived Content may be owned by third parties, including, without limitation, software developers and software publishers (together, “Digital Owners”). Nothing contained in this Agreement shall be construed to supersede, abrogate, amend or otherwise modify any rights of any Content Owners or Digital Owners, regardless of whether a Trigger Event shall have occurred."

- "A Trigger Event occurs when... the Archived Content is determined in good faith by the Board to be unavailable from any publisher for at least six consecutive months. A determination cannot be made by the Board in good faith unless the Board shall have tried to contact Publisher at least twice no less than thirty (30) days before releasing the triggered Archived Content (as described below) and evaluated all information received from Publisher as to whether a Trigger Event had, in fact, occurred and whether the event was temporary or permanent....Trigger Events include, but are not limited to..."Publisher No Longer in Business"..."Catastrophic Failure. While still publishing Content, Publisher is not able to provide access to the Content electronically due to technical or similar catastrophic and permanent failure."

- "Upon the occurrence of a Trigger Event and the affirmative vote of at least seventy-five percent (75%) of all the members of the Board then in office, and not more than two (2) of those members of the Board object, Archived Content may be copied and transferred from the CLOCKSS Archive to a host organization and made available to the general public without charge (“Release”). CLOCKSS will notify Publisher promptly upon the Release of Content. Released Content use terms and restrictions will be determined by an accompanying Creative Commons license (or equivalent license) chosen either by Publisher or, if Publisher fails to respond within thirty (30) days following receipt by it of the notice described above, by the CLOCKSS Board."

- "Any Released Content may be authorized to be removed from Host Organization(s) and public access by the affirmative vote of at least seventy-five percent (75%) of all the members of the Board then in office (“Removal Authorization”). However, because of the impact of a Removal Authorization, a negative vote of three (3) or more members of the Board then in office, shall override such Removal Authorization leaving the Released Content available to public access, unless the removal is requested by the Publisher, in which case the Released Archived Content shall be removed from Host Organization(s) and public access." 

- "Subject to the rights of Content Owners and Digital Owners, and of any creditors of Publisher under applicable law, all such interest holders having been disclosed to CLOCKSS, Publisher hereby grants to CLOCKSS a non-exclusive, royalty-free, worldwide license to: (i) ingest Archived Content onto the CLOCKSS Archive and continually audit and repair Content for preservation; (ii) copy Archived Content from the CLOCKSS Archive and transfer to Host Organizations upon its Release; (iii) reproduce, display and publish Released Archived Content (and any trademarks contained in such Archived Content) throughout the world; and (iv) License Released Archived Content freely to third parties pursuant to the terms and restrictions determined in the manner set forth in Section 4.E. above. No other rights are granted hereby by Publisher to CLOCKSS or any third party, and all such other rights are expressly reserved. For the avoidance of doubt, Publisher, Content Owners and Digital Owners shall retain all copyrights in and to the Content, as the case may be. CLOCKSS will retain any visible copyright notices present in the Archived Content whenever Archived Content is delivered to CLOCKSS and archived by it."

- Indemnification. Woah. Would OGC let us sign this?

Potential benefits
------------------
- already a network of libraries, already geographically distributed, already technically sound, people have already heard of it and trust it, already governed by clear, detailed ethics
- would provide a natural way to extend our registrar network, and get more scholarly publishers on board w/ Perma.
- we would not have to implement/maintain our own custom LOCKSS boxes; the Perma LOCKSS java plugin would be it
