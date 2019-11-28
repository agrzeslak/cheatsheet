---
layout: default
title: Google
tags:
    - Dorks
    - Search Engine
---
# Google

Operator | Description | Example
--- | --- | ---
- | Exclude term | -term or -"query with spaces"
+ "..." | Force exact match on word or phrase | +query or "query with spaces"
* | Wildcard match any word or phrase | example * query
#..# | Within range of numbers | query 2000..2020
\| OR | Matches one or other | apple \| banana
AND | Useful paired with other operators | (apple OR banana) AND peach
AROUND(n) | Words or phrases within n words of each other | example AROUND(4) query
allintitle: | Query phrase in title tag | Allintitle:query phrase
cache: | Shows Google's last cached version of a page | Cache:domain.com
ext: filetype: | File extension search | Filetype:aspx
info: | Links with info about domain | Info:domain.com
inpostauthor: | Blog posts written by author | inpostauthor:"steve bobs"
intext: | Query in text of page | intext:"example query"
intitle: | Single term in their title | Intitle:query
inurl: | Term in url | Inurl:query
related: | Similar to queried website | related:domain.com
link: | Link to query | Link:domain.com
location: | Results based around geographic region | Location:Sydney
site: | Search in that site | Site:domain.com

[Google Dorks](https://www.exploit-db.com/google-hacking-database)