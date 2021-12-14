---
layout: post
title:  "Testing with Anonymous Classes"
date:   2021-12-13 7:52:05 -0400
categories: java apprenticeship
---

I've been trying to find ways to unit test function that rely on complex 
classes and interfaces. I have a function like this in a Java application:

````java
public void doStuff(Socket socket) {
    InputStream in = socket.getInputStream();
    OutputStream out = socket.getOutputStream();
    // Read from in
    // Write to out
    in.close();
    out.close();
}
````

How can I validate the data that I read from and write to the socket's streams?
Or how could I test what happens when an exception is thrown while using these streams?
Java has this thing called [Anonymous Classes][anonymous-classes] that allows you to
inherit from and override functions of another class.

So if I wanted to test what happens when getInputStream fails, I could just override 
that function to throw an exception!

````java
@Test
public void testSocket() {
    Socket socket = new Socket() {
        @Override
        public InputStream getInputStream() throws IOException {
            throw new IOException();
        }
    }
    doStuff(socket);
    // Validate socket
}
````

If you're wanting to test regular scenarios that you expect to occur, you can completely
rewrite most of the original implementation of the dependent class. All Java cares about 
is that it is an object of that type.

````java
@Test
public void testSocket() {
    Socket socket = new Socket() {
        private boolean open = true;
        @Override
        public void close() { open = false; }
        @Override
        public void isClosed() { return !open; }
    }
    doStuff(socket);
    Assert.assertTrue(socket.isClosed())
}
````

This is especially handy when there are several scenarios that need to be tested,
and you don't exactly want to create a new class for each individual scenario.

[anonymous-classes]: https://docs.oracle.com/javase/tutorial/java/javaOO/anonymousclasses.html