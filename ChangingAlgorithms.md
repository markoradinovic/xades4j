A service provider for algorithm selection is represented by the `AlgorithmsProviderEx` interface. It is used to obtain the different algorithms (at least their URIs) that are used in signature production: the signature algorithm itself, the digest algorithm for `Reference` elements and for properties, the canonicalization algorithm, etc.

```
public interface AlgorithmsProviderEx
{
    Algorithm getSignatureAlgorithm(String keyAlgorithmName);
    Algorithm getCanonicalizationAlgorithmForSignature();
    Algorithm getCanonicalizationAlgorithmForTimeStampProperties();
    String getDigestAlgorithmForDataObjsReferences();
    String getDigestAlgorithmForReferenceProperties();
    String getDigestAlgorithmForTimeStampProperties();
}
```

The library includes a default implementation of this provider that uses a specific set of algorithms:

  * Signature: RSA(RSA\_SHA256), DSA(DSA\_SHA1)
  * Canonicalization: Canonical XML 1.0 withouth comments
  * Digest: SHA256 (data objects and reference properties); SHA1 (time-stamps)

Any other implementation can be used, such as one that gets the algorithms from a combo box in the GUI. As with other providers, **one sets the algorithms provider on the signing profile**. Any `XadesSigner` created by that profile will use the algorithms specified in the algorithms provider.

The `Algorithm` class is a representation of an algorithm, which may include parameters. The `xades4j.algorithms` package includes several subclasses for specific algorithms as well as a class for generic use (such as algorithms that don't take any parameter). Some of the classes on the package are also used for data object transforms.