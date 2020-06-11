---
layout: default
title: Miscellaneous
---

# Miscellaneous
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

## Linux
tar compression
```shell
tar -cjvf <output>.tar.bz2 <input>  # also pass through bzip2
tar -xjvf <input>.tar.bz2           # decompress .tar.bzip2
tar -cvf <output>.tar <input>       # only tar
tar -xvf <input>.tar                # decompress .tar
```

Can't execute?
```shell
mount | grep <directory>  # Might have noexec
```

---

## Windows
