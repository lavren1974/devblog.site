---
id: docusaurus-comment
slug: /docusaurus-comment
title: Comment Service
authors: kuizuo
---

Here are two review services recommended

Giscus: Based on GitHub Discussions, it is relatively friendly to programmers, and comment information prompts are sent through the GitHub mailbox.

Waline: It requires building backend services and database services, providing comment and pageview services, and is highly scalable.

## [giscus](https://giscus.app)

The previous comments used gitalk, but that was based on github issues, and issues could not be closed. Every time I opened the repository, I would see dozens of issues, which was very unfriendly.

So I later considered switching to [giscus](https://giscus.app/zh-CN)，, a comment system driven by [GitHub Discussions](https://docs.github.com/en/discussions) . First, we need to ensure the following:

1. **This repository is[public](https://docs.github.com/en/github/administering-a-repository/managing-repository-settings/setting-repository-visibility#making-a-repository-public)**，, otherwise visitors will not be able to view the discussion (it does not need to be a blog project, any repository will do).
2. **[giscus](https://github.com/apps/giscus) app is installed** otherwise visitors will not be able to comment and respond.
3. The **Discussions**  feature has been [enabled in your repository](https://docs.github.com/en/github/administering-a-repository/managing-repository-settings/enabling-or-disabling-github-discussions-for-a-repository)。


This blog has built-in comment component [src/component/Comment](https://github.com/kuizuo/blog/blob/main/src/components/Comment/index.tsx)，so you only need to `docusaurus.config.ts` set the giscus configuration in .

### Configure giscus

Open the [giscus](https://giscus.app/) official website and fill in the corresponding information to get a configured `<script>` label .

```html
<script
  src="https://giscus.app/client.js"
  data-repo="kuizuo/blog"
  data-repo-id="MDEwOlJlcG9zaXRvcnkzOTc2Mxxxxx"
  data-category="General"
  data-category-id="DIC_kwDOF7NJDM4CPK95"
  data-mapping="title"
  data-strict="0"
  data-reactions-enabled="1"
  data-emit-metadata="0"
  data-input-position="top"
  data-theme="light"
  data-lang="zh-CN"
  crossorigin="anonymous"
  async
></script>
```

Since I  `src/component/Comment` have merged configurations in the component and support theme changes and internationalization, you only need to copy `data-repo`, `data-repo-id`, `data-category`, `data-category-id` and `docusaurus.config.ts` The following is my configuration file.

```javascript title='docusaurus.config.ts' icon='logos:docusaurus'
giscus: {
  repo: 'kuizuo/blog',
  repoId: 'MDEwOlJlcG9zaXRvcnkzOTc2Mxxxxx',
  category: 'General',
  categoryId: 'DIC_kwDOF7NJDM4CPK95',
  theme: 'light',
  darkTheme: 'dark',
}
```

:::info 

Remember to replace the above data with yours. If you don't, the comment information will be under My Discussions.

:::

## [waline](https://github.com/walinejs/waline)

Another popular blog comment system at present is waline, which can provide comment and pageview services. Since it needs to be combined with back-end services and database services, it is more troublesome to configure than giscus, but it does not require github discussions, so it is also the standard configuration for most bloggers.

For more information on how to configure, see the official [Quick Start Guide | Waline](https://waline.js.org/guide/get-started.html)
