## Abstraction Level ##

The XAdES4j library is an high-level, configurable and extensible solution for XAdES signature services. Its API is designed to abstract the developer from the signature XML structure and processing rules, which are handled internally. Note that every XAdES signature has a `SignedInfo` with `Reference`s, including the one over the `SignedProperties` element, and an `Object` where the `QualifyingProperties` element is appended. As such, the library can collect the needed information and systematically handle the core signature structure and the XAdES incorporation rules.

This approach includes handling all the detailed property contents which often involve digest calculations, interaction with TSAs and associations between XML elements. An example of this approach is to represent the `CompleteCertificateRefs` property - which contains digests of certificates - with a collection of Java’s `X509Certificate` instances. During signature production the library calculates the corresponding digests and adds them to the final DOM tree using the appropriate structure.

The base principle is that the types that represent the qualifying properties and are used by the developer contain _high-level_ data, different from the one in the properties’ final structure.

## Service Providers ##

The library's abstraction level results in the need of additional services, such as obtaining message digest engines and interacting with TSAs. Furthermore, other services are required for signature production and verification, namely signing certificate selection, certificate validation and time-stamp tokens validation. XAdES4j handles these services as exchangeable components (**service providers**), which are plugged into the library through well defined interfaces and can be configured independently without altering the core mechanisms.

Note that some of the outcomes of these services are inputs for signature production or verification. However, directly depending on the needed information is quite restrictive. For instance, consider a scenario where the keying information is stored on a smart card. If the signing key (actually its in-memory reference) is directly used as an input for the signature profile, the user will have to be prompted for the corresponding PIN before the actual signature operation. A better approach is to have an entity that is responsible for providing the signing key right when it is needed. The library is not depending on the key itself but on a means of obtaining it. Furthermore, the specific actions undertaken to obtain the key are not relevant to the signing process: it might be obtained from a Java key store or a Windows key store, but that won’t change the signing process as a whole.

The approach just described for obtaining the signing key is also used on the other tasks presented above. For instance, instead of directly handling validation information, the verification process relies on an entity that validates certificates. Furthermore, the algorithms involved in the signature are also obtained through one of those entities. A possible realization is asking the user which algorithms should be used; another option is to have a default set of algorithms that is always used in a given context.

The library is complete, meaning it includes one or more implementations for each configurable service provider. Some of them are configured by default to make the library’s usage straightforward in multiple scenarios. Nevertheless, new providers may be created and plugged in as needed.

## Profiles ##

In _XAdES4j_ the inputs for signature production are classified in two groups. The first includes the signature properties and the cryptographic parameters and keying information, which encompass characteristics of the signer/signature. The second group consists of signed data objects and their properties, which define the resources being signed.

Considering the signatory as an entity that produces multiple signatures in a specific context, it is likely that his characteristics are similar on these different signatures, while the signed resources vary. Based on that observation, XAdES4j defines the set of invariant characteristics of the signatory and its signatures as a **signature profile**.

A signature profile is used to create a _signer_, i.e. an entity that produces signatures according to the characteristics on that profile. On a given context, multiple resources may be signed using the same signer, hence resulting on signatures with similar structure but over different content.

The signature profile concept is also applicable to signature verification. Although there’s no need to define signature characteristics, the features of the verification process still have to be defined, namely the cryptographic validation information that should be used. As for signature production, there is a **verification profile** that encompasses the characteristics of the signature verification process.

As previously describer, the information that defines a signature is collected through service providers. Therefore, a profile, for both signature production and verification, is a means of identifying the service providers that should be used in the corresponding signature operation.