[![Latest Release][version-image]][version-url]
[![caddy on DockerHub][dockerhub-image]][dockerhub-url]
[![Docker Build][gh-actions-image]][gh-actions-url]

# caddy-cloudflare-transform

The image includes 3 modules for Caddy: cloudflare, transform-encoder and caddy-exec.

The image is designed to obtain a TLS certificate using dns challenge api cloudflare, and using a log converter based on a desired pattern.

Please see the official [Caddy Docker Image](https://hub.docker.com/_/caddy) for deployment instructions.

Builds are available at the following Docker repositories:

* Docker Hub: [docker.io/metgen/caddy-cf-tf](https://hub.docker.com/repository/docker/metgen/caddy-cf-tf)
* GitHub Container Registry: [ghcr.io/metgen/caddy-cf-tf](https://ghcr.io/metgen/caddy-cf-tf)

For start you should add CLOUDFLARE_EMAIL and CLOUDFLARE_API_TOKEN as environment variables to your `docker run` command. Example:

      docker run -it --name caddy \
        -p 80:80 \
        -p 443:443 \
        -v caddy_data:/data \
        -v caddy_config:/config \
        -v $PWD/Caddyfile:/etc/caddy/Caddyfile \
        -e CLOUDFLARE_EMAIL=youremail@example.com \
        -e CLOUDFLARE_API_TOKEN=12345 \
        -e ACME_AGREE=true \
        metgen/caddy-cf-tf:latest
           
 You can obtain your [Cloudflare API token](https://support.cloudflare.com/hc/en-us/articles/200167836-Managing-API-Tokens-and-Keys) via the Cloudflare Portal. To create a API token with minimal scope, the following steps are needed:
   1. Log into your dashboard, go to account settings, create API token
   2. grant the following permissions:

      * Zone / Zone / Read
      * Zone / DNS / Edit
      
For use you should add the following to your Caddyfile as the [tls directive](https://caddyserver.com/docs/caddyfile/directives/tls#tls). 

   ```
   tls {$CLOUDFLARE_EMAIL} { 
     dns cloudflare {$CLOUDFLARE_API_TOKEN}
   }
   ```
To use the log converter you need to add to your Caddyfile [conversion rule](https://github.com/caddyserver/transform-encoder).   
   ```
   log {
      output file /logs/access.log 
      format transform `{ts} {request>headers>X-Forwarded-For>[0]:request>remote_ip} {request>host} {request>method} {request>uri} {status}` {
            time_format "02/Jan/2006:15:04:05"
      }
   }
   ```

This image supports tagging [See available tags here](https://hub.docker.com/r/metgen/caddy-cf-tf/tags). To select a specific version of `caddy`, set your [Docker image tag](https://docs.docker.com/engine/reference/run/#imagetag) to the caddy version you'd like to use. 

   Example: `metgen/caddy-cf-tf:2.9.1`

[version-image]: https://img.shields.io/github/v/release/metgen/caddy-cf-tf?style=for-the-badge
[version-url]: https://github.com/metgen/caddy-cf-tf/releases

[gh-actions-image]: https://img.shields.io/github/actions/workflow/status/metgen/caddy-cf-tf/main.yml?style=for-the-badge
[gh-actions-url]: [https://github.com/metgen/caddy-cf-tf/actions](https://github.com/metgen/caddy-cf-tf/actions)

[dockerhub-image]: https://img.shields.io/docker/pulls/metgen/caddy-cf-tf?label=DockerHub%20Pulls&style=for-the-badge
[dockerhub-url]: https://hub.docker.com/r/metgen/caddy-cf-tf
