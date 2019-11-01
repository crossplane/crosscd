# CrossCD

CrossCD is an infrastructure configuration library that allows you to bootstrap
entire environments for provisioning resources across clouds from a Kubernetes
cluster.

## Full

The easiest way to get started with a full multi-environment, multi-cloud
infrastructure setup is to run:

```bash
kubectl apply -k .
```

This will create `dev`, `staging`, and `prod` environments, populated with a
class from each provider for all resource types it supports.

## Quick Start

If you do not want full environment configuration and are just looking to try
out Crossplane, you can install a multi-cloud `dev` environment with the
following command:

```bash
kubectl apply -k ./classpacks/quickstart
```

This will create a `dev` environment, populated with a class for each provider
for all resource types it supports. It will also make `AWS` resources the
`default` option when you create unopinionated claims.

## Further Customization

Every bottom-level directory in each `{provider-name}/services` provides its own
`kustomization.yaml file` that specifies the resources present and also labels
them with the provider name. In [kustomize] language this is referred to as a
[base]. All other `kustomization.yaml` files are an [overlays]. Overlays
reference bases and apply additional configuration or [patches], which allow you
to replace parts of the base-level resources.

Customizing can take the form of either modifying bases or creating and
combining new overlays. For instance, when you run `kubectl apply -k .` in the
top-level directory of this repository, the `kustomization.yaml` is an overlay
for each of the provider's
`{provider-name}/environments/full/kustomization.yaml`, which is an overlay for
each of the `kustomization.yaml` files in the `dev`, `stage`, and `prod`
directories, which in turn are overlays for the
`{provider-name}/services/kustomization.yaml`, which is itself an overlay for
each type's `kustomization.yaml` (which are the base files we mentioned
earlier). This can be somewhat confusing, but it is always safe to start out
with the base files and then build up one layer at a time.

For examples on how to combine overlays effectively, take a look in
[classpacks/](./classpacks)!


<!-- Named Links -->
[kustomize]: https://github.com/kubernetes-sigs/kustomize
[base]: https://github.com/kubernetes-sigs/kustomize/blob/master/docs/glossary.md#base
[overlays]: https://github.com/kubernetes-sigs/kustomize/blob/master/docs/glossary.md#overlay
[patches]: https://github.com/kubernetes-sigs/kustomize/blob/master/docs/glossary.md#patch