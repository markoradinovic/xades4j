_XAdES4j_ allows the registration of additional verification logic on different moments of the verification process. All the verifiers presented below can be registered through `XadesVerificationProfile`.

# Early Verifiers #

To perform verifications on the signature structure just after unmarshalling, one should use `RawSignatureVerifier`s:

```
public interface RawSignatureVerifier
{
  void verify(RawSignatureVerifierContext ctx);
}
```

These verifiers are invoked after unmarshalling the signature and prior to any actual core or XAdES verification. A possible usage is checking for unsafe transforms on the data object references.

# Final Verifiers #

If some custom verifications are needed after the built-in verification process, one might use a `CustomSignatureVerifier`:

```
public interface CustomSignatureVerifier
{
    public void verify(
            XAdESVerificationResult verificationData,
            QualifyingPropertyVerificationContext ctx);
}
```

Invoking these verifiers is the last step of the verification process.

# Qualifying Property Verifiers #

To replace or extend the verification logic for a specific qualifying property, one should implement and register the appropriate `QualifyingPropertyVerifier`.

```
public interface QualifyingPropertyVerifier<TData extends PropertyDataObject>
{
    public QualifyingProperty verify(
            TData propData,
            QualifyingPropertyVerificationContext ctx);
}
```

To access the default verifier for a given qualifying property, custom verifiers should have a constructor dependency and use the `BuiltIn` annotation. For instance, to extend the verification of the `SigningTime` property one might do:

```
class MySigningTimeVerifier implements QualifyingPropertyVerifier<SigningTimeData>
{
    private final QualifyingPropertyVerifier<SigningTimeData> builtInVerifier;

    @Inject
    public MySigningTimeVerifier(
            @BuiltIn QualifyingPropertyVerifier<SigningTimeData> builtInVerifier)
    {
        this.builtInVerifier = builtInVerifier;
    }

    @Override
    public QualifyingProperty verify(SigningTimeData propData, QualifyingPropertyVerificationContext ctx) 
    {
        QualifyingProperty p = builtInVerifier.verify(propData, ctx);
        // Do something else here...
    }
}
```