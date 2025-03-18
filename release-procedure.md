# Release 

CI creates release in case if commit has specific tag(We use semantic versioning).

For release candidates (it is tagged as pre-release on GitHub):
- v0.1.0-rc1
- v0.1.0-rc3

For long supported releases:
- v0.1.0
- v0.1.1 (critical bugfix)

## Dockerhub

CI builds and pushes images for next projects:
- bdjuno
- big-dipper
- faucet

By default, it builds and pushes image with commit tag, 
but if commit has tag attached, it adds git tag as image tag as well.