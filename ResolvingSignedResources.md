In some cases, such as when using anonymous data object references, the data contained in the signature is not sufficient to resolve the signed data objects. To configure those aspects the `XadesVerifier.verify` method can be supplied with an instance of the `SignatureSpecificVerificationOptions`.

## Base URI for relative references ##

```
SignatureSpecificVerificationOptions options = new SignatureSpecificVerificationOptions().useBaseUri("http://www.ietf.org/rfc/");
```

## Data for anonymous referenecs ##

```
InputStream data = //...
SignatureSpecificVerificationOptions options = new SignatureSpecificVerificationOptions().useDataForAnonymousReference(data);
```

## Custom `ResourceResolver`s ##

```
ResourceResolver resolver = //...
SignatureSpecificVerificationOptions options = new SignatureSpecificVerificationOptions().useResourceResolver(resolver);
```