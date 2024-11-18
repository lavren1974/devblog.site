---
id: docusaurus-plugin
slug: /docusaurus-plugin
title: Plugins
authors: kuizuo
---

Under `docusaurus.config.ts` plugins, you can see all plugins and plugin configurations.

```typescript title='docusaurus.config.ts' icon='logos:docusaurus'
plugins: [
    'docusaurus-plugin-image-zoom',
    'docusaurus-plugin-sass',
    '@docusaurus/plugin-ideal-image',
    ['docusaurus-plugin-baidu-tongji', { token: 'xxxxxxxxxxxxxxx' }],
    [
      '@docusaurus/plugin-pwa',
      {
        debug: process.env.NODE_ENV === 'development',
        offlineModeActivationStrategies: ['appInstalled', 'standalone', 'queryString'],
        pwaHead: [
          { tagName: 'link', rel: 'icon', href: '/img/logo.png' },
          { tagName: 'link', rel: 'manifest', href: '/manifest.json' },
          { tagName: 'meta', name: 'theme-color', content: '#12affa' },
        ],
      },
    ],
    [
      './src/plugin/plugin-content-blog', // 为了实现全局 blog 数据，必须改写 plugin-content-blog 插件
      {
        path: 'blog',
        editUrl: ({ locale, blogDirPath, blogPath, permalink }) =>
          `https://github.com/kuizuo/blog/edit/main/${blogDirPath}/${blogPath}`,
        editLocalizedFiles: false,
        blogDescription: 'blog Description',
        blogSidebarCount: 10,
        blogSidebarTitle: 'Blogs',
        postsPerPage: 10,
        showReadingTime: true,
        readingTime: ({ content, frontMatter, defaultReadingTime }) =>
          defaultReadingTime({ content, options: { wordsPerMinute: 300 } }),
        feedOptions: {
          type: 'all',
          title: '愧怍',
          copyright: `Copyright © ${new Date().getFullYear()} 愧怍 Built with Docusaurus.<p><a href="http://beian.miit.gov.cn/" class="footer_lin">${beian}</a></p>`,
        },
      },
    ],
  ],
```

## [plugin-image-zoom](https://github.com/flexanalytics/plugin-image-zoom)

Image resizing plugin for Docusaurus.

## plugin-sass

Support sass preprocessor

## plugin-baidu-tongji

Enable the site to support [Baidu Statistics](https://tongji.baidu.com/web/welcome/login) ，so that you can see which pages your site visitors are mainly viewing and their behavior records, as shown in the figure below.
![image-20221204153015256](https://img.kuizuo.cn/image-20221204153015256.png)

At the same time [Footer](https://github.com/kuizuo/blog/blob/main/src/theme/Footer/index.tsx#L3) Added [@vercel/analytics](https://github.com/vercel/analytics) The prerequisite is that this project needs to be deployed on Vercel.

## [plugin-pwa](https://docusaurus.io/zh-CN/docs/api/plugins/@docusaurus/plugin-pwa)

Create a PWA documentation site that supports offline mode and app installation, like the one below.

![image-20221204153401244](https://img.kuizuo.cn/image-20221204153401244.png)

## plugin-content-blog

Since the official [plugin-content-blog](https://docusaurus.io/zh-CN/docs/api/plugins/@docusaurus/plugin-content-blog) plug-in does not set the blog data as global, it can only `BlogListPage` be obtained in the component of the blog list page. Since some components of this blog need to use some data, we will `plugin-content-blog` modify it here and add the blog information to the global data. All blog information can be accessed on any page.

```typescript title='src/plugin/plugin-content-blog.ts'
async function blogPluginEnhanced(context, options) {
  const blogPluginInstance = await blogPlugin(context, options)

  return {
    ...blogPluginInstance,
    async contentLoaded({ content, allContent, actions }) {
      // Create default plugin pages
      await blogPluginInstance.contentLoaded({ content, allContent, actions })

      // Create your additional pages
      const { blogPosts, blogTags } = content
      const { setGlobalData } = actions

      setGlobalData({
        posts: blogPosts.slice(0, 10), // Only store 10 posts
        postNum: blogPosts.length,
        tagNum: Object.keys(blogTags).length,
      })
    },
  }
}
```

:::warning 

These data may take up some space. [Click me](https://github.com/facebook/docusaurus/pull/7163#issuecomment-1096780257) check the details.

:::
