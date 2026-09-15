<h1 align="center">samflix-mc</h1>

<p align="center">
  Un réseau <strong>Minecraft Java moddé francophone</strong>, et l'outillage qui va autour.<br>
  <em>En développement — rien n'est ouvert au public.</em>
</p>

<p align="center">
  <img alt="Minecraft 1.21.1" src="https://img.shields.io/badge/Minecraft-1.21.1-5b8731?style=flat-square">
  <img alt="NeoForge" src="https://img.shields.io/badge/NeoForge-21.1-f16436?style=flat-square">
  <img alt="Rust" src="https://img.shields.io/badge/Rust-launcher-b7410e?style=flat-square&logo=rust&logoColor=white">
  <img alt="Statut" src="https://img.shields.io/badge/statut-prototype-e0a800?style=flat-square">
</p>

---

### Le projet

Un serveur moddé impose que chaque joueur charge **exactement** le même jeu de
mods que le serveur : à la moindre différence, la connexion est refusée. Demander
à chacun d'installer NeoForge à la main puis de recopier les bons fichiers, et de
recommencer à chaque mise à jour, est la première cause d'abandon sur ce type de
serveur.

D'où ce qu'on construit ici : un launcher qui fait ces étapes à la place du
joueur, une infrastructure reproductible pour le réseau, et un dépôt de contenu
déclaratif qui sert de source unique aux deux.

### Les dépôts

| | Rôle |
|---|---|
| **[mc-launcher](https://github.com/samflix-mc/mc-launcher)** | Launcher de bureau. Authentification Microsoft officielle en Rust — device code, Xbox Live, XSTS, vérification de licence. |
| **[mc-launcher-site](https://github.com/samflix-mc/mc-launcher-site)** | La page de présentation, [mc-launcher.ggy.info](https://mc-launcher.ggy.info). Tailwind et DaisyUI compilés, déployés par Helm. |
| **mc-server-docker** | Le réseau serveur : proxy Velocity et deux backends NeoForge. |
| **mc-content** | Mods, plugins et configurations. Déclaratif, aucun binaire versionné. |
| **[.github](https://github.com/samflix-mc/.github)** | Workflows de déploiement partagés. |

### Comment ça se déploie

Trois environnements, une seule chaîne. Un `push` sur `dev` ou `main` déploie
dev ou preprod ; la production demande un tag, que seul le propriétaire peut
poser. Les workflows sont mutualisés dans [`.github`](https://github.com/samflix-mc/.github) :
`helm` s'exécute sur le serveur, le runner ne fait que du SSH.

### Authentification

Le launcher **n'a pas de système de comptes**. Il ne voit ni ne stocke aucun mot
de passe : le joueur s'identifie chez Microsoft, et le launcher ne reçoit qu'un
jeton. La possession du jeu est vérifiée auprès de Mojang. Aucun contrôle de
sécurité, d'authentification ou de licence n'est contourné.

---

<p align="center"><sub>
  Projet personnel, non affilié à Mojang ni à Microsoft.
</sub></p>
