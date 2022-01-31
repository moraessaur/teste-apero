---
title: Blog, portfolio & snippets
description: |
  Aqui reservo um espaço para postar assuntos relacionados à ciência de dados em geral em uma espécie de blog. Também tenho incluído aqui alguns posts para ajudar colegas e amigos a entenderem algumas ferramentas básicas de R e análise de dados, que podem auxiliar em suas pesquisas, além de servirem como snippets para eu mesmo. Todo código utilizado aqui pode ser conferido no [repositório deste site (na pasta do blog)!](https://github.com/moraessaur/teste-apero/tree/main/content/blog)
  Para projetos de meu portfolio de trabalho & consultoria, conto com uma sessão separada (projetos). 
author: "Lucas Moraes"
show_post_thumbnail: true
show_author_byline: false
show_comments: true
show_post_date: false
# for listing page layout
layout: list # list, list-sidebar, list-grid

# for list-sidebar layout
sidebar: 
  title: A Sidebar for Your Thoughts
  description: |
    This is a fully featured blog that supports categories,
    tags, series, and pagination. Even this sidebar offers 
    a ton of customizations.
    
    Check out the _index.md file in the /blog folder 
    to edit this content. 
  author: "The R Markdown Team @RStudio"
  text_link_label: Subscribe via RSS
  text_link_url: /index.xml
  show_sidebar_adunit: false # show ad container

# set up common front matter for all pages inside blog/
cascade:
  author: "The R Markdown Team @RStudio"
  show_author_byline: true
  show_post_date: true
  show_disqus_comments: false # see disqusShortname in site config
  # for single-sidebar layout
  sidebar:
    text_link_label: View recent posts
    text_link_url: /blog/
    show_sidebar_adunit: false # show ad container
---

** No content below YAML for the blog _index. This file provides front matter for the listing page layout and sidebar content. It is also a branch bundle, and all settings under `cascade` provide front matter for all pages inside blog/. You may still override any of these by changing them in a page's front matter.**
