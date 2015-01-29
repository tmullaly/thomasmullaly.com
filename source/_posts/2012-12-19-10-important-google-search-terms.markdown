---
layout: post
title: "10 Important Google Search Terms"
date: 2012-12-19
comments: true
categories: security
---
##site
  * The site operator is great for trolling through all the content Google has gathered for a target.
  * This operator is used in conjunction with many of the other queries presented here to narrow the focus of the search to one target.
##intitle:index.of
  * The universal search for Apache-style directory listings.
  * Directory listings provide a wealth of information for an attacker.
##error | warning
  * Error messages are also very revealing in just about every context.
  * In some cases, warning text can provide important insight into the behind-the-scenes code used by a target.
##login | logon
  * This query locates login portals fairly effectively.
  * It can also be used to harvest usernames and troubleshoot procedures.
##username | userid | employee.ID | "your username is"
  * This is one of the most generic searches for username harvesting.
  * In cases where this query does not reveal usernames, the context around these words can reveal procedural informations an attacker can use in later offensive action.
##admin | administrator
  * Using the two most common terms for the owner or maintainer of a site, this query can also be used to reveal procedural information ("contact your administrator") and even admin login portals.
##-ext:html -ext:htm -ext:shtml -ext:asp -ext:php
  * This query, when combined with the site operator, gets the most common files out of the way to reveal more interesting documents.
  * This query locates backup or tempory files and directories
##inurl:temp | inurl:tmp | inurl:backup | inurl:bak
  * This query locates backup or temporary files and directories.
##intranet | help.desk
  * This query locates intranet sites ( which are often supposed to be protected from the general public) and help desk contact information and procedures.