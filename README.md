# dockerized_sqlfluff

This is a Dockerfile that allows the user to run a containerized version of
[sqlfluff](https://pypi.org/project/sqlfluff/) by
[Alan Cruickshank](https://github.com/alanmcruickshank).  The image is
rebuilt weekly thanks to [Travis CI](https://travis-ci.org/).

## To build

```sh
docker build -t sqlfluff .
```

## To pull hosted image from Docker Hub

```
docker pull wesleydeanflexion/sqlfluff
```

## To run

```sh
# mount and lint a file with locally-built image
docker run --rm -it -v $(pwd):/data sqlfluff filename.sql

# pipe a file to test with hosted image
docker run --rm -i wesleydeanflexion/sqlfluff - < filename.sql
```

### Recursive scan of all SQL files

This will start in the current directory and scan all `*.sql` files
(case-insensitive) with the hosted sqlfluff image.  The `sed` statement
will convert any container-specific paths back to paths meaningful
to the host system.

```
docker run \
  --entrypoint '' \
  --rm \
  --interactive \
  --volume $(pwd):/data \
  wesleydeanflexion/sqlfluff \
  find . \
    -iname "*.sql" \
    -exec sqlfluff lint {} \; \
  | sed -Ee "s|/data/\.|$(pwd)|g"
```

### Notes

1.   the file(s) to lint are sent as options to the sqlfluff container
2.   the container looks in `/data/` to find the file(s) provided
3.   sqlfluff accepts `-` as a filename to read from STDIN (for piping)

### Convenience alias

For your convenience, you may include the following into your shell
configuration file (e.g., `~/.bashrc`):

```sh
# this is the alias
alias sqlfluff='docker run --rm -i -v$(pwd):/data sqlfluff'

# here's how to use the alias
cat filename.sql | sqlfluff -
sqlfluff filename.sql
```
