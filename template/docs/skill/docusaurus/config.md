---
id: docusaurus-config
slug: /docusaurus-config
title: Configuration Files
authors: kuizuo
---

## docusaurus.config.ts

`docusaurus.config.ts` Located in the root directory of your website, contains configuration information for your site.

Here you can modify the logo, site name (title), author name, top announcement (announcementBar), navigation bar (navbar), bottom navigation (footer) and so on.

```typescript title='docusaurus.config.ts' icon='logos:docusaurus'
const config: Config = {
  title: 'title',
  url: 'https://kuizuo.cn',
  baseUrl: '/',
  favicon: 'img/favicon.ico',
  organizationName: 'kuizuo',
  projectName: 'blog',
  themeConfig: {
    image: 'img/logo.png',
    metadata: [
      {
        name: 'keywords',
        content: 'kuizuo, blog, javascript, typescript, node, react, vue, web',
      },
    ],
    // ...
}

export default config
```

At the same time, most of the configuration information can be placed here, such as search (algolia), comments (giscus), social links (socials), etc. These configurations can be obtained through docusaurus' built-in hooks (useThemeConfig, useDocusaurusContext).

Complete configuration information description [docusaurus.config.ts | Docusaurus](https://docusaurus.io/docs/api/docusaurus-config)

## sidebars.js

[A sidebar for configuring documents, such as technical notes](/docs/skill/) and [tool recommendations](/docs/basics/)。in this blog . Each item in the sidebar is a markdown file, and these files are stored in the docs directory for easy management.

[Sidebar | Docusaurus](https://docusaurus.io/docs/sidebar)

## Related information

### Basic information

You only need to search for **Shame** or **kuizuo** to find the key locations of the site name and author name , and then change them to yours.

### About me page

The details can `src/pages/about.mdx` be viewed and modified in .

Here you may need to show your technology stack. Here I recommend using [skillicons](https://skillicons.dev/) to generate icons for your technology stack, as shown below.

[![My Skills](https://skillicons.dev/icons?i=ts,nodejs,vue,nuxt,react,nextjs,tailwind,nestjs,prisma,postgres,redis,supabase,rust,wasm,vscode)](https://skillicons.dev)

The status information of GitHub is displayed using [GitHub Profile Summary Cards](https://github-profile-summary-cards.vercel.app/demo.html) or [github-stats](https://github.com/jstrieb/github-stats) ，Here I choose github-stats because it has animations, but if you need pictures, you need to build them yourself.

![](https://raw.githubusercontent.com/kuizuo/github-stats/master/generated/overview.svg#gh-light-mode-only)

![](https://raw.githubusercontent.com/kuizuo/github-stats/master/generated/languages.svg#gh-light-mode-only)

### Friends links, navigation, project pages

These three pages are implemented as custom pages through plugin-content-pages . If you want to know how to implement the page, you can see the custom page

Here you mainly focus on the data part. The data is stored in the root folder `/data` and uses ts as a type hint. The data will eventually be rendered in these pages. You only need to define the data to be displayed according to the type you match, and visit the corresponding page to view the effect.

### Social links

Just `data/social.ts` modify the social object in .

The following mainstream social accounts are built-in for you to choose from.

```typescript title='social.ts' icon='logos:typescript-icon'
export type Social = {
  github?: string
  twitter?: string
  juejin?: string
  qq?: string
  wx?: string
  cloudmusic?: string
  zhihu?: string
  email?: string
  discord?: string
}
```

## Other configuration

You may also need to configure giscus comments, search, site statistics, etc. These will be discussed in detail in [the plugin](/docs/docusaurus-plugin)。
