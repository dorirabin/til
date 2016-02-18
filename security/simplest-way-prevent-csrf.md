# Simplest way to prevent cross site request forgery (CSRF) attack

Problem: Cross site request forgery attacks trick user to perform restricted operations without their knowledge.

Solution: Add a custom header (e.g. X-Requested-By) in request and check this header in filter. Header value can be arbitary.

It is unable for browser to add custom header via form, image, iframe, etc. The only way to add custom header is through XMLHttpRequest. However, browser restricts using XMLHttpRequest from a different domain for cross site scripting (XSS).

References:

[Guidelines for Implementation of REST](https://www.nsa.gov/ia/_files/support/guidelines_implementation_rest.pdf) (Section IV.F)

[Jersey's CsrfProtectionFilter](https://jersey.java.net/apidocs/2.7/jersey/org/glassfish/jersey/server/filter/CsrfProtectionFilter.html)
