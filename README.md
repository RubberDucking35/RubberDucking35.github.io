# rubberduck.log

Blog personnel — sysadmin, geek, papa, Bretagne.

Jekyll + GitHub Pages. Fond noir, police blanche.

## Installation locale

```bash
# Prérequis : Ruby + Bundler
gem install bundler

# Cloner le repo
git clone https://github.com/RubberDucking35/RubberDucking35.github.io.git
cd RubberDucking35.github.io

# Installer les dépendances
bundle install

# Lancer en local
bundle exec jekyll serve
# → http://localhost:4000
```

## Écrire un article

Créer un fichier dans `_posts/` avec le format `YYYY-MM-DD-titre-article.md`.

**Front matter minimal :**

```yaml
---
layout: post
title: "Titre de l'article"
description: "Résumé court affiché dans la liste."
date: 2025-01-15
section: it       # 'it' ou 'culture'
tags: [intune, m365]
---
```

Puis écrire le contenu en Markdown.

## Déployer

```bash
git add .
git commit -m "Nouvel article : titre"
git push
```

GitHub Pages compile et déploie automatiquement en ~30 secondes.

## Structure

```
_layouts/
  default.html    → template HTML de base
  post.html       → template article
_posts/
  YYYY-MM-DD-*.md → articles
assets/css/
  main.scss       → styles (dark theme)
_config.yml       → configuration Jekyll
index.html        → page d'accueil
a-propos.md       → page à propos
```
