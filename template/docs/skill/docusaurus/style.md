---
id: docusaurus-style
slug: /docusaurus-style
title: Styles and Pages
authors: kuizuo
---

## [Style and layout](https://docusaurus.io/zh-CN/docs/styling-layout#styling-your-site-with-infima)

Docusaurus is a React single-page application. You can style the website like a normal React application, such as [tailwindcss](https://tailwindcss.com/) and component libraries. However, introducing these will increase the code size, so in this theme I use global styles and css modules.

## Change theme color

You can set the main color and background color [here](https://docusaurus.io/zh-CN/docs/styling-layout#styling-your-site-with-infima) to see the effect in light and dark modes. For example, my theme color is `#12AFFA`

`@docusaurus/preset-classic` Use [Infima](https://infima.dev/) as the underlying style framework. Infima provides flexible layouts and common UI component styles suitable for content-centric websites (blogs, documents, homepages). For more details, please check out [the Infima website](https://infima.dev/)。

## Home

[Because the blog-only mode](https://docusaurus.io/docs/blog#blog-only-mode)，is set , there is no dedicated homepage, but the blog list is set as the homepage. You need to `src/pages/index.tsx` delete the file (or rename it), otherwise it will cause a homepage path conflict. Of course, you can also make a dedicated homepage, like docusaurus, and then jump to the blog list page.

So the blog page is the homepage. But just the blog is not enough, so the Hero component is added, which is what the first visit to the blog looks like.

The SVG background file address on the right side of the homepage is: `src/components/Hero/img/hero_main.svg`, the illustration comes from [unDraw](https://undraw.co/illustrations), you can find this kind of illustration style background on this website. Or you can find someone who specializes in illustration design to design it for you.

## Custom pages

`Archive`, `Friends`, `Navigation`, `Projects` and `About` me pages src/pagesare defined in the directory, and the corresponding routes are mapped according to the file name. For page creation, see [Create a page | Docusaurus](https://docusaurus.io/zh-CN/docs/creating-pages)