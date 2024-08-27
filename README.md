# Chiron User Documentation

## how to setup documentation editing environment

## how to edit documentation

1. Make changes to user_docs_vault subdirectory using Obsidian.
2. Use the Webpage HTML Export Obsidian plugin to publish your site to the user_docs_build subdirectory.
3. Commit your changes to the main branch.
```shell
git add . -A
git commit -m "describe what you changed"
git push origin main
```

4. Publish your changes to the live website. It usually takes several minutes to update.
```shell
git subtree push --prefix user_docs_build origin gh-pages
```