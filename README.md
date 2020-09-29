# docs-scrape

This is a GitHub [workflow](.github/workflows/scrape.yml) repository for running the [Algolia scraper](https://docsearch.algolia.com/docs/run-your-own/) with Docker image `algolia/docsearch-scraper` for VuePress based websites.

## Instructions

### Config

Copy `config.json.dist` to `config.json` and edit it to fit your needs.

```sh
cp config.json.dist config.json
```

The config file [config.json](config.json) follows [VuePress config](https://github.com/algolia/docsearch-configs/blob/master/configs/vuepress.json).

### Secrets

Go to **API Keys** under your Algolia project. Then create the following secrets in your `docs-scrape` cloned repo:

- `APPLICATION_ID` with the value from **Application ID**
- `API_KEY` with the value from **Admin API Key**

### Push scraper

Add the following workflow in the **origin repo** where the VuePress website gets built. This will push the scrape workflow in your cloned `docs-scrape` repo.

```yaml
name: Push scrape
on: push
jobs:
  push-scrape:
    runs-on: ubuntu-latest
    steps:
      - name: Scrape it
        run: |
          curl -XPOST -u "${{ secrets.REPO_DOCS_SCRAPE_USERNAME }}:${{ secrets.REPO_DOCS_SCRAPE_TOKEN }}" -H "Accept: application/vnd.github.everest-preview+json" -H "Content-Type: application/json" https://api.github.com/repos/${{ secrets.REPO_DOCS_SCRAPE }}/dispatches --data '{"event_type": "push"}'
```

You will need the following secrets in the **origin repo**:

- `REPO_DOCS_SCRAPE_USERNAME` Username with access to your cloned `docs-scrape` repo
- `REPO_DOCS_SCRAPE_TOKEN` Personal Access Token (PAT) for the user above
- `REPO_DOCS_SCRAPE` `username/reponame` for your cloned `docs-scrape` repo

**Note:** If you already have a workflow, simply add the job right after publishing with VuePress.

## Thanks

Many thanks to [bahmutov](https://github.com/bahmutov) for the original repository example and blog post [Scrape Static Site with Algolia](https://glebbahmutov.com/blog/scrape-static-site-with-algolia/).
