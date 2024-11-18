---
id: docusaurus-deploy
slug: /docusaurus-deploy
title: deploy
authors: kuizuo
---

I used [Vercel](https://vercel.com/) before , which did not require any configuration. So I only needed to focus on outputting content. This is the article I deployed using Vercel at that time [Vercel deployment personal blog](https://kuizuo.cn/blog/vercel-deploy-blog)

But now,`vercel.app` due to DNS pollution, that is, blocked, it is inaccessible in China. Although it is possible to access it by using your own domain name to resolve to Vercel, being blocked means that the resolution speed of domestic DNS will inevitably decrease, resulting in a decrease in site access speed.

In addition, I wanted to have a better visitor experience, so I decided to use different domestic and foreign analysis methods to speed up access.

First, in the line type, different record values ​​are made for domestic and overseas. Domestic CDN services are used in China, while Vercel is used overseas.

![image-20221204161431863](https://img.kuizuo.cn/image-20221204161431863.png)

In this way, my domestic access is to access the domestic CDN, and my foreign access is to access Vercel's CDN. In this way, the networks in different regions can have a good access speed. You can go to [Ping.cn: Website Speed ​​Test-ping Detection](https://www.ping.cn/) to test the access speed of your site.

The following are the results of my website speed test. You can also visit [kuizuo.cn to check the network speed test results in various regions across the country - Ping.cn](https://www.ping.cn/http/kuizuo.cn) online

![image-20221204161146327](https://img.kuizuo.cn/image-20221204161146327.png)

Sure enough, it’s different when you spend money.

## Continuous integration

Since Vercel can automatically pull repository code and build and deploy it by itself, usually no configuration is required.

Since the code is submitted to the code repository (github), we need to use CI services to help us complete these tasks. Here I use [Github Action](https://github.com/marketplace) to help me build, the build log can be found at [Actions · kuizuo/blog](https://github.com/kuizuo/blog/actions) The following is my configuration file

```yaml title='.github/workflows/ci.yml' icon='logos:github-actions'
name: CI

on:
  push:
    branches:
      - main

jobs:
  build-and-deploy:
    runs-on: ${{ matrix.os }}

    strategy:
      matrix:
        os: [ubuntu-latest] # macos-latest, windows-latest
        node: [18]

    steps:
      - uses: actions/checkout@v4

      - name: Set node version to ${{ matrix.node-version }}
        uses: actions/setup-node@v4
        with:
          node-version: ${{ matrix.node-version }}

      - run: corepack enable

      - name: Setup
        run: npm i -g @antfu/ni

      - name: Install
        run: nci

      - name: Build
        run: nr build

      - name: SSH Deploy
        uses: easingthemes/ssh-deploy@v4.1.10
        env:
          SSH_PRIVATE_KEY: ${{ secrets.SSH_PRIVATE_KEY }}
          ARGS: '-avzr --delete'
          SOURCE: 'build'
          REMOTE_HOST: ${{ secrets.REMOTE_HOST }}
          REMOTE_USER: 'root'
          TARGET: '/opt/1panel/apps/openresty/openresty/www/sites/kuizuo.cn/index'
```

Waiting for CI to put the final build product on its own server via rsync completes the entire deployment process.

When everything is configured, I just need to push the code to the remote repository, and Github Action and Vercel will complete their respective tasks. After waiting for a while, visit the site again, and the code just submitted will take effect successfully.

## How to deploy without domain name and server？

Of course, the above is just my configuration plan. Many partners may not have their own domain name or their own server, so they just want to get something for free. So here I can only recommend [Netlify](https://www.netlify.com/), and then access it through Netlify's second-level domain name such as `kuizuo-blog.netlify.app`.

I personally recommend getting your own domain name, which can be accessed through Vercel's custom domain name. And because your own domain name is not resolved to a server in mainland China (Vercel's server is not in mainland China), there is no need to file a record, which is a more cumbersome step.
