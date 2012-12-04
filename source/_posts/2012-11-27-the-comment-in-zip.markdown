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
* 目錄
	* [解法](#solution)
	* [參考資料](#reference)

<h2 id="solution">解法</h2>

``` java ZipTest.java: extract the zip file comment
import java.io.IOException;
import java.io.RandomAccessFile;

public class ZipTest {
    public static void main(String[] args) {
		String file = null;
		/* use the first parameter as the input zip file */
		if (args.length > 0)
			file = args[0];

		RandomAccessFile zipFile = null;
		try {
			zipFile = new RandomAccessFile(file, "r");

			long fileSize = zipFile.length();
			/* find the magic number in a reverse manner */
			for (long i = 1 ; fileSize - i >= 0; ++i) {
				zipFile.seek(fileSize - i);
				byte b = zipFile.readByte();
				if (b == 0x06) {
					zipFile.seek(fileSize - i - 3);
					/* check for magic "0x06054b50" in little endian */
					byte[] key = new byte[4];
					zipFile.readFully(key);
					if (key[0] != 0x50 || key[1] != 0x4b || key[2] != 0x05) {
						continue;
					}
					/* get the file comment size */
					byte[] tmp = new byte[18];
					zipFile.readFully(tmp);
					int commentSize = (tmp[16] & 0xff) | ((tmp[17] & 0xff) >> 8);
					if (commentSize > 0) {
						byte[] comment = new byte[commentSize];
						zipFile.readFully(comment);
						System.out.println("comment: " + new String(comment));
					}
					break;
				}
			}
		} catch(Exception ex) {
			ex.printStackTrace();
		} finally {
			if (zipFile != null) {
				try {
					zipFile.close();
				} catch (IOException ex) {
				}
			}
		}
    }
}
```

ZIP檔案的註解放在EOCD記錄 (End of Central Directory Record)中。

至於EOCD存放在哪裡，就必須稍微研究一下Zip的檔案格式。
<br>

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

由以上資料我們可以知道，只要有辦法找到 "end of central dir signature"，我們就有辦法知道Zip註解的長度以及內容。
以上的解法就是從檔案尾端一個byte一個byte讀取，直到找個 "0x0605450" 這個 magic number。


<h2 id="reference">參考資料</h2>
[PKWARE](http://www.pkware.com/documents/casestudies/APPNOTE.TXT)
