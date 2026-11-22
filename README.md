# docker-compose-library

The Docker Compose files behind Eric's home lab. Each service has a folder; each file is one
Komodo stack on one host.

Home: Forgejo (`forgejo.eric-post.com`, reachable inside the house). A public copy on GitHub is
pushed automatically, but only after a secret scan of every commit comes back clean.

## Layout

```
<service>/
  <service>-<host>.yml     one stack on one host
  .env.example             every variable the file needs, with no values
```

Hosts: `nas-lab`, `bazzite-tv`, `rpi5-containers-01` to `-04`, `vps-racknerd`.

## Rules

1. **No secret ever goes in a file.** Write `${VARIABLE}` in the compose file. The value goes in
   the stack's Environment in Komodo, or in Infisical. List the name in `.env.example`.
2. **No `.env` file is committed.** `.gitignore` refuses them.
3. **Scan before pushing.** `gitleaks git .` and `gitleaks dir .` must both report no leaks.
4. **Never rewrite `main`.** The public copy follows it with ordinary pushes; a rewrite stops the
   copy until it is fixed by hand.
5. **A file moves here only once it is clean.** Files still in the old private repository
   (`eKioga/docker-compose-library` on GitHub) are treated as holding secrets until they move.

## How a change goes live

1. Commit and push to `main` on Forgejo.
2. Komodo deploys the stack from Forgejo (no internet needed, except for new images).
3. The `publish` workflow scans the whole history with gitleaks. Clean: it pushes `main` to
   GitHub. A finding: the run fails, nothing is pushed, and the secret must be removed and
   rotated before the next push.
