+++
author = "Daulton"
title = "OpenBSD reinstall all packages"
date = "2018-08-28"
description = "This guide will help you reinstall all of your OpenBSD packages."
tags = [
    "openbsd",
    "guides",
]
categories = [
    "guides",
]
+++

This guide will help you reinstall all of your OpenBSD packages.
<!--more-->

----------

1. The following will save a list of manually installed packages:

```
pkg_info -m >packages.txt
```

2. And then comes the unnerving step of uninstalling ALL packages.

```
pkg_delete -X
```

3. Next, I reinstalled all my packages using pkg_add's fuzzy adding feature:

```
export PKG_PATH=${my_favorite_mirror}
pkg_add -z -l packages.txt
```

At this point it will begin reinstalling all of your packages. It worked just like the usual pkg_add -ui. At the end of the entire process, all of your packages will have been reinstalled. And make package on those problematic ports works again.
