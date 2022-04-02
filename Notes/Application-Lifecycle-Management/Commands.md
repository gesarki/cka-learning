*SIDEBAR*: Dockerfile commands


```dockerfile
# dockerfile
FROM Ubuntu
ENTRYPOINT ["sleep"]
CMD ["5"]
```

### ENTRYPOINT
- lets you specify which command to run on startup
- is suffixable with `CMD` or the arg in `docker run ubuntu-sleeper <arg>`

### CMD
- lets you specify which command to run on startup
- or what to append to `ENTRYPOINT`

## Examples
`docker run ubuntu-sleeper`
- sleeps for 5 seconds

`docker run ubuntu-sleeper 10`
- sleeps for 10 seconds (override CMD)

`docker run --entrypoint sleep2.0 ubuntu-sleeper 10`
- `sleep2.0`s for 10 seconds (overrides BOTH)

# Translating to Pod definition (tricky)
```yaml
# pod.yaml
apiVersion: v1
kind: Pod
metadata:
  name: ubuntu-sleeper-pod
spec:
  containers:
  - name: ubuntu-sleeper
    image: ubuntu-sleeper   # The image that's created from the above dockerfile
    command: ["sleep"]      # This overrides "ENTRYPOINT"
    args: ["10"]            # This overrides "CMD"
