From [caddyserver.com](//caddyserver.com):
> Caddy 2 is a powerful, enterprise-ready, open source web server with automatic HTTPS written in Go

The configurations here assume you have installed Caddy [via package manager](https://caddyserver.com/docs/install#debian-ubuntu-raspbian), and are using [systemd to run Caddy](https://caddyserver.com/docs/running).

----

Here is a dead-simple way to have Caddy act as a reverse-proxy in front of NodeBB:

```
caddy reverse_proxy --from example.org --to localhost:4567
```

**That was fast!** &ndash; but we'll probably want something more permanent.

----

To use a `Caddyfile` instead (which you probably do), here is that same setup:

```
example.org
reverse_proxy localhost:4567
```

Save those two lines into `/etc/caddy/Caddyfile` and reload Caddy (`systemctl reload caddy`).

----

If you want a more robust setup with compression and direct file-serving for built assets (as mentioned in [Scaling NodeBB](../scaling)):

```
example.org {
    encode zstd gzip

    handle_path /assets/* {
        root * /path/to/nodebb
        try_files /build/webpack/{path} /build/public/{path} /public/{path}
        file_server
    }

    handle {
        reverse_proxy localhost:4567
    }
}
```

**Neat!**