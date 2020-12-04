# Login as Root

If you'd like to log in to a Docker container as `root`, you can use a `-u` or `--user` option.
This can take either `username` or `uid`.

As the `uid` of `root` is `0`, the quickest way is to add `-u 0`.
You can find `uid`s in `/etc/passwd`.

ref: https://stackoverflow.com/questions/28721699/root-password-inside-a-docker-container

## `docker run`
If you'd like to create a temporary container and log in to it as `root`, run:

```bash
$ docker run -it --rm <IMAGE> bash
```

## `docker exec`
To log in to an exisiting Docker container as `root`, run:

```bash
$ docker exec -it -u 0 <IMAGE> bash
```
