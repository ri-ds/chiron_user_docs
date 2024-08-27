# Chiron User Documentation

## how to edit this documentation

You need Obsidian and any tool that can run git commands.

1. clone this repo on your computer
```shell
git clone https://github.com/jmeinken/chiron_user_docs.git
```

2. Set up an Obsidian vault on subdirectory `user_docs_vault`.
3. Use Obsidian to edit markdown files.
4. Use the community plugin "Webpage HTML Export" to publish the site.
    - Select every directory except `meta` and all its subdirectories.
    - Build output should go to `docs` subdirectory.
5. You can check the build in a web browser from the file `docs/index.html`.
6. once you're satisfied with it, commit to the main branch.
```shell
git add . -A
git commit -m "describe what you changed"
git push origin main
```

7. The live website is tied to the `docs` directory in the main branch and will automatically update.
