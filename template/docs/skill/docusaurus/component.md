---
id: docusaurus-component
slug: /docusaurus-component
title: Custom Components
authors: kuizuo
description: docusaurus-component
---

The initial docusaurus project already has pre-set components, such as blog layout, tab archive page, etc. However, the styles of these components may not meet your aesthetic taste, or you may want to add something to these theme components. Then you need to use [Swizzle](https://docusaurus.io/zh-CN/docs/swizzling)

## Theme component

The theme components in docusaurus are stored in **@docusaurus/theme-classic/theme** If you want to overwrite a component, you can create a file with the same file path structure in the src/theme directory.

Like this

```
website
├── node_modules
│   └── @docusaurus/theme-classic
│       └── theme
│           └── Navbar.js
└── src
    └── theme
        └── Navbar.js
```

Whenever it is imported `@theme/Navbar` ，`website/src/theme/Navbar.js` it will be loaded first.

For more information about layered architecture , see [Client Architecture | Docusaurus](https://docusaurus.io/docs/advanced/client)

## swizzle component

To print `@docusaurus/theme-classic` an overview of all components, run:

```bash
npm run swizzle @docusaurus/theme-classic -- --list
```

Here we take the archive page as an example. The official archive page component is `theme/BlogArchivePage`

There are two ways to complete custom components:[pop-up components](https://docusaurus.io/docs/swizzling#ejecting) or [wrapper components](https://docusaurus.io/docs/swizzling#wrapping)

For example, to pop up a component, you can execute the following [command](https://docusaurus.io/zh-CN/docs/cli#docusaurus-swizzle)：

```bash
npm run swizzle @docusaurus/theme-classic BlogArchivePage -- --eject --typescript
```

This will create `src/theme/BlogArchivePage/index.tsx` the code for the archive page, and all you have to do is modify the code to achieve the style and functionality you want.。

However, this only gets the index.tsx file, and there may be subcomponents. So my general approach is `node_modules/@docusaurus/theme-classic/src/theme` to find the folder where the component is located in , and then copy the entire folder to `src/theme`. In this way, you can get the most original ts file, and at the same time, you can modify more places, which is more convenient for personalization.

:::warning

**However** , when using custom components, some theme components may have certain **risks** . Especially when upgrading Docusaurus, it becomes more difficult, because if the received attributes change, or the theme API used internally changes, it may cause page rendering failure.

For example, when I upgraded docusaurus to the official version 2.0.0, a page error occurred. The reason was that the data passed to the component by [plugin-content-blog](https://docusaurus.io/zh-CN/docs/api/plugins/@docusaurus/plugin-content-blog) changed, which made the data unable to be parsed, and naturally the page rendering failed.

:::

If you don't upgrade the dependencies, there will be no problem, but who can guarantee that some features of the new version will not attract users to upgrade? So when customizing components, you may need to maintain certain codes after upgrading the dependencies. What you need to do is to re-swizzle the latest file, then compare the changes and finally troubleshoot the problem.
