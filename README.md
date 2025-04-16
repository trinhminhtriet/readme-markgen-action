# readme-markgen-action

A GitHub Action that automatically generates & updates markdown content (like your README.md)

## Instructions

1. Create a [markgen](https://github.com/trinhminhtriet/markgen) template and
place it anywhere in a repository that you automatically want to update. In this
guide we will use `templates/README.md.tpl`.

You can find an example template that I use to automatically update my GitHub
profile here: https://github.com/trinhminhtriet/markgen/blob/master/templates/README.md.tpl

2. In order to access some of GitHub's API, you need to provide a valid GitHub
token as a secret called `PERSONAL_GITHUB_TOKEN`. You can create a new token by
going to your profile settings:

`Developer settings` > `Personal access tokens` > `Generate new token`

Depending on your template you will need access to different API scopes. If you
want to support the full set of features, tick the checkboxes next to these
scopes: `read:user`, `repo:status`, `public_repo`, `read:org`. Check out the
[markgen](https://github.com/trinhminhtriet/markgen) documentation for a detailed
list of required scopes for each individual template function.

Now create a new secret in your repository's `Settings` and enter that token.

3. Create a new GitHub workflow in your repository: `.github/workflows/readme-markgen-action.yml`

```yml
name: Update README

on:
  push:
  schedule:
    - cron: "0 */1 * * *"

jobs:
  markgen:
    runs-on: ubuntu-latest

    steps:
      - uses: actions/checkout@master

      - uses: trinhminhtriet/readme-markgen-action@master
        env:
          GITHUB_TOKEN: ${{ secrets.PERSONAL_GITHUB_TOKEN }}
        with:
          template: "templates/README.md.tpl"
          writeTo: "README.md"

      - uses: stefanzweifel/git-auto-commit-action@v4
        env:
          GITHUB_TOKEN: ${{ secrets.PERSONAL_GITHUB_TOKEN }}
        with:
          commit_message: Update generated README
          branch: main
          commit_user_name: readme-markgen-action 🤖
          commit_user_email: actions@github.com
          commit_author: readme-markgen-action 🤖 <actions@github.com>
```

Careful: if you use `master` instead of `main` as the default branch, you will
need to update the above config for `git-auto-commit-action` accordingly.

This action will be triggered once per hour, parses `templates/README.md.tpl`
and generates a new `README.md` for you, and eventually pushes the changes to
the `master` branch. Make sure to adjust the input values `template` and
`writeTo` to suit your needs.
