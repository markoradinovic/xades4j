# Enveloped Signatures #

XML signatures are often _enveloped_ signatures, i.e. signatures that are contained on the XML element being signed. The `Enveloped` helper class can be used to created this kind of signatures.

```
Element elemToSign = doc.getDocumentElement();
XadesSigner signer = new XadesTSigningProfile(...).newSigner();
new Enveloped(signer).sign(elemToSign);
```

The resulting signature contains a `Reference` to the specified element using the appropriate _enveloped signature transform_. Also, the `Signature` is appended to that element.

# Enveloping Signatures #

As defined by XML-DSIG an [enveloping signature](http://www.w3.org/TR/xmldsig-core/#def-SignatureEnveloping) is one that has signed content inside an `Object` element of the signature itself. In XAdES4j this type of signed content is represented by the `EnvelopedXmlObject` class. This type of signed objects wrap a DOM node with the content to be signed and are added to the signature just like other signed objects.

```
Document docSource = ...;
Document docDest = ...;
Node objContent = docDest.importNode(docSource.getDocumentElement(), true);
XadesSigner signer = ...;

DataObjectDesc obj = new EnvelopedXmlObject(objContent, "text/xml", null);
signer.sign(new SignedDataObjects(obj), docDest);
```

The resulting signature contains an `Object` element with the signed data and a `Reference` that covers that element, so that the content is actually included in signature generation.

# Detached Signatures over Non-XML Resources #

XAdES4j is based on Apache XML Security to implement the XML-DSIG core. This means that the `Reference` elements are built using Apache's API. Thus, the type of resources that can be signed using XAdES4j depend on the underlying support. Apache XML Security supports not only same-document XML references and resources on the Web (HTTP), but also resources on the local file system and custom resource resolvers. XAdES4j gives you straightforward support for producing and verifying detached signatures over any `InputStream`. The following excerpt illustrates signature production for that scenario:

```
XadesSigner signer = ...; // Create any signer using the profile that suits you
Document doc = ...; // The XML document (probably empty)

AnonymousDataObjectReference ref = new AnonymousDataObjectReference(new FileInputStream("path/to/file"));
SignedDataObjects objs = new SignedDataObjects(ref);
signer.sign(objs, doc);
```

The resulting signature contains a `Reference` without the `URI` attribute. Note that this assumes that the verifier also supports this feature.

The `SignedDataObjects` also supports an optional base URI, that may be used with relative references to create non-null `Reference` elements.

```
DataObjectDesc obj = new DataObjectReference("rfc3161.txt");
SignedDataObjects objs = new SignedDataObjects(obj).withBaseUri("http://www.ietf.org/rfc/");
signer.sign(objs, doc);
```