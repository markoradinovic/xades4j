# Signature Enrichment #

_XAdES4j_ enables the addition of extra unsigned qualifying properties to an existing signature. This is useful to extend the signature's form since all the forms above XAdES-EPES are based on unsigned properties. This is typically done during/right after verification because all the validation data is available at that time. Signature enrichment is based on the `XadesSignatureFormatExtender` interface and the whole process is implemented by reusing components from signature production.

## Creating an _Extender_ ##

As for other signature operations, an extender is created through a profile. In this case you don't need to specify any service provider, as they all can have defaults. Nevertheless, you may override those defaults.

```
XadesFormatExtenderProfile p = new XadesFormatExtenderProfile();
XadesSignatureFormatExtender ext = p.getFormatExtender();
```

## Extending a signature ##

The `XadesSignatureFormatExtender` contains a single method that is passed a XML signature and a set of unsigned properties. You can use this method directly but the best approach is to let the library do that job upon verification. To that end, the `XadesVerifier` interface has an additional method that verifies a signature and then extends its form by using the available information. This method is passed an extender and the minimum form that the signature should have after the execution. If the signature being verified has a "lower" format, the extender is used.

```
Element sigElem = ...;
XadesVerificationProfile p = ...;
XadesVerifier v = p.newVerifier();
// If the signature is a XAdES-BES or EPES, it will be extended to XAdES-T
v.verify(sigElem, ext, XAdESForm.T); 
```