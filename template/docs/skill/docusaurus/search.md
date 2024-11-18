---
id: docusaurus-search
slug: /docusaurus-search
title: Search
authors: kuizuo
---

> [Search | Docusaurus](https://docusaurus.io/docs/search)

## [algolia](https://www.algolia.com/)

There are two options for configuring Algolia.

1. Let Docsearch (more precisely, [Algolia Crawler](https://crawler.algolia.com/) ) crawl your website once a week (you can also crawl it yourself), provided that the project is open source, otherwise it is charged . The advantage is that no additional configuration is required, and the application is more cumbersome (this blog currently uses this method)

2. You can run the DocSearch crawler yourself and crawl at any time, but you need to register an account and build the crawler environment yourself, or use Github Actions to help us crawl.

### Solution

The application for Algolia DocSearch is described in detail in the document. The application is troublesome, you need to wait for the email, and you need to reply to the other party for confirmation. So the conditions for free hosted DocSearch are relatively demanding, but once you apply, it is almost a one-time solution, and I highly recommend it. If the application is successful, you can view it in [Crawler Admin Console](https://crawler.algolia.com/admin/crawlers)

![image-20220627232545640](https://img.kuizuo.cn/image-20220627232545640.png)

Then fill in the appId, apiKey, and indexName of algolia `docusaurus.config.ts` .

```javascript title='docusaurus.config.ts'
algolia: {
  appId: 'GV6YN1ODMO',
  apiKey: '50303937b0e4630bec4a20a14e3b7872',
  indexName: 'kuizuo',
}
```

After the crawl is completed, it will be sent to your email regularly

![image-20230219144035031](https://img.kuizuo.cn/image-20230219144035031.png)

### Solution

[Run your own | DocSearch (algolia.com)](https://docsearch.algolia.com/docs/run-your-own)

Because Option 1 is really difficult to apply for and has a high probability of failure, I have no choice but to adopt Option 2.

First, apply for an [Algolia](https://www.algolia.com/) account, then create an index in the indices on the left, and get the Application ID and API Key in API Keys (note that there are two API KEYs)

![image-20210821230135749](https://img.kuizuo.cn/image-20210821230135749.png)

![image-20210821230232837](https://img.kuizuo.cn/image-20210821230232837.png)

The API KEY entered `docusaurus.config.ts` in is the **Search-Only API Key**

```js
themeConfig: {
    algolia: {
      apiKey: "xxxxxxxxxxx",
      appId: "xxxxxxxxxxx",
      indexName: "kuizuo",
    },
}
```

I chose Linux as the system and ran the crawler code in a Docker environment. [Install jq](https://github.com/stedolan/jq/wiki/Installation#zero-install) `choose()` install for installation (the installation may be a little slow). You can check the documentation for details, and then check the installation results in the console.

```
[root@kzserver kuizuo.cn]# jq --version
jq-1.6
```

Then create a file in any directory `.env` and fill in the corresponding APPID and API KEY (here is`Admin API Key`, I always thought it was the Search API Key at the time and it tricked me for a long time 😭)

```js
APPLICATION_ID = YOUR_APP_ID
API_KEY = YOUR_API_KEY
```

Then create `docsearch.json` a file in the project directory, the content of which can be referred to as follows (replace the highlighted part with your website)

```json title='docsearch.json' {2-4}
{
  "index_name": "xxxx",
  "start_urls": ["https://example.com"],
  "sitemap_urls": ["https://example.com"],
  "selectors": {
    "lvl0": {
      "selector": "(//ul[contains(@class,'menu__list')]//a[contains(@class, 'menu__link menu__link--sublist menu__link--active')]/text() | //nav[contains(@class, 'navbar')]//a[contains(@class, 'navbar__link--active')]/text())[last()]",
      "type": "xpath",
      "global": true,
      "default_value": "Documentation"
    },
    "lvl1": "header h1, article h1",
    "lvl2": "article h2",
    "lvl3": "article h3",
    "lvl4": "article h4",
    "lvl5": "article h5, article td:first-child",
    "lvl6": "article h6",
    "text": "article p, article li, article td:last-child"
  },
  "custom_settings": {
    "attributesForFaceting": ["type", "lang", "language", "version", "docusaurus_tag"],
    "attributesToRetrieve": ["hierarchy", "content", "anchor", "url", "url_without_anchor", "type"],
    "attributesToHighlight": ["hierarchy", "content"],
    "attributesToSnippet": ["content:10"],
    "camelCaseAttributes": ["hierarchy", "content"],
    "searchableAttributes": [
      "unordered(hierarchy.lvl0)",
      "unordered(hierarchy.lvl1)",
      "unordered(hierarchy.lvl2)",
      "unordered(hierarchy.lvl3)",
      "unordered(hierarchy.lvl4)",
      "unordered(hierarchy.lvl5)",
      "unordered(hierarchy.lvl6)",
      "content"
    ],
    "distinct": true,
    "attributeForDistinct": "url",
    "customRanking": ["desc(weight.pageRank)", "desc(weight.level)", "asc(weight.position)"],
    "ranking": ["words", "filters", "typo", "attribute", "proximity", "exact", "custom"],
    "highlightPreTag": "<span class='algolia-docsearch-suggestion--highlight'>",
    "highlightPostTag": "</span>",
    "minWordSizefor1Typo": 3,
    "minWordSizefor2Typos": 7,
    "allowTyposOnNumericTokens": false,
    "minProximity": 1,
    "ignorePlurals": true,
    "advancedSyntax": true,
    "attributeCriteriaComputedByMinProximity": true,
    "removeWordsIfNoResults": "allOptional",
    "separatorsToIndex": "_",
    "synonyms": [
      ["js", "javascript"],
      ["ts", "typescript"]
    ]
  }
}
```

Run the docker command

```bash
docker run -it --env-file=.env -e "CONFIG=$(cat docsearch.json | jq -r tostring)" algolia/docsearch-scraper
```

Then wait for the container to run and crawl your website. Finally, open the algolia console and the following page will appear, indicating success

![image-20210821225934002](https://img.kuizuo.cn/image-20210821225934002.png)

Because we need to ensure that the project is successfully deployed before triggering, if vercel deployment is used, the trigger conditions can be as follows.

```yaml title='.github/workflows/docsearch.yml'
name: docsearch

on: deployment

jobs:
  algolia:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v2

      - name: Get the content of docsearch.json as config
        id: algolia_config
        run: echo "::set-output name=config::$(cat docsearch.json | jq -r tostring)"

      - name: Run algolia/docsearch-scraper image
        env:
          ALGOLIA_APP_ID: ${{ secrets.ALGOLIA_APP_ID }}
          ALGOLIA_API_KEY: ${{ secrets.ALGOLIA_API_KEY }}
          CONFIG: ${{ steps.algolia_config.outputs.config }}
        run: |
          docker run \
            --env APPLICATION_ID=${ALGOLIA_APP_ID} \
            --env API_KEY=${ALGOLIA_API_KEY} \
            --env "CONFIG=${CONFIG}" \
            algolia/docsearch-scraper
```

Add [secrets](https://docs.github.com/en/actions/security-guides/encrypted-secrets#creating-encrypted-secrets-for-a-repository) to your Github repository and submit code to trigger the crawler rules.

## [orama](https://docs.oramasearch.com/open-source/plugins/plugin-docusaurus)

The process of configuring Algolia is a little bit complicated. Here you can integrate [orama](https://docs.oramasearch.com/open-source/plugins/plugin-docusaurus)，in Docusaurus , which is a full-text, vector and hybrid search query service running in the browser, server and edge. The final effect is shown in the figure

![](https://img.kuizuo.cn/2024/0118082834-202401180828818.png)

## Local search

If you think the Algolia application is too troublesome, Docusaurus also provides local search, but the search is definitely worse than the full-text search.

Local Search Plugins：[docusaurus-search-local](https://github.com/cmfcmf/docusaurus-search-local)
