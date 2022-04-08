
# Somfy TaHoma Developer Mode

## Getting started

First connect to the Somfy website and navigate to the 👨 **My Account** menu.  
Find the different available options for your TaHoma box and activate **Developer Mode**.

Activating this mode will enable a local API on your TaHoma box. Be aware that Somfy will not be able to provide support for usage of this API.


## API authentication

In order to use this now available local API, you need to negociate a token with our cloud API to authenticate your calls to the local API.

```{{url}}``` to be used is **ha101-1.overkiz.com**

```{{pod}}``` The pin of your gateway (eg. 1234-5678-9012)

### Login
When succeeding, the response to this call will include a JSESSIONID cookie that needs to be used whith the other calls to our backend to be authenticated.

#### Method: POST
>```
>https://{{url}}/enduser-mobile-web/enduserAPI/login
>```
#### Headers
|Key|Value|
|---|---|
|Content-Type|application/x-www-form-urlencoded|
#### Body (**x-www-form-urlencoded**)

|Key|Value|
|---|---|
|userId|YOUR_EMAIL_ADDRESS|
|userPassword|YOUR_PASSWORD|

---

### Generate a token
#### Method: GET
>```
>https://{{url}}/enduser-mobile-web/enduserAPI/config/{{pod}}/local/tokens/generate
>```
#### Headers

|Key|Value|
|---|---|
|Content-Type|application/json|
|Cookie|JSESSIONID=THE_VALUE_YOU_GOT_FROM_THE_LOGIN|


---

### Activate your token
Use your personal label to identify your tokens.
#### Method: POST
>```
>https://{{url}}/enduser-mobile-web/enduserAPI/config/{{pod}}/local/tokens
>```
#### Headers

|Key|Value|
|---|---|
|Content-Type|application/json|
|Cookie|JSESSIONID=THE_VALUE_YOU_GOT_FROM_THE_LOGIN|

#### Body (**raw**)

```json
{
  "label" : "Toto token",
  "token" : "{{token}}",
  "scope" : "devmode"
}
```

# Security

## TLS certificate

⚠ Due to the local nature of the API, The TaHoma box use a certificate signed by a self signed authority.

To avoid security issue, add the following authority to your HTTPS client trust store:
https://ca.overkiz.com/overkiz-root-ca-2048.crt

## API Authorization

All local api calls must be authorized. This authorization is made using the **Bearer authentication scheme** and the
token retrived previously.

Simple add the **Authorization** header when making request.

    Authorization: Bearer <token>


# Discovery

You can find your TaHoma box on the local network using the mDNS protocol.

TaHoma boxes with developper mode enable broadcast a service with:

Service Type: _kizboxdev._tcp

TXT:
|Key|Value|
|---|---|
|gateway_pin|gateway pin (eg. 1234-5678-9012)|
|api_version|Version of the available REST API (eg. 1)|
|fw_version|Firmware version of the TaHoma (eg. 2019.4.3) |

⚠ Hostame and service name are not a reliable way to identify a TaHoma by its pin. the TXT record gateway_pin has to be match. 

# API Documentation

You are now ready to use this local API.

Get api details by reading the specification in doc/openapi.yaml
or browse and try it online using swagger-ui: https://somfy-developer.github.io/Somfy-TaHoma-Developer-Mode/

# Rate limits

There is no rate limiting on this local API. However, be aware that, if you calls the API too frequently,
the gateway might be overloaded which will result in an unwanted latency during products control.

We advise you to call the required API endpoint when your application start and use events to get future change.

For exemple:
 - Call `/setup` at application start
 - Register event listener with `/events/register`
 - Fetch events on `/events/{listenerId}/fetch` once every second at the most
