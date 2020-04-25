##### Networking

https://hpbn.co/
```
Performance is a feature. This book provides a hands-on overview of what every web developer needs to know about the various types of networks (WiFi, 3G/4G), transport protocols (UDP, TCP, and TLS), application protocols (HTTP/1.1, HTTP/2), and APIs available in the browser (XHR, WebSocket, WebRTC, and more) to deliver the best—fast, reliable, and resilient—user experience.

★★★★½ (463): GoodReadsAmazonO'Reilly

This book is required reading for anyone who cares about web performance; it's already established as the go-to reference on the topic.

—Mark Nottingham (IETF HTTPBis Chair)

§Table of Contents
Networking 101
Primer on Latency and Bandwidth
Speed Is a Feature
The Many Components of Latency
Speed of Light and Propagation Latency
Last-Mile Latency
Bandwidth in Core Networks
Bandwidth at the Network Edge
Delivering Higher Bandwidth and Lower Latencies
Building Blocks of TCP
Three-Way Handshake
+
Congestion Avoidance and Control
Bandwidth-Delay Product
Head-of-Line Blocking
+
Optimizing for TCP
Building Blocks of UDP
Null Protocol Services
+
UDP and Network Address Translators
Optimizing for UDP
Transport Layer Security (TLS)
Encryption, Authentication, and Integrity
HTTPS Everywhere
+
TLS Handshake
+
TLS Session Resumption
Chain of Trust and Certificate Authorities
+
Certificate Revocation
TLS Record Protocol
+
Optimizing for TLS
Testing and Verification
Performance of Wireless Networks
Introduction to Wireless Networks
Ubiquitous Connectivity
Types of Wireless Networks
+
Performance Fundamentals of Wireless Networks
Measuring Real-World Wireless Performance
WiFi
From Ethernet to a Wireless LAN
WiFi Standards and Features
+
Measuring and Optimizing WiFi Performance
+
Optimizing for WiFi Networks
Mobile Networks
+
Brief History of the G’s
+
Device Features and Capabilities
+
Radio Resource Controller (RRC)
+
End-to-End Carrier Architecture
+
Packet Flow in a Mobile Network
Heterogeneous Networks (HetNets)
Real-World 3G, 4G, and WiFi Performance
Optimizing for Mobile Networks
Preserve Battery Power
+
Eliminate Periodic and Inefficient Data Transfers
+
Anticipate Network Latency Overhead
Design for Variable Network Interface Availability
Burst Your Data and Return to Idle
Offload to WiFi Networks
Apply Protocol and Application Best Practices
HTTP
Brief History of HTTP
HTTP 0.9: The One-Line Protocol
HTTP/1.0: Rapid Growth and Informational RFC
HTTP/1.1: Internet Standard
HTTP/2: Improving Transport Performance
Primer on Web Performance
Hypertext, Web Pages, and Web Applications
+
Anatomy of a Modern Web Application
+
Performance Pillars: Computing, Rendering, Networking
Synthetic and Real-User Performance Measurement
Browser Optimization
HTTP/1.X
Benefits of Keepalive Connections
HTTP Pipelining
Using Multiple TCP Connections
Domain Sharding
Measuring and Controlling Protocol Overhead
Concatenation and Spriting
Resource Inlining
HTTP/2
Brief History of SPDY and HTTP/2
Design and Technical Goals
Binary Framing Layer
Streams, Messages, and Frames
Request and Response Multiplexing
Stream Prioritization
One Connection Per Origin
Flow Control
Server Push
Header Compression
Upgrading to HTTP/2
+
Brief Introduction to Binary Framing
Optimizing Application Delivery
Optimizing Physical and Transport Layers
+
Evergreen Performance Best Practices
Optimizing for HTTP/1.x
+
Optimizing for HTTP/2
Browser APIs and Protocols
Primer on Browser Networking
Connection Management and Optimization
Network Security and Sandboxing
Resource and Client State Caching
Application APIs and Protocols
XMLHttpRequest
Brief History of XHR
Cross-Origin Resource Sharing (CORS)
Downloading Data with XHR
Uploading Data with XHR
Monitoring Download and Upload Progress
Streaming Data with XHR
+
Real-Time Notifications and Delivery
XHR Use Cases and Performance
Server-Sent Events (SSE)
EventSource API
Event Stream Protocol
SSE Use Cases and Performance
WebSocket
+
WebSocket API
+
WebSocket Protocol
+
WebSocket Use Cases and Performance
Performance Checklist
WebRTC
Standards and Development of WebRTC
+
Audio and Video Engines
+
Real-Time Network Transports
+
Establishing a Peer-to-Peer Connection
+
Delivering Media and Application Data
+
DataChannel
+
WebRTC Use Cases and Performance
Performance Checklist
§About the author
Ilya Grigorik is a web performance engineer at Google and co-chair of the W3C Web Performance Working Group. Follow him on his blog and Twitter for the latest web performance news, tips, and talks.
```

This is Ilya Grigorik's blog:
```
Ilya Grigorik
Web Performance Engineer
Director, Developer Relations at Google
Author of High Performance Browser Networking
Entrepreneur, instigator, photographer
```

https://www.igvita.com/

This includes posts and videos:
```
Recent Postsview all posts
Stop Cross-Site Timing Attacks with SameSite cookies26 Aug 2016
Building Fast & Resilient Web Applications20 May 2016
Control Groups (cgroups) for the Web?01 Mar 2016
The "Average Page" is a myth12 Jan 2016
Don't lose user and app state, use Page Visibility20 Nov 2015
Eliminating Roundtrips with Preconnect17 Aug 2015
Browser Progress Bar is an Anti-pattern25 Jun 2015 
Fixing the 'Blank Text' Problem10 Apr 2015
Resilient Networking: Planning for Failure26 Jan 2015
Capability Reporting with Service Worker15 Dec 2014
Events & Conferencesview all talks
image.performance.now().reject(reasons) (video)performance.now()
Building the web of tomorrow, together (video)WeAreDevelopers
How’s the UX on the Web, Really? (video)SmashingConf London
Delivering Fast and Resilient Applications (video)Google I/O
HTTP/2 Performance Anti-patterns (video)
```