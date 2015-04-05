Whenever a time-stamp property is used, _XAdES4j_ uses a `TimeStampTokenProvider` to actually obtain the time-stamp token. The input calculation is handled by the library and the provider is invoked with the resulting octet-stream.

```
public interface TimeStampTokenProvider
{
   public TimeStampTokenRes getTimeStampToken(byte[] tsDigestInput, String digestAlgUri);
}
```

As with other providers, a `TimeStampTokenProvider` is configured in the signing profile. Nevertheless, the library includes a default implementation which obtains tokens from a TSA using HTTP. You can change the TSA URL by subclassing the provider.

```
class MyTimeStampTokenProvider extends DefaultTimeStampTokenProvider
{
   @Inject
   public MyTimeStampTokenProvider(MessageDigestEngineProvider messageDigestProvider)
   {
      super(messageDigestProvider);
   }

   @Override
   protected String getTSAUrl()
   {
      return "http://tsa.starfieldtech.com/";
   }
}
```

The library also includes `AuthenticatedTimeStampTokenProvider`, which obtains tokens from a TSA using HTTP with basic authentication. To use this provider one configures the provider itself as well as the authentication data for the TSA:

```
XadesSigningProfile p = new XadesBesSigningProfile(...)
  .withTimeStampTokenProvider(AuthenticatedTimeStampTokenProvider.class)
  .withBinding(TSAHttpAuthenticationData.class, new TSAHttpAuthenticationData("url", "user", "password"));
```