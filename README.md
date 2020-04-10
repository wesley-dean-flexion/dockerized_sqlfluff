# dockerized_sqlfluff

This is a Dockerfile that allows the user to run a containerized version of
[sqlfluff](https://pypi.org/project/sqlfluff/) by 
[Alan Cruickshank](https://github.com/alanmcruickshank).

## To build

```sh
docker build -t sqlfluff .
```

## To run

```sh
# mount and lint a file
docker run --rm -it -v $(pwd):/data sqlfluff filename.sql

# pipe a file to test
docker run --rm -i sqlfluff - < filename.sql
```

### Recursive scan of all SQL files

```
find . \
  -iname "*.sql" \
  -exec docker run \
    --rm \
    --interactive \
    --volume $(pwd):/data \
    wesleydeanflexion/sqlfluff \
    /data/{} \; \
  | sed -Ee "s|/data/\.|$(pwd)|g"
```

### Convenience alias

```sh
alias sqlfluff='docker run --rm -i -v$(pwd):/data sqlfluff'
cat filename.sql | sqlfluff -
sqlfluff filename.sql
```

### Notes

1.   the file(s) to lint are sent as options to the sqlfluff container
2.   the container looks in `/data/` to find the file(s) provided
3.   sqlfluff accepts `-` as a filename to read from STDIN (for piping)
