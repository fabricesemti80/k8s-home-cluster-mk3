# Forward Auth

As this was the most challanging (for me at least) part of the cluster setup, I try to be more detailed here. Also please see the "traefik2-inspiration.md" file, which was the document found on the Internet that helped solve me this (and perhaps will be useful to others too)

## Prepare

As we will use Google for authentication, we will have a shopping list:

- a domain name we own

- Cloudflare as our DNS (others might work; did not tried)

- We will also need a Google-project (see step 8 of the inspiration doc) that we will use for authentication.

- Once we have these, we need to set up a secret

 1. The "whitelist" should contain the email(s) that are allowed to access our site(s)
 1. The "secret" is just a random string that the cookie will use
 1. The "clientID" comes from Google
 1. The "clientSecret" also comes from Google

## Helmrelease

Once we have the secret defined, we can prepare the helmrelease.

The secret will be used via env:

```yaml

   middleware:
      enabled: true

      ...

    env:
      - name: PROVIDERS_GOOGLE_CLIENT_ID
        valueFrom:
          secretKeyRef:
            name: traefik-forward-auth-secret
            key: clientId
      - name: PROVIDERS_GOOGLE_CLIENT_SECRET
        valueFrom:
          secretKeyRef:
            name: traefik-forward-auth-secret
            key: clientSecret
      - name: SECRET
        valueFrom:
          secretKeyRef:
            name: traefik-forward-auth-secret
            key: secret
      - name: WHITELIST
        valueFrom:
          secretKeyRef:
            name: traefik-forward-auth-secret
            key: whitelist
```

- We will also going to use a few values from other secrets in the cluster, such as SECRET_DOMAIN (our domain) and I will use the SECRET_CLOUDFLARE_EMAIL as well for the whitelist, since it is my email.

- We also going to enable the middleware, so we can use it in ingress / ingress routes.

## Traefik

We will also modify the main Traefik deployment.

- we will add the CloudFlare-credentials as env to it

```yaml
    env:
      - name: CF_API_EMAIL
        valueFrom:
          secretKeyRef:
            name: cloudflare-creds
            key: email
      - name: CF_API_KEY
        valueFrom:
          secretKeyRef:
            name: cloudflare-creds
            key: api-key
```

- and we will let it do the certificate resolution

```yaml
     - "--certificatesresolvers.default.acme.email=${SECRET_CLOUDFLARE_EMAIL}"
      - "--certificatesresolvers.default.acme.storage=/data/acme.json"
      - "--certificatesresolvers.default.acme.caserver=https://acme-v02.api.letsencrypt.org/directory"
      - "--certificatesResolvers.default.acme.dnschallenge=true"
      - "--certificatesResolvers.default.acme.dnschallenge.provider=cloudflare"
      - "--accesslog=true"
```

## Usage

We can then use ingresses and ingressroutes to autehnticate, examples:

- ingressroute

![ingressroute](images/2021-11-15%2017_04_54-WSL_ingress_route.png)

- ingress

![ingress](images/2021-11-15%2017_06_18-WSL_%20ingress.png)

*Note: the "networking-" part in the "networking-traefik-forward-auth@kubecnetescrd" corresponds to the name space where the middleware is deployed.*
