# Data Object Properties #

The classes that represent the signed data objects also enable the specification of the qualifying properties that apply to each data object, such as `DataObjectFormat` and `IndividualDataObjectTimeStamp`.

```
DataObjectDesc obj = new DataObjectReference("rfc3161.txt")
   .withDataObjectFormat(new DataObjectFormatProperty("text/plain"))
   .withDataObjectTimeStamp();
```

Some qualifying properties apply to all the data objects that are present on a signature. Those properties can be defined using the `SignedDataObjects` class.

```
SignedDataObjects dataObjs = new SignedDataObjects(obj)    
   .withCommitmentType(AllDataObjsCommitmentTypeProperty.proofOfOrigin())
   .withDataObjectsTimeStamp();
```

Another option to define data object properties is to use a custom `DataObjectPropertiesProvider` which is invoked when no properties are explicitly specified for a given object. This can be useful when all the data objects share the same properties.

# Optional Signature Properties #

Most XAdES qualifying properties are part of the different signature forms. As such, _XAdES4j_ always includes the properties for the form in use when creating a signature. For instance, if a `XadesTSigningProfile` is used, the resulting signer will always include the `SignatureTimeStamp` property.

Nevertheless, XAdES includes some optional signature properties, such as `SignatureProductionPlace` and `CounterSignature`, that can be used with all the forms. These properties can be included using a custom `SignaturePropertiesProvider` which is configured in the signing profile, as all the other providers.

```
XadesSigningProfile profile = new XadesBesSigningProfile(...).withSignaturePropertiesProvider(new SignaturePropertiesProvider()
{
   @Override
   public void provideProperties(SignaturePropertiesCollector signedPropsCol)
   {
      signedPropsCol.setSignerRole(new SignerRoleProperty("CTO"));
      signedPropsCol.setSignatureProductionPlace(new SignatureProductionPlaceProperty("City", "Country"));
      signaturePropsCol.setSigningTime(new SigningTimeProperty());
   }
});
```

The default `SignaturePropertiesProvider` only includes the `SigningTime` property