# 1.3.2 #

  * Fix wrong data object reference mapping when the signing certificate is signed ([bug report](http://stackoverflow.com/questions/26164600/xades4j-produces-an-invalid-signature-with-signsigningcertificate-set-to-true-an/26203773#26203773))
  * Support namespaces on `XPath` and `XPath2Filter` transforms
  * Update Apache Santuario and Bouncy Castle dependencies

# 1.3.1 #

  * Search all the X09Data elements for signing certificate related data
  * Timestamp token validation: use all the certificates on the token to validate the TSA certificate
  * Support additional data objects on counter signatures
  * Minor bug fixes and docs improvements