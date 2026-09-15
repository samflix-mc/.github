# .github

Workflows réutilisables et profil de l'organisation.

## `deploy-helm.yml`

Déploie un chart Helm sur le cluster K3s de samflix. Le runner ne fait que du
SSH : `helm` s'exécute sur le serveur, dont le kubeconfig K3s pointe sur
`127.0.0.1:6443` — le port 6443 est volontairement fermé depuis l'extérieur.

```yaml
jobs:
  deploy:
    uses: samflix-mc/.github/.github/workflows/deploy-helm.yml@main
    with:
      chart-path: chart
      release: mon-service
      namespace: mc-dev
      environment: dev
      verify-url: https://dev.exemple.ggy.info
    secrets: inherit
```

Chaque couple dépôt / environnement a son propre répertoire distant, donc les
trois environnements cohabitent sans se marcher dessus.

## `release-rust.yml`

Compile un binaire Rust pour Windows, macOS (Intel et Apple Silicon) et Linux
(x86_64 et arm64), puis publie une release avec les empreintes SHA-256.

```yaml
on:
  push:
    tags: ["v*"]
jobs:
  release:
    uses: samflix-mc/.github/.github/workflows/release-rust.yml@main
    with:
      binary: mc-auth
```

## Secrets

Les cinq secrets SSH sont définis **au niveau de l'organisation**, visibilité
« tous les dépôts ». Attention : sur le plan gratuit, les secrets
d'organisation ne sont **pas** accessibles aux dépôts privés — ceux-là doivent
avoir leur propre copie, posée avec `gh secret set -R`.
