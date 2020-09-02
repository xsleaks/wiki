# XS-Leaks Wiki

## Build Process

### Build locally

1. Install the [Hugo Framework](https://gohugo.io/getting-started/installing/) **extended** version > 0.68
2. Clone this repo
3. Run `hugo server --minify` in root directory 
4. Open your browser and go to http://localhost:1313 (or as indicated by hugo output)

### Generate static files

1. Run `hugo --buildDrafts`

## Automatic Deployment

This repository uses [Github Actions](https://github.com/features/actions) to automatically build and publish a static version of the XS-Leaks Wiki once a Pull Request is accepted. To bring Github Pages automation into Github Actions we use [actions-gh-pages](https://github.com/peaceiris/actions-gh-pages). To automatically build a website with the Hugo Framework, we use [actions-hugo](https://github.com/peaceiris/actions-hugo)

The strategy used to give the workflow access to this repository uses a [deploy_key](https://github.com/peaceiris/actions-gh-pages#%EF%B8%8F-set-ssh-private-key-deploy_key) which is privately set in this repository.