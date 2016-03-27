# Confused deputy attack on implicit grant flow

Parties:

* Alice - Resource owner
* GoodApp - Client
* BadApp - Client (Attacker)
* SocialApp - Resource / Authorization Server

Attack flow:

1. Alice registered an account A on GoodApp with SocialApp account S
2. Alice registered an account B on BadApp with SocialApp account S
3. BadApp gets the access token from SocialApp
4. BadApp constructs a redirect URI of GoodApp with the access token
5. BadApp can now access account A on GoodApp because GoodApp thinks BadApp is Alice

Reason:

GoodApp has no idea that the access token is generated for GoodApp or not.

Solution:

GoodApp should check with SocialApp that which client is assoicated with the access token.

References:

[How and why is Google OAuth token validation performed?](http://stackoverflow.com/questions/17241771/how-and-why-is-google-oauth-token-validation-performed/17439317#17439317)
