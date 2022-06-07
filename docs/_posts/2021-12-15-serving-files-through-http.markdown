---
layout: post
title:  "Serving Files Through HTTP"
date:   2021-12-15 7:52:05 -0400
categories: java, apprenticeship
---

I spent some time today trying to transfer various file types over Sockets
in Java, and there were a lot of people suggesting that [ImageIO][ImageIO] 
be used to do this. This worked fine for static images like JPG and PNG, 
but didn't work so great for GIF files, and definitely wouldn't work for PDFs.

So how can I serve those oddball files using Sockets? The answer to this 
was actually a lot simpler than I originally thought. With _any_ non-video 
file type (at least that I'm aware of), all you need to do is format your 
response like this:

````
HTTP/1.1 200 OK
Content-Type: [File MIME Type]
Content-Length: [Bytes Count in Content]

[File Content]
````

So how do we get the content of a file? I had originally thought about
using the BufferedInputStream's readLine method. Unfortunately, that strips 
out each newline and carriage return character. While each line could just
be appended with `\r\n`, I can't guarantee that _both_ characters were there
in the original line.

Instead, what I decided to do is fill a byte array with the entirety of the 
file content.

````java
byte[] readFileBytes() {
    File file = new File(path);
    byte[] data  = new byte [(int) file.length()];
    new FileInputStream(file).read(data);
    return data;
}
````

Using this byte array, we can use the length of the array for the 
Content-Length and then write the array to the end of the request.

So now we have the Content-Length and File Content, but how do we know what
to use as the Content-Type? [IANA][mime-types] has a list of all the MIME 
types to be used for transferring different files. Using this list you can 
decide what Content-Type to use for your HTTP response.

[ImageIO]: https://docs.oracle.com/javase/7/docs/api/javax/imageio/ImageIO.html
[mime-types]: https://www.iana.org/assignments/media-types/media-types.xhtml