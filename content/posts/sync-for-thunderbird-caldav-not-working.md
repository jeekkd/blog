+++
author = "Daulton"
title = "Sync for caldav not working after update to Thunderbird 60"
date = "2019-01-21"
description = "Fix caldav not syncing after an update to Thunderbird 60"
tags = [
    "software",
    "guides",
]
categories = [
    "guides",
]
+++

"After I updated from Thunderbird 52.9 to 60 enlightenment stopped working with all caldav calendars on my nextcloud-instance. It doesn't synchronize anymore and doesn't even show that it doesn't. This is a known-issue, it's a NextCloud problem. There is a workaround:
<!--more-->

1. Go to the config editor (Settings -> Advanced -> General -> Config Editor)
2. Search for network.cookie.same-site.enabled
3. Set it to false 

For your own security you should set it back to true once this issue is resolved.(Source, accessed 01/21/2019)"

[Source](https://support.mozilla.org/en-US/questions/1229880#answer-1143907)
