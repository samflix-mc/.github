<h1 align="center">samflix-mc</h1>

<p align="center">
  Un réseau <strong>Minecraft Java moddé francophone</strong>, et l'outillage qui va autour.<br>
  <em>En développement — rien n'est ouvert au public.</em>
</p>

<p align="center">
  <img alt="Minecraft 1.21.1" src="https://img.shields.io/badge/Minecraft-1.21.1-5b8731?style=flat-square">
  <img alt="NeoForge" src="https://img.shields.io/badge/NeoForge-21.1-f16436?style=flat-square">
  <img alt="Rust" src="https://img.shields.io/badge/Rust-launcher-b7410e?style=flat-square&logo=rust&logoColor=white">
</p>

---

Un serveur moddé impose que chaque joueur charge **exactement** le même jeu de
mods que le serveur. Installer NeoForge à la main, recopier les bons fichiers,
recommencer à chaque mise à jour : c'est là que la plupart des joueurs
abandonnent. On construit ce qui supprime cette étape.

| | |
|---|---|
| **[mc-launcher](https://github.com/samflix-mc/mc-launcher)** | Le launcher. Authentification Microsoft officielle, en Rust. |
| **[mc-launcher-site](https://github.com/samflix-mc/mc-launcher-site)** | [mc-launcher.ggy.info](https://mc-launcher.ggy.info) |
| **[.github](https://github.com/samflix-mc/.github)** | Workflows de déploiement partagés. |
| mc-server-docker · mc-content | Serveurs et contenu — privés, infrastructure en exploitation. |

Le launcher **n'a pas de système de comptes** : le joueur s'identifie chez
Microsoft, et le launcher ne reçoit qu'un jeton. La possession du jeu est
vérifiée auprès de Mojang. Aucun contrôle de sécurité, d'authentification ou de
licence n'est contourné.

<p align="center"><sub>Projet personnel, non affilié à Mojang ni à Microsoft.</sub></p>
