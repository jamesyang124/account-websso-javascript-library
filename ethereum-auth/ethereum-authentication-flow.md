# Ethereum Authentication Flow

{% hint style="warning" %}
This flow is **only** support for HTC OAuth client with **password grant type**, if need this integration, please contact us for expanding OAuth client access control or other solution.
{% endhint %}

HTC Account support Ethereum authentication with identity's **capitalization encoded checksum** Ethereum address. After identity **already connected** to an Ethereum wallet app, HTC account integrated app client could get HTC account access token by this flow. Scenario is as following:\


![sequence for HTC Account ethereum auth flow](../.gitbook/assets/Untitled-2022-01-27-1409.png)

{% hint style="warning" %}
Ethereum address performing the signing conformant to **capitalization encoded checksum** specified in EIP-55 where applicable.
{% endhint %}

### HTC Account Server Host Environment

| ENV   | Resource Domain                    |
| ----- | ---------------------------------- |
| STAGE | ​https://account-stage.htcvive.com |
| PRDO  | ​https://account.htcvive.com       |

### HTC Account Profile Server Host Environment

| ENV   | Resource Domain                           |
| ----- | ----------------------------------------- |
| STAGE | https://account-profile-stage.htcvive.com |
| PROD  | https://account-profile.htcvive.com       |

### API SPEC

{% swagger method="post" path="/RME/SS/api/ethereum-auth/v1/login" baseUrl="https://account.htcvive.com" summary="check signed message and authenticate HTC account user" %}
{% swagger-description %}

{% endswagger-description %}

{% swagger-parameter in="body" required="true" name="paddr" %}
**capitalization encoded checksum**

 ethereum address
{% endswagger-parameter %}

{% swagger-parameter in="body" name="extra" required="true" %}
extra data payload for HTC account
{% endswagger-parameter %}

{% swagger-parameter in="body" name="extra.clientId" required="true" %}
HTC OAuth client's client id
{% endswagger-parameter %}

{% swagger-parameter in="body" name="extra.scopes" required="true" type="String list" %}
HTC OAuth client's required scope list
{% endswagger-parameter %}

{% swagger-parameter in="body" name="sig" %}
ethereum personal_sign JSON RPC call respond signed signature
{% endswagger-parameter %}

{% swagger-parameter in="header" name="content-type" %}
application/json
{% endswagger-parameter %}

{% swagger-response status="200: OK" description="accessToken field for HTC Account access token, profileServiceUri used for getting account's ethereum address API" %}
```javascript
{
	"accessToken": "htc-account-access-token",
	"account": {
		"accountId": "6c3789f4-1d4a-4a5b-bc81-b9c14631eaff",
		"provider": "ethereum"
	},
	"expiresIn": 86400,
	"profileServiceUri": "https://account-profile.htcvive.com/RME/SS/",
	"scopes": [
		"email",
		"birthday",
		"issuetoken",
		"profile.write"
	],
	"serviceUri": "https://account.htcvive.com/RME/SS/"
}
```
{% endswagger-response %}

{% swagger-response status="400: Bad Request" description="Verify Signature failed, client error handling should be general " %}
```javascript
{
    // Response
}
```
{% endswagger-response %}

{% swagger-response status="400: Bad Request" description="Nonce Already Expired, client error handling should be general" %}
```javascript
{
    // Response
}
```
{% endswagger-response %}
{% endswagger %}

{% swagger method="post" path="/RME/SS/api/ethereum-auth/v1/nonce" baseUrl="https://account.htcvive.com" summary="generate 6-digit nonce and 10 seconds nonce for ethereum personal_sign message" %}
{% swagger-description %}

{% endswagger-description %}

{% swagger-parameter in="body" name="paddr" required="true" %}
**capitalization encoded checksum**

 ethereum address
{% endswagger-parameter %}

{% swagger-parameter in="header" name="content-type" %}
application/json
{% endswagger-parameter %}

{% swagger-response status="200: OK" description="directly respond 6 digit nonce, ex: 123453" %}
```javascript
{
    // Response
}
```
{% endswagger-response %}
{% endswagger %}

{% swagger method="get" path="/RME/SS/api/ethereum-auth/v1/metadata" baseUrl="https://account.htcvive.com" summary="get ethereum personal_sign message template" %}
{% swagger-description %}

{% endswagger-description %}

{% swagger-response status="200: OK" description="signin-text-template field for personal_sign message template" %}
```javascript
{
    "signin-text-template": "I am sign-in with this one-time 6-digit nonce:     %s"
}
```
{% endswagger-response %}
{% endswagger %}

### REFERENCE

{% embed url="https://github.com/ethereum/EIPs/blob/master/EIPS/eip-4361.md" %}

{% embed url="https://github.com/ethereum/EIPs/blob/master/EIPS/eip-55.md" %}
