# Types of Signed Data Objects #

The possible types of signed data objects are `DataObjectReference`, `EnvelopedXmlObject` and `AnonymousDataObjectReference`. The signed data objects are supplied to the signer using the `SignedDataObjects` class and the signature will contain one `Reference` element for each of those objects.

```
DataObjectReference obj1 = ...;
DataObjectReference obj2 = ...;
EnvelopedXmlObject obj3 = ...;
SignedDataObjects dataObjs = new SignedDataObjects(obj1, obj2, obj3);
```

## `DataObjectReference` ##

`DataObjectReference` is used for same-document or external URI references. When using an instance of `DataObjectReference` the URI of the resulting `Reference` element is the one specified in that instance. The _type_ of the `Reference` can also be specified.

```
DataObjectReferenceobj1 = new DataObjectReference("http://www.ietf.org/rfc/rfc3161.txt");
DataObjectReferenceobj2 = new DataObjectReference("#root");
```

The reference URI can also be relative. In that case, the base URI to resolve the references should be specified on the `SignedDataObjects` class.

```
DataObjectReferenceobj1 = new DataObjectReference("rfc3161.txt");
SignedDataObjects objs = new SignedDataObjects(obj1).withBaseUri("http://www.ietf.org/rfc/")
```

## `EnvelopedXmlObject` ##

`EnvelopedXmlObject` is used for content that will be added to an `Object` element within the signature. In this case the signature is an _enveloping_ signature.

```
Node objContent = ...;
EnvelopedXmlObject obj = new EnvelopedXmlObject(objContent);
```

When using `EnvelopedXmlObject` the library creates an `Object` element with the specified content and a corresponding `Reference` that covers that element.

## `AnonymousDataObjectReference` ##

`AnonymousDataObjectReference` is used for references without the URI attribute (at most one reference per signature). For these references, the referenced data must be directly supplied by the application and the receiver is expected to do the same when verifying the signature.

```
InputStream objStream = ...;
AnonymousDataObjectReference obj = new AnonymousDataObjectReference(objStream);
```

Note that `DataObjectReference` also supports file URIs and relative URIs, which may avoid some usages of `AnonymousDataObjectReference`.

# Custom `ResourceResolvers` #

Apache Santuario supports different ways of resolving data object references by using `ResourceResolver`s. It is also extensible, allowing the configuration of both global and per-signature resolvers. When producing a signature custom resolvers can be added through the `SignedDataObjects` class:

```
SignedDataObjects dataObjs = new SignedDataObjects()
   .withSignedDataObject(new DataObjectReference("my://ref"))
   .withResourceResolver(new MyResourceResolver());
```

These resolvers are considered in the same order they are added and have priority over the globally registered resolvers.

# Transforms #

All the signed data objects can be subject to transforms. The `DataObjectDesc` class is the base for the types presented on the previous section and allows the definition of the transforms that should be applied to the object. The library supports some of the most common transforms and a generic transform with parameters.

```
DataObjectDesc obj1 = new DataObjectReference("#someId")
   .withTransform(XPath2Filter.subtract("xpath1").intersect("xpath2"));
DataObjectDesc obj2 = new DataObjectReference("#root")
   .withTransform(new EnvelopedSignatureTransform());
```