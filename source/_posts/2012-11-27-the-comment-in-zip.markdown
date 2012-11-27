---
layout: post
title: "Java: ZIP 壓縮檔的註解"
date: 2012-11-27 23:01
comments: true
categories: [Java, Programming, Compression, Zip]
---
Java 的"java.util.zip"套件可以輕易獲得各個內容物的註解。

然而，整個Zip壓縮檔的註解卻無法透過此API輕易獲得。

要攫取此檔案的註解必須要瞭解Zip檔的架構。
<!-- more -->

ZIP檔案的註解放在EOCD記錄 (End of Central Directory Record)中。

ZIP 的檔案格式
	[local file header 1]
	[encryption header 1]
	[file data 1]
	[data descriptor 1]
	. 
	.
	.
	[local file header n]
	[encryption header n]
	[file data n]
	[data descriptor n]
	[archive decryption header] 
	[archive extra data record] 
	[central directory header 1]
	.
	.
	.
	[central directory header n]
	[zip64 end of central directory record]
	[zip64 end of central directory locator] 
	[end of central directory record]

接下來我們看EOCD的結構
	end of central dir signature    4 bytes  (0x06054b50)
	number of this disk             2 bytes
	number of the disk with the
	start of the central directory  2 bytes
	total number of entries in the
	central directory on this disk  2 bytes
	total number of entries in
	the central directory           2 bytes
	size of the central directory   4 bytes
	offset of start of central
	directory with respect to
	the starting disk number        4 bytes
	.ZIP file comment length        2 bytes
	.ZIP file comment       		(variable size)


Reference:
http://www.pkware.com/documents/casestudies/APPNOTE.TXT
