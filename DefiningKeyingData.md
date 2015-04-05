# Supplying Keying Data #

When producing a signature, the signing key and certificate are supplied through a `KeyingDataProvider`. The interface for this provider has two methods to obtain the signing key and certificate, respectively.

```
public interface KeyingDataProvider
{
   List<X509Certificate> getSigningCertificateChain();
   PrivateKey getSigningKey(X509Certificate signingCert);
}
```

During signature production the `getSigningCertificateChain` method is the first to be invoked. When the signature operation is actually going to be applied, the `getSigningKey` method in invoked with the first certificate in the chain. The certificate is supplied to ease the provider's implementation if it manages multiple keys.

## Using File System Key Stores ##

A common way to provide keying data is using key stores, namely those stored in the file system. To that end, _XAdES4j_ includes the `FileSystemKeyStoreKeyingDataProvider` class. It uses three helper interfaces:

  * `KeyStorePasswordProvider` - provides the password to access the key store.
  * `SigningCertSelector`  - selects the signing certificate among the certificates associated to private key entries in the key store, hence selecting the signing key.
  * `KeyEntryPasswordProvider` - provides the password to access a specific key store entry (private key entry).

The different types of file system key stores supported in the Java platform (JKS, PKCS#12, ...) can be used when creating a file system keying data provider.

```

public class DirectPasswordProvider implements KeyStorePasswordProvider, KeyEntryPasswordProvider {...}

public class FirstCertificateSelector implements SigningCertSelector {...}

KeyingDataProvider kp = new FileSystemKeyStoreKeyingDataProvider(
                "pkcs12",
                "my/LG.pfx",
                new FirstCertificateSelector(),
                new DirectPasswordProvider("storepwd"),
                new DirectPasswordProvider("keypwd"),
                true);
```

## Using PKCS#11 Key Stores ##

Another common way to provide keying data is using PKCS#11 key stores, such as smart-cards. To that end, _XAdES4j_ includes the `PKCS11KeyStoreKeyingDataProvider` class, which uses the Java's PKCS11 provider that in turn is a wrapper to a native provider. The PKCS#11 keying data provider uses the same helper interfaces that are used by the file system keying provider. However, the key store and key entry protections are often handled by the native lib, which means they can be omitted. The provider is configured with the path to the native library and a name.

```
KeyingDataProvider kp = new PKCS11KeyStoreKeyingDataProvider(
               "path/to/native/lib",
               "name",
               new FirstCertificateSelector(),
               null, null, false);
```