---
module: B-INN-000
title: Workshop Bot Discord
subtitle: Créer son premier Bot Discord

language: NodeJS
build: npm

noFormalities: false
noCleanRepo: false
noBonusDir: true
noErrorMess: true

author: Fazanwolf
version: 1.0
---

# Workshop Bot Discord

## Pré-requis

Si vous n'avez pas déjà installé les outils nécessaires à ce workshop suivez les instructions situées ici : #br
[https://github.com/Fazanwolf/workshop-bot-discord/blob/master/REQUIREMENTS.md](https://github.com/Fazanwolf/workshop-bot-discord/blob/master/REQUIREMENTS.md)

## Configuration Discord

### Création d'une Application Discord

Pour pouvoir créer votre bot, vous devrez créer une application discord.

Pour ce faire, créez une nouvelle application sur le portail développeur de discord, nommez votre
application et acceptez-les clauses.

#hint([Portail Développeur](https://discord.com/developers/applications).)

#hint(Sur l'onglet `General Information`, vous pouvez customiser votre application.
(nom, description, icon de profil, etc))

### Transformer une Application en Bot

Pour pouvoir inviter votre application sur votre serveur discord de test, vous devez la définir comme bot.

Allez sur l'onglet `Bot` et ajoutez un bot.

#hint(Parametrez l'`Authorization Flow` de votre bot comme vous le souhaitez.)

### Les privilèges

Pour que votre bot puisse utiliser l'API de Discord, il vous faut activer certains privilèges.

#hint([Qu'est-ce qu'une API ?](https://www.redhat.com/fr/topics/api/what-are-application-programming-interfaces))

Pour ce faire, sur l'onglet Bot, dans `Privileged Gateway Intent` ajoutez les Gateway que vous souhaitez.

#warn(Ne partagez surtout pas votre `BOT_TOKEN`.)

### Ajouter votre bot à un serveur

Maintenant que votre bot est paramétré, il faut l'ajouter à un serveur.

Sur l'onglet `OAuth2`, dans le sous onglet `URL Generator`, sélectionnez la méthode `In-app Authorization`,
cochez `bot` et ajoutez vos permissions.

#warn(Un bon bot est un bot avec des permissions clairement définies.)

Copiez le lien généré dans un navigateur et sélectionnez votre serveur.

#newpage

## Configuration

### L'environnement

Par sécurité, créez un fichier `.env` à la racine de votre repository.

Dans ce fichier, vous aurez besoin:

- BOT_TOKEN: dans l'onglet `Bot` > `Reset Token`
- CLIENT_ID: l'ID de votre application
- GUILD_ID: [Developer Mode](https://beebom.com/how-enable-disable-developer-mode-discord/) > Clique droit sur le
  serveur où se trouve votre bot > `Copy ID`
- LOG_ID: l'ID d'un channel de votre serveur pour vos logs

```txt
BOT_TOKEN=VOTRE_BOT_TOKEN
CLIENT_ID=VOTRE_CLIENT_ID
GUILD_ID=VOTRE_GUILD_ID
LOG_ID=VOTRE_CHANNEL_ID
```

#warn(Vous ne devez jamais partager votre `BOT_TOKEN`.)

#hint(Pour utiliser les variables d'environnement en Node.js, utilisez [`dotenv`](https://www.npmjs.com/package/dotenv).)

### Le projet

#hint(Le template vous épargne la [création d'un projet avec npm](https://developer.mozilla.org/en-US/docs/Learn/Server-side/Express_Nodejs/development_environment).)

Listes des packages du projet:

- dotenv
- discord.js
- fs
- path

#warn(Assurez-vous d'installer les packages nécessaires.)

#newpage

## Le bot: Pas pratique mais fonctionnel

Afin de voir si votre configuration de bot est fonctionnelle, nous allons utiliser le minimum requis pour faire un bot.

### Mise en place du Client Discord

Pour commencer, nous allons créer notre client Discord.

Notre client (`Client`) a besoin de permissions pour fonctionner.

Nous allons donc les définir avec des `GatewayIntentBits`.

#hint(Documentation [Discord.js](https://discord.com/developers/docs/topics/gateway#list-of-intents).)

### Ma première commande

Pour faire simple, vous allez créer un tableau de commande.

Une commande est un objet qui stocke:

- name: Le nom de ta commande
- description: La description de ta commande

#hint(Cherchez comment faire un objet et des tableaux par vous-même.)

### Enregistrer les commandes

Pour enregistrer nos commandes, vous aurez besoin de créer une variable `rest`.

`rest` est créé à partir de REST, **version 10** de discord.js.

On utilise ensuite la méthode `put()` avec comme paramètre, la `Routes` pour les commandes
d'une application et votre tableau de commandes.

#hint(Vous pouvez définir si vous souhaitez enregistrer globalement ou par guild en modifiant la route.)

### Supprimer les commandes

Pour supprimer les commandes qui ne sont plus dans votre code ou à jour.

On utilise la méthode `delete()` avec comme paramètre, la `Routes` pour les commandes d'une application et votre tableau de commandes.

### Evènement: "Bot: Je suis ready!"

Pour notre premier "écouteur" d'évènement et pour pouvoir tester notre bot.

On va avoir besoin d'écouter l'évènement qui permet de savoir si votre bot est prêt à l'usage.

Pour cela, on va créer un listener qui affichera `Bot: Je suis ready!`

Voici un exemple de listener:

```js
client.on(Events.TonEvents, async () => {
    // Ton code
});
```

#hint(Regardez les évènements [`Client`](https://discord.js.org/#/docs/discord.js/main/search?query=Client%23).)

#warn(Attention aux évènements qui doivent être exécutés qu'une seule fois.)

### Tester

Pour tester, vous devrez ajouter `client.login(TON_BOT_TOKEN);` à la toute fin du fichier `bot.js`

La commande a utilisé pour démarrer le bot:

```bash
npm run start
```

### Exécuter une commande

Votre commande est enregistrée dans la guild mais n'effectue rien pour le moment. Pour exécuter nos commandes, on doit
aussi écouter un évènement.

Une fois le listener, l'évènement et les paramètres mis en place, on vérifie:

- Le type d'interaction correspond à celui d'une `SlashCommand`, retourner si ce n'est pas le cas.
- Le nom de la commande correspond à celle de notre commande, coder le coeur de notre commande.

Le but de la commande `help` est de répondre à l'utilisateur avec un message d'aide en embed.

### Logguer des actions

Pour logguer des actions, on va créer un listener qui écoute un évènement.

Ce listener aura pour évènement la suppression des messages et devra envoyer un message dans
un channel choisi.

#hint(Félicitation, vous avez réussi à faire un bot pas beau.
Mais ce n'est que le début du workshop.)

#newpage

## Le bot: avec architecture

### Création d'un type

On va gérer nos différentes fonctions avec un objet `InteractionType` qui servira d'énumeration avec les types
d'interaction.

Les types:

- Slash = "slash"
- Button = "button"
- Select = "select"
- Modal = "modal"

### Architecture Commandes

Dans le dossier `commands`, créez un fichier `help.slash.js`.

Dans ce fichier, nous allons stocker la commande.

La commande sera sous la forme:

```js
module.exports = {
  data: "...", // Le builder type de votre commande
  execute(interaction) {
    // Votre code
  },
};
```

#hint(Documentation [Discord.js](https://discord.js.org/#/docs/main/stable/class/BaseCommandInteraction).)

### Mise en place d'un gestionnaire de commandes

Pour gérer nos commandes, nous allons créer un gestionnaire de commandes.

Cela va nous permettre d'automatiser et rendre plus lisible le code.

Pour cela, nous allons créer une Collection `slash` dans client, qui va nous permettre de stocker nos commandes.

#hint(Une [`Collection`](https://discord.js.org/#/docs/collection/main/general/welcome) est une [`Map`](https://developer.mozilla.org/fr/docs/Web/JavaScript/Reference/Global_Objects/Array/map) implémenter et optimiser par Discord.)

Vous pouvez ensuite créer une fonction `registerSlashCommand` qui aura:

- En paramètre, le client
- En retour, un tableau de data des commandes enregistrées en JSON

Cette fonction devra stocker les commandes dans un tableau et les enregistrer dans la Collection.

Vous pouvez supprimer le tableau de commande utilisé précédemment et
appeler la fonction `registerSlashCommand`.

### Architecture Evènements

Dans le dossier `events`, créez un fichier `ready.slash.js`.

Dans ce fichier, nous allons stocker le listener précédemment créer.

Votre fichier doit exporter un objet avec:

- Le nom de l'évènement
- Un booléen pour savoir si l'évènement
- Une fonction nommée `execute` qui aura les paramètres nécessaires à l'évènement

Copiez tous vos listeners sous cette forme.

### Mise en place d'un gestionnaire d'évènements

Vous devez créer une fonction `registerEvents` qui va utiliser les fonctions chargées dans
`client.on` et `client.once`.

#hint(Si vous avez bien fait votre gestionnaire de commandes, vous pouvez récupérer le corps de votre fonction `registerSlashCommand`.)

Appelez ensuite la fonction `registerEvents` dans votre fichier `bot.js`.

### Créer une interaction: Bouton

Pour créer une interaction de type bouton, vous allez créer un fichier dans `commands` nommé
`click-me.slash.js` et `click-me.bouton.js`.

Dans le fichier `click-me.slash.js`, vous allez créer une `data` du type `SlashCommand` et un `execute` qui
répondra à l'utilisateur avec un bouton.

Dans le fichier `click-me.bouton.js`, vous allez créer une `data` du type `Button` qui aura les mêmes informations
que le bouton de la commande `click-me.slash.js` et un `execute` qui répondra avec une image.

### Mise en place d'une gestionnaire d'interaction

Créez une fonction `registerInteraction` qui prendra client, un type.

Cette fonction fonctionnera de la même manière que `registerSlashCommand`, à la différence que
vous devrez gérer chaque type.

#warn(Attention à la key que vous enregistrez dans la Collection.)

Appelez ensuite la fonction `registerInteraction` dans votre fichier `bot.js` avant les évènements.

#newpage

### Créer des interactions

Amusez-vous à créer des interactions de type `Selection` et `Modal`.

#hint(Documentation [Discord.js](https://discord.js.org/#/docs/main/stable/class/BaseCommandInteraction).)

### Optimisation

Vous pouvez repasser sur votre code et optimiser le code qui se duplique.

#hint(Vous pouvez passer des fonctions dans les paramètres de vos fonctions, faire des boucles, créer des fichiers ou dossiers pour mieux structurer.)

## Imagination

Maintenant que vous savez:

- Faire un bot avec une architecture
- Gérer des évènements
- Créer des commandes
- Manipuler des interactions

Vous pouvez laisser votre imagination porter vos créations.

#hint(J'espère que ce workshop vous a plu et que vous avez appris quelque chose.)