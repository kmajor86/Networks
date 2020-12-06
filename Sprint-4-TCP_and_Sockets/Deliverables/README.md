# Sprint 4 &ndash; Deliverables

<img src="https://imgs.xkcd.com/comics/the_cloud.png" width="85%" />

<strong>Group Members/Collaborators:</strong> Eric Grimm, Jaysa Ramirez, Nikola Vuckovic

<strong>Honor Code:</strong> “On my honor, I have not given, nor received, nor witnessed any unauthorized assistance on this work.”
-- Kyle Major

## Web Server

### Description and Goals
For this project, you’ll create a functional web server capable of receiving and handling HTTP requests. When your server is complete, you should be able to connect to it using a web browser and view the pages it supplies. This project will give you insight into how the HTTP protocol works and how a web server uses HTTP to deliver web pages.
This project uses all of the ideas from our previous two client-server projects, including the sockets interface and multi-threaded request handling. Therefore, the code from the previous two projects may be useful resource.
Getting Started

This directory contains the following items.
- This write-up
- `webserver.c`, the shell program that you’ll complete
- `test.html`, a sample web page
- `image.jpg`, a test image used by test.html
- A directory called `files` that contains `hello.txt`, another test file
- `forms.html`, a simple form that submits data using the POST method

### Developing the Web Server
Begin by looking at `webserver.c`. The `main` routine is similar to our other server programs: it creates a socket connection, binds it to a port, then listens in a loop for incoming connections. When a client establishes a new connection, the server spins off a new thread that uses listen for messages to receive incoming requests. When the listening thread receives a new HTTP request it calls handle request, passing in the buffer containing the message.

**Handling requests**
   
Most of the server’s work, and most of yours, happens in `handle_request`. The function first parses the input HTTP header to obtain the HTTP method (which should be GET), the Uniform Resource Indicator (URI), and HTTP version. It further parses the URI to obtain the relative path to the requested file.

The HTTP protocol defines several methods that clients and servers may use to exchange information. Your server only needs to support GET, which web browsers use to retrieve pages and files from the server. If the method type is anything other than GET, the server returns a 501 error response indicating that the server does not implement the method. The function `send_error` response automatically constructs both the appropriate HTTP response header and a simple web page, then sends the response back to the client.

**Opening files and checking for errors**

Next, the server attempts to open the file; if the file does not exist, the server should return a `404: Not Found` error message. If the file exists, the server uses `stat` to learn its size, then uses `mmap` to map the contents of the file to a buffer in memory. `mmap` returns a pointer of type `char *` that refers to the file contents. This allows the server to send the file data over the connection without explicitly reading the entire file into a buffer using read.

**Sending the response message**

The final steps are to construct the header and body of the HTTP response and send them. The header should have the following format:

```
HTTP/1.0 200 OK
Server: CMS450 Web Server
Content-Length: 795
Content-Type: text/html
                                        <---- Required empty line
```

Each line of the header ends with a carriage return-line feed combination (`\r\n`), with an additional blank `\r\n` after the last line of the header. The `Content-Length` field is the size of the file in bytes. `Content-Type` is a special string indicating the type of the returned file; for HTML files, the type is `text/html`. The `get_filetype` function automatically processes the filename to determine a reasonable HTTP file type and returns the appropriate string.

The final step is to send the response header and file over the socket connection to the client. Use the `send_response` method, which you’ll also fill in.

### Running the Server
Simply compile with `gcc` and run the server in the regular terminal window. By default, the server runs on `localhost` at port 80. View the port through your Mimir interface and change
the URL to load different files.

### Behaviors

- Load `test.html`, `image.jpg`, and `files/hello.txt` correctly

- Load any other files that might be added to the project directory (file names can't be hardcoded)

- Return a 404 on attempting to load a file that doesn't exist.

- Return a 501 error message on a POST request generated by `forms.html`