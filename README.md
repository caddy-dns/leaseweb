**DEVELOPER INSTRUCTIONS:**

- Update module name in go.mod
- Update dependencies to latest versions
- Update name and year in license
- Customize configuration and Caddyfile parsing
- Update godocs / comments (especially provider name and nuances)
- Update README and remove this section

---

Leaseweb module for Caddy
===========================

This package contains a DNS provider module for [Caddy](https://github.com/caddyserver/caddy). It can be used to manage DNS records with Leaseweb.

## Caddy module name

```
dns.providers.leaseweb
```

## Config examples

To use this module for the ACME DNS challenge, [configure the ACME issuer in your Caddy JSON](https://caddyserver.com/docs/json/apps/tls/automation/policies/issuer/acme/) like so:

```json
{
	"module": "acme",
	"challenges": {
		"dns": {
			"provider": {
				"name": "leaseweb",
				"api_key": "LEASEWEB API KEY"
			}
		}
	}
}
```

or with the Caddyfile:

```
# globally
{
	acme_dns leaseweb ...
}
```

```
# one site
tls {
	dns leaseweb ...
}
```

## Contributing / developing

Extensive documentation can be found [here](https://caddyserver.com/docs/extending-caddy).

Quickest way to get test this library is [xcaddy](https://github.com/caddyserver/xcaddy).

For example, using Docker that could look like this:

1. Make sure to clone both this repo and [libdns/leaseweb](https://github.com/libdns/leaseweb) into your current folder as `./caddy-leaseweb` and `./libdns-leaseweb` respectively.

2. Make a Dockerfile with the following contents:

```Dockerfile
FROM caddy:builder-alpine AS builder

RUN mkdir -p /tmp/caddy-leaseweb
RUN mkdir -p /tmp/libdns-leaseweb
COPY ./caddy-leaseweb /tmp/caddy-leaseweb/
COPY ./libdns-leaseweb /tmp/libdns-leaseweb/

RUN xcaddy build \
    --with github.com/caddy-dns/leaseweb=/tmp/caddy-leaseweb \
    --with github.com/libdns/leaseweb=/tmp/libdns-leaseweb

FROM caddy:alpine

COPY --from=builder /usr/bin/caddy /usr/bin/caddy
```

3. run `docker build --no-cache . -t caddy-debug`.

You can then use the `caddy-debug` docker container for testing.

If you want to change/develop code, just edit the code in the local `./caddy-leaseweb` and/or `./libdns-leaseweb` folders.

To iterative development you can also `docker run --rm -it -v ./caddy-leaseweb:/tmp/caddy-leaseweb -v ./libdns-leaseweb:/tmp/libdns-leaseweb caddy:builder-alpine` and then use

```shell
xcaddy build \
    --with github.com/caddy-dns/leaseweb=/tmp/caddy-leaseweb \
    --with github.com/libdns/leaseweb=/tmp/libdns-leaseweb
```