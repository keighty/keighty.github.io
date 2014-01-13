---
layout: post
title: "Serving Some Java"
comments: true
description: ""
category: java
tags: []
---

For a recent java coding challenge I was asked, among other things, to make a server. It has been several years since I last attempted it, and I had forgotten how easy it could be.

#### Server
A server, like any good conversationalist, is a good listener. A server is a program running on a computer that listens at a port, waiting to receive instructions.

{% highlight java linenos %}
public class Server {
  private static String inputLine;

  public static void main(String[] args) {

    private static final int PORT_NUMBER = 63400

    try {
      // Socket variables
      ServerSocket serverSocket = new ServerSocket(PORT_NUMBER);
      Socket clientSocket = serverSocket.accept();

      // IO variables
      InputStreamReader inputReader = new InputStreamReader(clientSocket.getInputStream());
      BufferedReader bReader = new BufferedReader(inputReader);

      while ((inputLine = bReader.readLine()) != null) {
        System.out.println(inputLine);
      }

    } catch (Exception e) {
      System.out.println(e);
    }
  }
}{% endhighlight %}

The server opens a socket on a specified port (in this case, port 63400). A socket is the point at which two-way communication happens between computer programs. The server opens a socket at a port and waits for a client to bind the socket so they can exchange information. It waits for input from the client, with instructions to print out anything the client puts in (line 18). So where is the client?

#### Client
The client is even simpler than the server: it finds the server at the specified port and connects to the socket.

{% highlight java linenos %}
public class Client {

  private static final int PORT_NUMBER = 63400

  public static void main(String[] args) {

    try {

      // Socket variables
      Socket socket = new Socket("localhost", PORT_NUMBER);
      PrintWriter pWriter = new PrintWriter(socket.getOutputStream(), true);

      pWriter.println("Hello World!");

    } catch (Exception e) {
      System.out.println(e);
    }
  }
}{% endhighlight %}

With a successful connection, it generates an output stream. If you start the server in one console it will look like it isn't doing anything, but when you start the client in another console you can watch them communicate. The client writes "Hello World" directly into the port where the server is listening (socket.getOutputStream()), and as instructed, the server obediently prints "Hello World" to the console.

Awesome.