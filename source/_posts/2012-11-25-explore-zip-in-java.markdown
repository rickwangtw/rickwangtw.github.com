---
layout: post
title: "Java: ZIP 壓縮檔的探索"
date: 2012-11-25 17:16
comments: true
categories: [Java, Programming, Compression, Zip]
---
<h2 id="overview">前言</h2>

ZIP 壓縮格式於1989年由Phil Katz設計。

支援的壓縮方式多元，包含未經壓縮的stored，最常用的[deflate](http://en.wikipedia.org/wiki/DEFLATE)，
7z使用的[lzma](http://en.wikipedia.org/wiki/LZMA)等。

此格式雖然壓縮率沒有RAR或是7-zip來的高，由於Winzip廣泛地被使用以及Windows ME之後系統支援，
現在為個平台主流的壓縮格式。

<h2 id="content">檢索內容</h2>

``` java ZipTest.java: list the contents of a zip
import java.io.IOException;
import java.util.Enumeration;
import java.util.zip.ZipEntry;
import java.util.zip.ZipFile;

public class ZipTest {
    public void list(String file) {

        try {
			/* the default file is test.zip */
			if (file == null) {
				file = "test.zip";
			}
            ZipFile zipFile = new ZipFile(file);
            Enumeration zipEntries = zipFile.entries();

            while (zipEntries.hasMoreElements())
                System.out.println(((ZipEntry)zipEntries.nextElement()).getName());

        } catch (IOException ex) {
            ex.printStackTrace();
        }
    }

    public static void main(String[] args) {
		String file = null;
		/* use the first parameter as the input zip file */
		if (args.length > 0)
			file = args[0];
        new ZipTest().list(file);
    }
}
```
<br>
<h2 id="reference">參考資料</h2>

[Wikipedia](http://en.wikipedia.org/wiki/Zip_\(file_format\))

[Javadb.com](http://www.javadb.com/how-to-list-the-contents-of-a-zip-file)
