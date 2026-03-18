# Webserv: A High-Performance HTTP/1.1 Server


Webserv is a high-performance web server inspired by Nginx, developed in C++98. It features a robust, non-blocking I/O architecture capable of handling multiple concurrent connections, virtual hosting, and dynamic content through CGI.

The project was a collaborative effort by three developers, each focusing on critical layers of the server's architecture:
- **[cwoon](https://www.github.com/colin-woon)**: HTTP Protocol Layer, Router integration, and CGI Request/Response life-cycle.
- **[jothomas](https://github.com/SolR3g3m)**: Configuration parsing infrastructure and CGI output processing.
- **[tjun-fan](https://github.com/wesleytaetae)**: Networking substrate, socket management, and the `poll()`-based event loop.

## 🚀 Key Features

- **Event-Driven Architecture**: Utilizes `poll()` for non-blocking I/O, ensuring efficient resource usage and the ability to scale to high concurrency.
- **HTTP/1.1 Compliance**: Full support for GET, POST, and DELETE methods, along with chunked transfer encoding and keep-alive connections.
- **Virtual Hosting**: Support for multiple servers on the same host/port distinguished by the `Host` header.
- **Dynamic Content via CGI**: Robust CGI implementation supporting multiple interpreters (Python, PHP, Node.js) with configurable timeouts.
- **Flexible Configuration**: Powerful Nginx-like configuration system with support for hierarchical locations, aliases, redirects, and custom error pages.
- **Security & Stability**: Implements client body size limits, connection timeouts, and isolated CGI process management.

## 🏗️ Technical Architecture

### 1. Networking & Event-Loop (tjun-fan)
The core of the server is a high-performance event loop built on `poll()`. It manages:
- Listener socket initialization and non-blocking configuration.
- Client connection lifecycle management (Accept/Read/Write/Close).
- Timeout sweeping for stalled or expired connections.

### 2. Configuration Parsing (jothomas)
A custom Lexer and Parser translate human-readable `.conf` files into a hierarchical data structure. This includes:
- Tokenization of Nginx-style directives.
- Validation of server and location context constraints.
- Optimized lookup structures for rapid routing.

### 3. HTTP Layer & Protocol Logic (Colin)
The HTTP layer handles the complexity of the protocol's state machine:
- **Request Parsing**: Efficient parsing of headers and bodies, including large file uploads and chunked encoding.
- **Routing Engine**: Intelligent matching of request URIs to the most specific location blocks.
- **Method Handlers**: Specialized logic for GET (file serving/directory listing), POST (uploads/CGI), and DELETE.
- **Response Generation**: Automated generation of status codes, headers, and body content with support for custom error pages.

### 4. CGI Integration (Colin & jothomas)
Extends server functionality to run external scripts:
- **Execution**: Fork-exec model with pipe-based communication for non-blocking script execution.
- **Environment Management**: Dynamic generation of CGI environment variables.
- **Asynchronous Processing**: CGI I/O is integrated directly into the main `poll()` loop to prevent server blocking during script execution.

## 🛠️ Installation & Usage

### Prerequisites
- GCC/Clang with C++98 support
- Make

### Building the Server
```bash
make
```

### Running the Server
```bash
./webserv [configuration_file]
```
Example:
```bash
./webserv config/colin.conf
```

## 📊 Performance & Resilience
Webserv is designed to be resilient under heavy load. The non-blocking model ensures that a slow CGI script or a large file upload does not degrade the performance of other active connections. Comprehensive error handling and timeout mechanisms further ensure high availability.

---
*Developed as part of the 42 Core Curriculum project.*
