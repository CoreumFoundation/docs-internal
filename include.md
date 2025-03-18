# How to include a.md into b.md

For instance we have next dir structure:
```
./src/tutorials/a.md
./src/tutorials/b.md
```

To include a.md into b.md you should use next syntax at b.md:
```
!!!include(./src/tutorials/a.md)!!!
```

Note, that path should be relative to root of the repo.
