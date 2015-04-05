# Exception Model #

XAdES4j has an hierarchic exception model on which all the exceptions are ultimately based on XAdES4jException. This enables the developer to go from rough to fine grained exception filtering by pointing at different levels in the hierarchy. In addition, exceptions are _models_ of the errors (i.e. contain information such as the certificate that was invalid, the property that failed verification, etc.), enabling the custom application error messages.

  * XAdES4jException
    * UnsupportedAlgorithmException
    * KeyingDataException
      * SigningKeyException
      * SigningCertChainException
      * SigningCertRequirementException
        * SigningCertKeyUsageException
        * SigningCertValidityException
    * PropertyDataGenerationException
      * PropertyDataGeneratorErrorException
      * PropertyDataGeneratorNotAvailableException
    * PropertyDataStructureException
      * PropertyDataStructureVerifierNotAvailableException
    * MarshalException
      * UnsupportedDataObjectException
    * UnmarshalException
      * PropertyUnmarshalException
    * InvalidSignatureException
      * CoreVerificationException
        * ReferenceValueException
        * SignatureValueException
      * QualifyingPropertiesIncorporationException
      * QualifyingPropertyVerifierNotAvailableException
      * InvalidPropertyException
        * PropertyVerifierErrorException
        * `(Multiple property-specific exceptions)`
      * InvalidXAdESFormException
    * CertificateValidationException
      * CannotSelectCertificateException
      * CannotBuildCertificationPathException
      * InvalidKeyInfoDataException
    * TimeStampTokenGenerationException
    * TimeStampTokenVerificationException
      * TimeStampTokenStructureException
      * TimeStampTokenSignatureException
      * TimeStampTokenDigestException
    * XAdES4jXMLSigException