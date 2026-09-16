# .github

La mécanique commune aux dépôts de l'organisation, et le profil.

| | |
|---|---|
| [`environnements.yaml`](environnements.yaml) | ce que valent `dev`, `preprod` et `prod` |
| `resoudre-env.yml` | traduit une référence git en environnement, puis lit la table |
| `deploy-helm.yml` | déploie un chart sur le cluster K3s |
| `release-rust.yml` | compile un binaire pour cinq cibles et publie une release |
| `actions/ssh-k3s` | pose la clé de déploiement, exécute un script sur le nœud |

## `environnements.yaml`

Un seul endroit décide ce qu'est un environnement : namespace, hôtes du site,
port du proxy, tag de l'image de contenu, et si le pack du launcher y est
publié. **Ajouter un environnement, c'est ajouter une entrée** — aucun workflow
à toucher.

C'est aussi le seul endroit où se décide la version de contenu de la
production. Elle était auparavant recopiée à la main dans `mc-server` et dans le
workflow de `mc-launcher-site`, sans que rien ne vérifie qu'elles concordaient.
Or c'est cette concordance qui décide si un joueur entre dans la partie : les
registres NeoForge sont négociés à la connexion, et un mod en version
différente entre le client et le serveur éjecte le joueur sans message
exploitable.

La table vit ici parce que ce dépôt est **public** : `mc-content` et
`mc-server`, qui ne le sont pas, peuvent la lire sans jeton.

### La règle de déclenchement, la même pour tous

| référence | environnement |
|---|---|
| `main` | dev |
| `workflow_dispatch` | au choix, souvent preprod |
| tag `v*` | prod |

La production n'est jamais atteinte par un push — c'est la barrière
d'approbation du pauvre, les règles de protection d'environnement étant
réservées au plan Enterprise. La préproduction n'est jamais atteinte par une
branche : une répétition se décide.

## `resoudre-env.yml`

```yaml
jobs:
  env:
    uses: samflix-mc/.github/.github/workflows/resoudre-env.yml@main
    with:
      environnement: ${{ inputs.environnement }}   # facultatif, force la cible

  deploy:
    needs: env
    uses: samflix-mc/.github/.github/workflows/deploy-helm.yml@main
    with:
      namespace: ${{ needs.env.outputs.namespace }}
      ...
```

Sorties : `environnement`, `namespace`, `contenu-image`, `contenu-tag`,
`contenu-pull`, `pull-secret`, `site-hotes`, `site-url`, `site-pack`,
`jeu-port`, `jeu-deploiements`, `noeud`.

## `deploy-helm.yml`

```yaml
jobs:
  deploy:
    uses: samflix-mc/.github/.github/workflows/deploy-helm.yml@main
    with:
      chart-path: chart
      release: mon-service
      namespace: mc-dev
      environment: dev
      verify-url: https://exemple.ggy.info
      pull-secret: ghcr-pull     # si le chart tire une image privée
    secrets: inherit
```

`helm` s'exécute sur le serveur, dont le kubeconfig K3s pointe sur
`127.0.0.1:6443` — le port est fermé depuis l'extérieur. Le runner ne fait que
du SSH. Chaque couple dépôt / environnement a son répertoire distant.

`pull-secret` garantit la présence du Secret de tirage dans le namespace avant
de déployer, en recopiant celui d'un namespace voisin s'il manque. Sans lui, un
pod reste en `ImagePullBackOff`, `helm --atomic` annule tout, et le message
d'échec parle d'un délai dépassé sans jamais nommer la cause.

## `release-rust.yml`

```yaml
on:
  push:
    tags: ["v*"]
jobs:
  release:
    uses: samflix-mc/.github/.github/workflows/release-rust.yml@main
    with:
      binary: mc-pack
```

Windows, macOS Intel et Apple Silicon, Linux x86_64 et arm64, avec empreintes
SHA-256. Gratuit et illimité parce que le dépôt est public.

## `actions/ssh-k3s`

Pose la clé de déploiement, et exécute au besoin un script sur le nœud. Le
script part par l'entrée standard : il n'est jamais relu par le shell local,
donc aucune expansion imprévue, et sa longueur n'est pas limitée.

```yaml
- uses: samflix-mc/.github/.github/actions/ssh-k3s@main
  with:
    cle: ${{ secrets.SSH_PRIVATE_KEY }}
    hotes-connus: ${{ secrets.SSH_KNOWN_HOSTS }}
    utilisateur: ${{ secrets.SSH_USER }}
    hote: ${{ secrets.SSH_HOST }}
    port: ${{ secrets.SSH_PORT }}
    arguments: mc-dev
    script: |
      set -eu
      kubectl -n "$1" get pods
```

Sans `script`, l'action se contente de poser la clé et rend la commande `ssh`
complète en sortie (`commande`, `destination`), pour `rsync` ou un appel sur
mesure.

## Secrets

Les cinq secrets SSH sont définis au niveau de l'organisation. Sur le plan
gratuit ils ne sont **pas** accessibles aux dépôts privés, qui doivent avoir
leur propre copie (`gh secret set -R`).

`mc-content` a en plus un `DISPATCH_TOKEN` qui lui est propre, pour prévenir
ses consommateurs qu'une image de contenu est publiée. Un secret
d'organisation n'aurait pas convenu, pour la même raison.
