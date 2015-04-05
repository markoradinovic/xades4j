# Signature Production #

_XAdES4j_ supports producing signatures in any of the four main XAdES forms (XAdES-BES, XAdES-EPES, XAdES-T and XAdES-C).

All the optional qualifying properties in XAdES 1.4.1, i.e. the ones that are not part of any specific form. As for mandatory properties, the library doesn't support the ones related to attribute certificates, such as `AttributeCertificateRefs` and `AttrAuthoritiesCertValues`. In addition, the use of OCSP is not yet supported.

Generating a XAdES signature with _XAdES4j_ consists of two tasks. The first is to create a _signer_ which is represented by the `XadesSigner` interface. The second is to define the resources being signed.

## Creating a _Signer_ ##

Signers are created through `XadesSigningProfile`s which are a means to configure the service providers that should be used by the signer. The only mandatory provider is the keying provider, i.e. the one that is used by the signer to obtain the signing key/certificate.

```
KeyingDataProvider kp = new FileSystemKeyStoreKeyingDataProvider(...);
XadesSigningProfile p = new XadesBesSigningProfile(kp);
XadesSigner signer = p.newSigner();
```

Since each XAdES form demands specific information (policy document, validation data), there are diferent profile types for each of them: `XadesBesSigningProfile`, `XadesEpesSigningProfile`, `XadesTSigningProfile` and `XadesCSigningProfile`. Each type enables the configuration of the appropriate service providers.

## Defining the Signed Resources ##

The possible types of signed data objects are `DataObjectReference`, `EnvelopedXmlObject` and `AnonymousDataObjectReference`. The first is used for same-document or external URI references, while the second is used for content that will be added to an `Object` element. The last type is used for references without the `URI` attribute (at most one reference per signature). These classes enable the specification of the data object's qualifying properties. Qualifying properties that apply to all the signed data objects can also be defined using the `SignedDataObjects` container.

```
DataObjectDesc obj = new DataObjectReference("http://...").withDataObjectTimeStamp();
SignedDataObjects dataObjs = new SignedDataObjects(obj).withCommitmentType(AllDataObjsCommitmentTypeProperty.proofOfOrigin());
```

All`DataObjectReference`, `EnvelopedXmlObject` and `AnonymousDataObjectReference` represent _signed content_. This means that a `Reference` is **always added to the signature**. When using an instance of  `DataObjectReference` the URI of the resulting `Reference` element is the one specified in that instance. When using `EnvelopedXmlObject` the library creates not only an `Object` element with the specified XML content but also a `Reference` that covers that element. Finally, when using an instance of  `AnonymousDataObjectReference` the URI attribute of the resulting `Reference` element is ommited.

All the signed data objects can be subject to transforms. The library supports some of the most common transforms and a generic transform with parameters.

```
DataObjectDesc obj = new DataObjectReference("#someId")
   .withTransform(new XPath2FilterTransform(XPathFilter.subtract("/excludedElem")))
   .withTransform(new GenericDataObjectTransform("http://transform.uri"));
```

## Generating the Signature ##

After configuring the signer and defining the signed data objects, applying the signature is straightforward.

```
Element sigParentNode = ...; // The DOM node to which the signature will be appended (Element or Document)
signer.sign(dataObjs, sigParentNode);
```