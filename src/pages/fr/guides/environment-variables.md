---
layout: ~/layouts/MainLayout.astro
title: Utiliser des variables d'environnement
description: Apprenez comment utiliser les variables d'environnement dans un projet Astro.
setup: |
  import ImportMetaEnv from '~/components/ImportMetaEnv.astro';
---

Astro utilise Vite pour les variables d'environnement, et permet d'utiliser [n'importe quelle méthode de Vite](https://vitejs.dev/guide/env-and-mode.html) pour obtenir et définir des variables d'environnement.

Notez que _toutes_ les variables d'environnement sont disponibles dans le code coté serveur, mais seulement les variables préfixées avec `PUBLIC_` sont disponibles dans le code client pour des raisons de sécurité.

Jetez un oeil à l'exemple officiel des [variables d'environnement](https://github.com/withastro/astro/tree/main/examples/env-vars) pour un aperçu des pratiques à appliquer.

```ini
SECRET_PASSWORD=motdepasse123
PUBLIC_ANYBODY=juste là
```

<p>
  Dans cet exemple, <code>PUBLIC_ANYBODY</code> ( disponible en tant que <ImportMetaEnv path=".PUBLIC_ANYBODY" /> ) sera accessible à la fois dans le code côté serveur et côté client, alors que <code>SECRET_PASSWORD</code> ( disponible en tant que <ImportMetaEnv path=".SECRET_PASSWORD" /> ) ne sera accessible que côté serveur.
</p>

## Variables d'environnement par défaut

Astro inclut quelques variables d'environnement par défaut :

<ul>
  <li>
    <ImportMetaEnv path=".MODE" /> (<code>development</code> | <code>production</code>): Représente le mode dans lequel le site tourne actuellement. Défini comme <code>development</code> en utilisant la commande <code>astro dev</code> et à <code>production</code> en utilisant <code>astro build</code>.
  </li>

  <li>
    <ImportMetaEnv path=".BASE_URL" /> (<code>string</code>): Représente l'URL de base sous laquelle votre site est déployé. Déterminé par <a href="/fr/reference/configuration-reference/#base">l'option <code>base</code> dans votre configuration</a>.
  </li>

  <li>
    <ImportMetaEnv path=".PROD" /> (<code>boolean</code>): Si votre site tourne en mode <i>"production"</i>.
  </li>

  <li>
    <ImportMetaEnv path=".DEV" /> (<code>boolean</code>): Si votre site tourne en mode <i>"development"</i> (toujours opposé à la valeur de <ImportMetaEnv path=".PROD" />).
  </li>

  <li>
    <ImportMetaEnv path=".SITE" /> (<code>string</code>): <a href="/fr/reference/configuration-reference/#site">L'option <code>site</code> dans votre configuration</a> spécifié dans le fichier <code>astro.config.mjs</code> de votre projet.
  </li>
</ul>

## Définir des variables d'environnement

Les variables d'environnement peuvent être chargées depuis les fichiers `.env` dans le répertoire de votre projet.

Vous pouvez aussi ajouter/attacher un mode (soit `production` ou `development`) en suffixe au nom du fichier, comme `.env.production` ou `.env.development`, qui rendent ces variables d'environnement uniquement actives dans ce mode.

Créez un fichier `.env` dans le répertoire de votre projet et ajoutez quelques variables à ce fichier.

```bash
# .env
# Ceci ne sera disponible que lorsque vous lancerez le serveur !
DB_PASSWORD="foobar"
# Ceci sera disponible partout !
PUBLIC_POKEAPI="https://pokeapi.co/api/v2"
```

```ini
.env                # Chargé dans tous les cas
.env.local          # Chargé dans tous les cas, ignoré par git
.env.[mode]         # Chargé uniquement dans le mode spécifié
.env.[mode].local   # Chargé uniquement dans le mode spécifié, ignoré par git
```

## Obtenir des variables d'environnement

<p>
  Au lieu d'utiliser `process.env`, avec Vite, vous utilisez <ImportMetaEnv />, qui utilise la fonctionnalité `import.meta` ajoutée dans ES2020.
</p>
<p>
  Par exemple, utilisez <ImportMetaEnv path=".PUBLIC_POKEAPI" /> pour obtenir la variable d'environnement `PUBLIC_POKEAPI`.
</p>

```js
// Quand import.meta.env.SSR === true
const data = await db(import.meta.env.DB_PASSWORD);

// Quand import.meta.env.SSR === false
const data = fetch(`${import.meta.env.PUBLIC_POKEAPI}/pokemon/squirtle`);
```

_Ne vous inquiétez pas si votre navigateur ne supporte pas <ImportMetaEnv />, Vite remplace toutes les mentions de <ImportMetaEnv /> par des valeurs statiques._

> ⚠️ATTENTION⚠️ :
> Étant donné que Vite remplace statiquement <ImportMetaEnv />, vous ne pouvez pas y accéder avec des clés dynamiques comme <ImportMetaEnv path="[key]" />.

## Autocomplétion pour TypeScript

<p>
  Par défaut, Vite fournit des définitions de type pour <ImportMetaEnv /> dans `vite/client.d.ts`.
</p>

Vous pouvez aussi définir d'autres variables d'environnement dans les fichiers `.env.[mode]`, mais vous voulez sûrement accéder à l'autocomplétion pour les variables d'environnement définies par l'utilisateur qui commencent par `PUBLIC_`.

Pour faire cela, vous pouvez créer un fichier `env.d.ts` dans le répertoire `src/`, puis étendre `ImportMetaEnv` comme ceci :

```ts
interface ImportMetaEnv {
  readonly DB_PASSWORD: string;
  readonly PUBLIC_POKEAPI: string;
  // plus de variables d'environnement...
}

interface ImportMeta {
  readonly env: ImportMetaEnv;
}
```
