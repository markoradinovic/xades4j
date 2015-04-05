# `KeyInfo` content #

The library includes a provider named `BasicSignatureOptionsProvider` which can be used to control some basic signature options, including ones related to the `KeyInfo` element. It also allows to decide whether the signing certificate itself should be signed.

```
public interface BasicSignatureOptionsProvider
{
  boolean includeSigningCertificate();
  boolean includePublicKey();
  boolean signSigningCertificate();
}
```

As with the other providers, `BasicSignatureOptionsProvider` can be configured on the signing profiles.

# Signature position on the DOM tree #

The `XadesSigner` interface has an overload of the `sign` method that enables controlling how the signature element is appended to the DOM tree:

```
public XadesSignatureResult sign(
  SignedDataObjects signedDataObjects,
  Node referenceNode,
  SignatureAppendingStrategy appendingStrategy);
```

To that end, a `SignatureAppendingStrategy` should be used. It is passed the signature element and the node that was passed to the `sign` method.

```
public interface SignatureAppendingStrategy
{
  void append(Element signatureElement, Node referenceNode);
  //...
}
```

The library includes some common appending strategies, define on the `SignatureAppendingStrategies` class.