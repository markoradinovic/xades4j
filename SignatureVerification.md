# Signature Verification #

_XAdES4j_ can verify signatures in any of the four main XAdES forms. This includes the core XML-DSIG verificationand XAdES property validation.

In order to verify a signature with one has to create a _verifier_ which is represented by the `XadesVerifier` interface. The verifier can be used to verify different signatures, returning detailed information about each of them (validation data, algorithms, qualifying properties, signed data objects and so on).

## Creating a _Verifier_ ##

As in signature production, verifiers are created using a profile, namely `XadesVerificationProfile`.  This is a means to configure the service providers that should be used by the verifier. The only mandatory provider is the certificates validator, i.e. the one that is used by the verifier whenever it needs to validate a certificate (signing certificate, TSA certificates).

_XAdES4j_ includes a PKIX certificate validator that relies on the JCA PKIX classes as well as an helper class to load intermediate certificates and CRLs from the file system.

```
FileSystemDirectoryCertStore certStore = new FileSystemDirectoryCertStore("path/to/dir");
KeyStore trustAnchors = ...;
CertificateValidationProvider certValidator = new PKIXCertificateValidationProvider(trustAnchors, false, certStore.getStore());

XadesVerificationProfile p = new XadesVerificationProfile(certValidator);
XadesVerifier v = p.newVerifier();
```

Other providers, such as message digest engine providers and time-stamp verifiers, have default implementations that can be overridden through the profile.

## Verifying the Signature ##

Having the verifier and the signature DOM element, verification is straightforward. The process returns a set of information about the signature, such as its form and qualifying properties.

```
Element sigElem = ...;
XAdESVerificationResult r = verifier.verify(sigElem);

System.out.println(r.getSignatureForm());
System.out.println(r.getSignatureAlgorithmUri());
System.out.println(r.getSignedDataObjects().size());
System.out.println(r.getQualifyingProperties().all().size());
```

### Accessing Qualifying Properties ###
`XAdESVerificationResult` provides access to information about a verified signature, such as algorithms, signed data objects, and qualifying properties. To easily access the high-level property data for a specific type of property, the `getPropertiesFilter` method can be used:
```
XAdESVerificationResult result = ...
Collection<SignatureTimeStampProperty> timeStamps = result 
        .getPropertiesFilter()
        .getOfType(SignatureTimeStampProperty.class);

for (SignatureTimeStampProperty ts : timeStamps)
{
    System.out.println(ts.getTime());
}
```