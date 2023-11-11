# MOVED TO CODEBERG - [codeberg.org/chdorner/bookwyrm-dev](https://codeberg.org/chdorner/bookwyrm-dev)

# Local BookWyrm development environment

For a more detailed description and instructions see [this blog post](https://amble.blog/2023/01/29/a-local-developent-setup-for-bookwyrm-federation/).

For most work the [recommended BookWyrm development environment](https://docs.joinbookwyrm.com/install-dev.html)
is more than enough, except for locally testing the ActivityPub
federation.

This setup is using CloudFlare Tunnels to satisfy the requirement
of having two separate domains accessible via a non-self-signed SSL
certificate, but this part can easily be swapped out with another
service like ngrok and its alternatives.

## Structure

The structure assumes that two BookWyrm instances are running,
one `dev-lit` and one `dev-sci`.

Each local instance runs a few services:
* gunicorn web server on port `5040` for `dev-lit`, `5050` for `dev-sci`
* celery worker
* celery beat scheduler
* celery flower interface on port `5041` for `dev-lit`, `5051` for `dev-sci`
* `cloudflared` tunnel

The setup assumes that you are running PostgreSQL and Redis separately,
feel free to either run it natively or in containers.

## Setup

For each instance (`dev-lit` and `dev-sci`):

1. Copy `.env.local.example` and configure the variables, this file can be used to override any value from `.env`
2. Configure the CloudFlare tunnel (see [this post](https://amble.blog/2023/01/29/a-local-developent-setup-for-bookwyrm-federation/) for more detailed instructions), then copy and configure `cloudflared-tunnel.yaml.example`
3. Create the two databases (defaults to `bookwyrm_lit` and `bookwyrm_sci`)
4. Use each instance's `manage` script to run management commands, for setting up a completely new instance:
    1. `./dev-lit/manage migrate`, plus the same command for `dev-sci`
    2. `./dev-lit/manage initb`, plus the same command for `dev-sci`
    3. `./dev-lit/manage compile_themes`, plus the same command for `dev-sci`
    4. `./dev-lit/manage collectstatic`, plus the same command for `dev-sci`
    5. `./dev-lit/manage admin_code`, plus the same command for `dev-sci`

Additionally to test how federation looks in other ActivityPub services, there's a [takahe](https://github.com/jointakahe/takahe) setup available, to set this up you'll need to:

1. Copy `.env.local.example` and configure the variables, this file can be used to override any value from `.env`
2. Configure the CloudFlare tunnel (see [this post](https://amble.blog/2023/01/29/a-local-developent-setup-for-bookwyrm-federation/) for more detailed instructions), then copy and configure `cloudflared-tunnel.yaml.example`
3. Create a database, use the same name as in `TAKAHE_DATABASE_SERVER` configuration (i.e. `takahe`)
4. Use the `manage` script to run commands for setting up a new instance:
    1. `./takahe/manage migrate`
5. When having takahe up and running sign up your first user with the same email address as defined
   in `TAKAHE_AUTO_ADMIN_EMAIL`. The confirmation link will be available in the console which prints
   out the whole signup email that isn't being actually sent in this setup.

## Running

This setup comes with a `Procfile`, feel free to use your favourite process manager like [foreman](https://github.com/ddollar/foreman) or [hivemind](https://github.com/DarthSim/hivemind).

```bash
# for foreman
foreman start

# for hivemind
hivemind
```
