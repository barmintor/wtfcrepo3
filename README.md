wtfcrepo3: A FAQ for Fedora Commons Repository v3.x
===================================================

1. *What's a simple description of Fedora Commons Repository (FCRepo), conceptually speaking?*  
   FCRepo is a web-facing system for identifying resources, describing them with shallow RDF, and attaching file-like contents to them. A FCRepo repository is like a network graph with files dangling off it. That metaphor drives the fundamental concepts in FCRepo 3, **objects** and **datastreams**.
  1. *Ok, what are FCRepo Objects?*  
    An object is a node in the repository graph. It is identified with a local ID called its PID. The PID consists of two parts: A namespace and a name (usually a sequentially generated number) joined with a colon (':'). In the repository, this PID is used to compose the URI for an object, which will be in the form "info:fedora/$PID".
    Objects have **properties** and **datastreams**.
  2. *And FCRepo Datastreams?*  
    Datastreams are streams of bytes identified in the context of a FCRepo object. Like Objects, they have properties. It's no accident that they sound a lot like files; they usually start out that way. But that may also be pointers to streams of bytes in the form of a URL.


Contributors
============
\#wtfcrepo3
Ben Armintor (@barmintor)
Andrew Berger (@andrewjbtw)
