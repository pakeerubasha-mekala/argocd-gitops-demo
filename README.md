# argocd-gitops-demo

A minimal GitOps setup with [Argo CD](https://argo-cd.readthedocs.io): one **app-of-apps** root, a Kustomize base with
**dev** and **prod** overlays, and a sample app ([podinfo](https://github.com/stefanprodan/podinfo)).

Git is the source of truth. To change what runs in the cluster you change a file and merge; Argo CD does the rest.

## Layout

```
bootstrap/root-app.yaml                  # the one thing applied by hand
apps/                                    # Argo CD Applications, created and synced from Git
  podinfo-dev.yaml                       #   dev: auto-sync, prune and self-heal
  podinfo-prod.yaml                      #   prod: shows the diff, a human presses Sync
manifests/podinfo/
  base/                                  # Deployment + Service (hardened pod settings)
  overlays/dev/                          # namespace podinfo-dev, 1 replica
  overlays/prod/                         # namespace podinfo-prod, 3 replicas
```

## Try it

You need a cluster with Argo CD installed (for example `kind` plus the [Argo CD install manifests](https://argo-cd.readthedocs.io/en/stable/getting_started/)).

1. Fork this repo and replace `pakeerubasha-mekala` in the `repoURL` fields with your account.
2. `kubectl apply -f bootstrap/root-app.yaml`
3. Open the Argo CD UI: `podinfo-dev` syncs by itself; sync `podinfo-prod` by hand.

Preview the rendered manifests without a cluster:

```sh
kubectl kustomize manifests/podinfo/overlays/dev
kubectl kustomize manifests/podinfo/overlays/prod
```

## Ideas that this layout makes easy

- Promote a change from dev to prod by merging it into the base or the prod overlay.
- Add a new app: drop an `Application` file into `apps/`.
- Roll back: `git revert`.

## Status

The Kustomize overlays render cleanly with `kubectl kustomize`. The Argo CD `Application` files and the sync behaviour
have not been tested against a live cluster, so try it in a throwaway cluster first.

## License

MIT
