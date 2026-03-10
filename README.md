# v0xn0x.com

Blog pessoal sobre cybersecurity, construido com [Jekyll](https://jekyllrb.com/) e o tema [Chirpy](https://github.com/cotes2020/jekyll-theme-chirpy) (v6.5.2).

## Requisitos

- [Ruby](https://www.ruby-lang.org/) >= 3.0
- [Bundler](https://bundler.io/)
- [Node.js](https://nodejs.org/) e npm
- [Git](https://git-scm.com/)

## Executando localmente

1. Instale as dependencias:

```bash
bundle install
npm install
```

2. Compile os assets JavaScript:

```bash
npm run build
```

3. Inicie o servidor de desenvolvimento:

```bash
bash tools/run
```

O site estara disponivel em `http://localhost:4000` com live reload ativado.

### Outros comandos uteis

| Comando | Descricao |
|---|---|
| `bash tools/test` | Build de producao + validacao com htmlproofer |
| `npm test` | Lint dos arquivos SCSS |
| `npm run fixlint` | Corrige problemas de lint automaticamente |

## Criando posts

### 1. Crie o arquivo

Crie um arquivo Markdown na pasta `_posts/` seguindo o padrao de nomenclatura:

```
_posts/YYYY-MM-DD-titulo-do-post.md
```

Exemplo: `_posts/2024-05-15-analise-de-malware.md`

### 2. Adicione o front matter

Todo post deve comecar com um bloco de front matter YAML:

```yaml
---
title: Titulo do Post
author: v0xn0x
date: 2024-05-15 10:00:00 -0300
categories: [CATEGORIA, SUBCATEGORIA]
tags: [TAG1, TAG2]
---
```

#### Campos opcionais

| Campo | Descricao |
|---|---|
| `pin: true` | Fixa o post na pagina inicial |
| `math: true` | Habilita renderizacao de expressoes matematicas |
| `mermaid: true` | Habilita diagramas Mermaid |
| `image.path` | Caminho da imagem de capa (ex: `/img/imagem.png`) |
| `image.alt` | Texto alternativo da imagem de capa |
| `toc: false` | Desabilita o indice lateral (habilitado por padrao) |
| `comments: false` | Desabilita comentarios no post |

### 3. Escreva o conteudo

Apos o front matter, escreva o conteudo em Markdown. O tema suporta:

- Blocos de codigo com syntax highlighting
- Imagens: `![descricao](/img/nome-da-imagem.png)`
- Expressoes matematicas (com `math: true`)
- Diagramas Mermaid (com `mermaid: true`)

### 4. Imagens

Coloque as imagens dos posts na pasta `img/` e referencie com caminho absoluto:

```markdown
![descricao](/img/minha-imagem.png)
```

### 5. Visualize o post

Com o servidor local rodando (`bash tools/run`), o post aparecera automaticamente em `http://localhost:4000`.

## Deploy

O deploy e automatico via GitHub Actions. Ao fazer push para a branch `master`, o workflow `.github/workflows/pages-deploy.yml` compila o site e publica no GitHub Pages.

## Licenca

Este projeto utiliza a [MIT License](LICENSE).
