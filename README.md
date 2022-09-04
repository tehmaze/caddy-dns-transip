# Caddy-DNS-TransIP

This is an attempt to create a Caddy DNS module that interacts with the TransIP API to enable getting
TLS certificates for your domain.

To build Caddy including this module, execute `xcaddy build --with github.com/bashopman/caddy-dns-transip=./`
where `./` is the root of the checked out repository.

Example Caddyfile:
```
<domainname> {
  tls {
    dns transip <username> <path to TransIP API private key file>
  }
}
```

*NOTE:* the implementation does not work:
```
ERROR	tls.issuance.acme.acme_client	cleaning up solver	{"identifier": "sub.example.com", "challenge_type": "dns-01", "error": "no memory of presenting a DNS record for sub.example.com (probably OK if presenting failed)"}
ERROR	tls.obtain	could not get certificate from issuer	{"identifier": "sub.example.com", "issuer": "acme-v02.api.letsencrypt.org-directory", "error": "[sub.example.com] solving challenges: presenting for challenge: adding temporary record for zone sub.example.com.: This is not a valid domain name: 'sub.example.com.' (order=https://acme-v02.api.letsencrypt.org/acme/order/******/*******) (ca=https://acme-v02.api.letsencrypt.org/directory)"}
```
I cannot find the cause of this error message.

What does work:
* the arguments from the Caddyfile are correctly read
* the code is able to get domain records from the TransIP API using 
    ```
    records, err := p.Provider.GetRecords(ctx, "sub.example.com")
    fmt.Print(records)
    ```

---

**DEVELOPER INSTRUCTIONS:**

- Update module name in go.mod
- Update dependencies to latest versions
- Update name and year in license
- Customize configuration and Caddyfile parsing
- Update godocs / comments (especially provider name and nuances)
- Update README and remove this section

---

\<PROVIDER\> module for Caddy
===========================

This package contains a DNS provider module for [Caddy](https://github.com/caddyserver/caddy). It can be used to manage DNS records with \<PROVIDER\>.

## Caddy module name

```
dns.providers.provider_name
```

## Config examples

To use this module for the ACME DNS challenge, [configure the ACME issuer in your Caddy JSON](https://caddyserver.com/docs/json/apps/tls/automation/policies/issuer/acme/) like so:

```json
{
	"module": "acme",
	"challenges": {
		"dns": {
			"provider": {
				"name": "provider_name",
				"api_token": "YOUR_PROVIDER_API_TOKEN"
			}
		}
	}
}
```

or with the Caddyfile:

```
# globally
{
	acme_dns provider_name ...
}
```

```
# one site
tls {
	dns provider_name ...
}
```
