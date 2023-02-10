# Github actions

A collection of reusable github actions

## install-node-modules

Installing node modules with yarn (with cache if possible)

```yml
- uses: sfstudios/actions/install-node-modules@master
  with:
    NPM_TOKEN: ${{ secrets.NPM_TOKEN }}
    branch: ${{ github.ref }}
```
