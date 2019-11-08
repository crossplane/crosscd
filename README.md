# CrossCD

CrossCD is an infrastructure configuration library that allows you to bootstrap
entire environments for provisioning resources across clouds from a Kubernetes
cluster.

## Quick Start

If you do not want full environment configuration and are just looking to try
out Crossplane, you can install a multi-cloud `dev` environment with the
following command:

```bash
kubectl apply -k ./classpacks/quickstart
```

This will create a `dev` environment, populated with a class for each provider
for all resource types that support dynamic provisioning. It will also make
`AWS` resources the `default` option when you create unopinionated claims. Note
that this does not statically provision any managed resources, so types like the
GCP `Network` are not included because they are not currently supported by
dynamic provisioning.

## Further Customization

Every bottom-level directory in each `{provider-name}/services` provides its own
`kustomization.yaml file` that specifies the resources present and also labels
them with the provider name. In [kustomize] language this is referred to as a
[base]. All other `kustomization.yaml` files are [overlays]. Overlays reference
bases and apply additional configuration or [patches], which allow you to
replace parts of the base-level resources. In this repository, each base
provides all *required* fields for validation, but may not include every field
required for successful provisioning. For example, fields that require one of
two fields have both omitted in the base, such that overlays do not have to
delete fields.

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

## Naming

When looking at bases in this repository, files with managed resources are named
`resource.yaml`. If you apply a base of this type in your Crossplane cluster, it
will lead to the attempted provisioning of an external resource. Files named
`class.yaml` denote resource classes, which provide configuration for
provisioning external resources, but will not lead to actual provisioning until
a compatible claim is created.

<!-- Named Links -->
[kustomize]: https://github.com/kubernetes-sigs/kustomize
[base]: https://github.com/kubernetes-sigs/kustomize/blob/master/docs/glossary.md#base
[overlays]: https://github.com/kubernetes-sigs/kustomize/blob/master/docs/glossary.md#overlay
[patches]: https://github.com/kubernetes-sigs/kustomize/blob/master/docs/glossary.md#patch