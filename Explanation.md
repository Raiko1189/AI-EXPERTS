Assignment Explanation
What was the bug?
In the Client.request() method, when api=True, the token refresh logic did not handle the case where oauth2_token was a dictionary instead of an OAuth2Token instance.

The refresh step was skipped.
The Authorization header was never added.
The test test_api_request_refreshes_when_token_is_dict failed because it expected Bearer fresh-token.
Why did it happen?
The original logic only refreshed the token in two cases:

Token was None
Token was an expired OAuth2Token
If the token existed but was not an OAuth2Token (e.g., a dictionary from cache or configuration):

Neither condition applied
Python treated the dictionary as truthy
Refresh was skipped, assuming the token was valid
How the fix solves it
The condition was updated to:

if not isinstance(self.oauth2_token, OAuth2Token) or self.oauth2_token.expired:
    self.refresh_oauth2()
This ensures the client refreshes the token when:

Missing
Expired
Not a valid OAuth2Token instance
As a result:

All tests pass
Authorization header is always correctly set
Edge case not covered
Tests do not cover cases where an OAuth2Token exists but has invalid or empty values (e.g., missing access token).