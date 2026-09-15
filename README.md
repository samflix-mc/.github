# .github

Workflows réutilisables et profil de l'organisation.

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
    secrets: inherit
```

`helm` s'exécute sur le serveur, dont le kubeconfig K3s pointe sur
`127.0.0.1:6443` — le port est fermé depuis l'extérieur. Le runner ne fait que
du SSH. Chaque couple dépôt / environnement a son répertoire distant.

## `release-rust.yml`

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

Windows, macOS Intel et Apple Silicon, Linux x86_64 et arm64, avec empreintes
SHA-256. Gratuit et illimité parce que le dépôt est public.

## Secrets

Les cinq secrets SSH sont définis au niveau de l'organisation. Sur le plan
gratuit ils ne sont **pas** accessibles aux dépôts privés, qui doivent avoir
leur propre copie (`gh secret set -R`).
