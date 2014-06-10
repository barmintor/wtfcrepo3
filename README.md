wtfcrepo3: A FAQ for Fedora Commons Repository v3.x
===================================================

1. *What's a simple description of Fedora Commons Repository (FCRepo), conceptually speaking?*  
   FCRepo is a web-facing system for identifying resources, describing them with shallow RDF, and attaching file-like contents to them. A FCRepo repository is like a network graph with files dangling off it. That metaphor drives the fundamental concepts in FCRepo 3, **objects** and **datastreams**.
  1. *Ok, what are FCRepo Objects?*  
    An object is a node in the repository graph. It is identified with a local ID called its PID. The PID consists of two parts: A namespace and a name (usually a sequentially generated number) joined with a colon (':'). In the repository, this PID is used to compose the URI for an object, which will be in the form "info:fedora/$PID".
    Objects have **properties** and **datastreams**.
  2. *And FCRepo Datastreams?*  
    Datastreams are streams of bytes identified in the context of a FCRepo object. Like Objects, they have properties. It's no accident that they sound a lot like files; they usually start out that way. But that may also be pointers to streams of bytes in the form of a URL. Datastreams and their contents can be versioned in FCRepo 3.  

2. *How are simple FCRepo objects structured?*  
   Objects minimally have a PID, some core properties, and the 3 required system datastreams: DC, RELS-EXT, and AUDIT. There is also an optional system datastream: RELS-INT.
  1. *DC? That's great, can we just use our existing Dublin Core?*  
     Not exactly. FCRepo 3 expects the DC datastream to consist of only the [15 core DC elements](http://dublincore.org/documents/dces/), serialized as XML, and wrapped in an container element borrowed from OAI. FCRepo also inserts the PID as a DC:Identifier value. When the DC stream is updated, its contents are parsed and re-serialized, so it is not naively possible to verify the contents post-update by checksum. The DC datastream was intended to contain minimal description to assist repository administrators. Its contents are searchable in a rudimentary application bundled with FCRepo, and indexed into the backing triplestore if it is enabled.  
  2. *What's RELS-EXT?*  
    RELS-EXT is an RDF-XML serialization of triples for which the containing FCRepo object is the subject. It also has a format requirement: The container element must be an RDF:Description, with an "about" attribute indicating the object by internal URI (this means that rdf:type assertions must be made explicitly as contained statements). Like the DC stream, FCRepo will parse and reserialize RELS-EXT data. The contents of RELS-EXT are indexed into the backing triplestore if it is enabled.  
  3. *What's RELS-INT?*
    RELS-INT is very similar to RELS-EXT, but rather than serializing triples about the FCRepo object, it serializes triples whose subject is a datastream in the same FCRepo object context. If used, its contents are also indexed into the triplestore.  
  4. *What's AUDIT?*  
    AUDIT is a rudimentary accounting of changes to the object. It is not directly modifiable, and its contents are not indexed anywhere by default.  
  5. *What are the core object properties?*  
    * info:fedora/fedora-system:def/model#state, with possible values Active, Inactive and Deleted
    * info:fedora/fedora-system:def/model#label, a plain text title for the object
    * info:fedora/fedora-system:def/model#ownerId, the semicolon delimited list of FCRepo user IDs that own the object (by default fedoraAdmin)
    * info:fedora/fedora-system:def/model#createdDate, the date created
    * info:fedora/fedora-system:def/view#lastModifiedDate  
    The core object properties are indexed into both the basic search app's index and the triplestore (if enabled). Unfortunately, the object's core properties are not versioned because of an idiosyncracy in FCRepo's object serialization. 
3. *How does FCRepo 3 store objects?*  
   This is where XML enters the picture: FCRepo serializes the tree of the object, its properties, and its datastreams (though not usually their content) as an XML document using a markup called <B>F</b>edora <b>O</b>bject <b>XML</b>, or *FOXML*. FOXML documents encapsulate versions of datastreams with pointers to their content or, in some cases, inline XML of their content. The FOXML document approximates what digital preservationists call an <b>A</b>rchival <b>I</b>nformation <b>P</b>ackage (*AIP*). While datastream properties (for all versions) are present inline in the FOXML, datastream contents will normally be indicated with a URI. The format of this URI will vary according to whether the datastream's contents are managed by FCRepo (that is, in FCRepo's storage) or externally (either as a file-system URI or an HTTP URL).  
   The location of this XML document will depend on the configuration FCRepo's storage module, but current defaults will place it in a shallow hierarchy $FEDORA_HOME/data/objectStore based on the MD5 hash of the FCRepo object's internal URI.  

4. *Are there limits to the size of files that can be ingested into FCRepo 3?*  
   Until recent iterations of FCRepo3, the archaism of the Java Web App specs limited uploaded parts to 2Gb (owing to the definition of body length as a signed 32-bit integer), and thus implicitly limited the size of POSTed datastream contents. This *ought* no longer to be the case (file bugs where applicable), but the easiest way to deal with such files is by passing references to the content instead (the dsLocation parameter in the REST api). One significant departure would be datastreams with inline XML content- such datastreams must be representable as a byte array, and are thus limited to 2Gb (please, do not create inline XML datastreams this large).  

5. *Can you ingest files of any type into FCRepo 3 (including unknown MIME types, or obsolete formats)?*  
   FCRepo is agnostic about the format of managed or external datastream contents (with the obvious exceptions of the system datastreams). The drawbacks of unidentified MIME types will be in the inferred download name, which for datastreams with no MIME or with application/binary will be given the suffix '.bin' (this can be circumvented by giving the datastream a dsLabel property). If the MIME is inadequate to identifying the format, it can be elaborated with the datastream's formatURI property, which is largely documentary (eg, assign a URI from the PRONOM registry to this property).  


Contributors
============
\#wtfcrepo3
Ben Armintor (@barmintor)
Andrew Berger (@andrewjbtw)
