# Somfy TaHoma Developer Mode

## Getting started

1. Open the **TaHoma By Somfy** application on your device 📱.
2. Navigate to the **Advanced Functions** menu in the application.
3. Activate **Developer Mode** by tapping 👇 7 times on the version number of your gateway (like 2025.1.4-11).

By completing these steps, you enable the local API on your TaHoma box 🚀. 

![Developer Mode](/img/Key visuals developer mode 1.png)

4. Generate a token from the **Developer Mode** menu to authenticate your API calls.

![Token](/img/Key visuals developer mode 2.png)
  
> [!NOTE]
> Multiple tokens can be generated, but keep in mind that they can only be 👀 read and copied at the time of their creation. Ensure you save them securely for future use.

# Security

## TLS certificate

> [!WARNING]
> Due to the local nature of the API, the TaHoma box uses a certificate signed by a self signed authority.

To avoid security issues, add the following authority to your HTTPS client trust store:
`https://ca.overkiz.com/overkiz-root-ca-2048.crt`

## API Authorization

All local api calls must be authorized. This authorization is made using the **Bearer authentication scheme** and the
token retrieved previously.

Simply add the **Authorization** header when making request.

```
Authorization: Bearer <token>
```
# Discovery

You can find your TaHoma box on the local network using the mDNS protocol.

TaHoma boxes with developer mode enabled broadcast a service with:

Service Type: \_kizboxdev.\_tcp

TXT:

| Key         | Value                                         |
| ----------- | --------------------------------------------- |
| gateway_pin | gateway pin (eg. 1234-5678-9012)              |
| api_version | Version of the available REST API (eg. 1)     |
| fw_version  | Firmware version of the TaHoma (eg. 2019.4.3) |


> [!WARNING]
> Host name and service name are not a reliable way to identify a TaHoma by its pin. The TXT record `gateway_pin` has to match.

# API Documentation

You are now ready to use this local API.

Get API details by reading the specification in [`docs/openapi.yaml`](docs/openapi.yaml)
or browse and try it online using [`swagger-ui`](https://somfy-developer.github.io/Somfy-TaHoma-Developer-Mode).

# Rate limits

There is no rate limiting on this local API. However, be aware that if you call the API too frequently,
the gateway might be overloaded which will result in an unwanted latency during products control.

We advise you to call the required API endpoint when your application starts and use events to get future updates.

For example:

- Call `/setup` at application start
- Register event listener with `/events/register`
- Fetch events on `/events/{listenerId}/fetch` once every second at most


> [!CAUTION]
> Our support teams are available if you have any problems or questions regarding the use or operation of the TaHoma system.
> 
> However, for questions related to the development of third-party software using this API, please refer to this gitHub project
