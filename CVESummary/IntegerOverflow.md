# N-Day-CVE_Summary
DJR
---
## CVE-2006-2025
```
Ubuntu14.04_pt
libtiff:3.8.0
2006
/home/pt/testsuites/tiff-3.8.0
```

### Patch
```$xslt
--- tiff-v3.5.7/libtiff/tif_dirread.c.multiple	2006-04-26 08:52:01.000000000 -0400
+++ tiff-v3.5.7/libtiff/tif_dirread.c	2006-04-26 08:52:24.000000000 -0400
@@ -683,13 +683,20 @@
 	int w = tiffDataWidth[dir->tdir_type];
 	tsize_t cc = dir->tdir_count * w;
 
+	/* Check for overflow. */
+	if (!dir->tdir_count || !w || cc / w != (tsize_t)dir->tdir_count)
+		goto bad;
+
 	if (!isMapped(tif)) {
 		if (!SeekOK(tif, dir->tdir_offset))
 			goto bad;
 		if (!ReadOK(tif, cp, cc))
 			goto bad;
 	} else {
-		if (dir->tdir_offset + cc > tif->tif_size)
+		/* Check for overflow. */
+		if ((tsize_t)dir->tdir_offset + cc < (tsize_t)dir->tdir_offset
+		    || (tsize_t)dir->tdir_offset + cc < cc
+		    || (tsize_t)dir->tdir_offset + cc > (tsize_t)tif->tif_size)
 			goto bad;
 		_TIFFmemcpy(cp, tif->tif_base + dir->tdir_offset, cc);
 	}
```

### Summary
这个integer overflow比较简单，patch也比较简单，就是用乘法和加减判断一下加减溢出。
用core dump跟一下给的payload，发现实际触发漏洞的是_TIFFmemcpy(cp, tif->tif_base + dir->tdir_offset, cc)。
bugzilla上(`https://bugzilla.redhat.com/show_bug.cgi?id=189933`)报的版本是4.0。实际看下来4.0相较于3.8已经有很大的改动，
那个vuln. func本身都不存在了。exploit-db上标的是3.x，但实际3.9.2也没这个问题了。

---
## CVE-2010-1634

```$xslt
Ubuntu14.04_core
python-2.7.0(2.6,3.2,3.1)
2010
/home/core/workspace/program/Python-2.7
```

### Description


```$xslt
- audioop_tostereo - should be fine, but relies on undefined behaviour
- audioop_lin2lin - undetected overflow: size=1, size2=4, len=0x40000001
- audioop_ratecv - undetected overflow: nchannels=0x5fffffff (32bit)
- audioop_ulaw2lin - undetected overflow: size=4, len=0x40000001
- audioop_alaw2lin - same as audioop_ulaw2lin
- audioop_adpcm2lin - undetected overflow: size=4, len=0x20000001
```

### Summary

基本上是涉及INT_MAX的大数check。

---
## CVE-2014-7185

```$xslt
Ubuntu14.04_core
python-2.7.6
2014
/home/core/python/Python-2.7.6
```

### Patch

```$xslt
--- a/Objects/bufferobject.c
+++ b/Objects/bufferobject.c
@@ -88,7 +88,7 @@ get_buf(PyBufferObject *self, void **ptr
             *size = count;
         else
             *size = self->b_size;
-        if (offset + *size > count)
+        if (*size > count - offset)
             *size = count - offset;
     }
     return 1;
@@ -875,4 +875,4 @@ PyTypeObject PyBuffer_Type = {
     0,                                          /* tp_init */
     0,                                          /* tp_alloc */
     buffer_new,                                 /* tp_new */
-};
\ No newline at end of file
+};
```
### Payload
```$xslt
import sys
a = bytearray('CVE request')
b = buffer(a, sys.maxsize, sys.maxsize)
print b[:8192]
```

### Summary
sys.maxsize是INT_MAX,所以有个加法溢出。



