# talos-installer-zfs

Talos installer images with `siderolabs/zfs` + `containdk/talos-zpool-extension`,
plus `siderolabs/amd-ucode` and `siderolabs/nut-client` for the AMD Ryzen 7 5825U
backup node.

`factory.talos.dev` rejects custom extension refs (`field extensions not found in
type schematic.SystemExtensions`), so the installer is assembled locally with
`imager` and published to GHCR.

## Use

```sh
# boot the official v1.13.10 ISO in maintenance mode, then:
talosctl install --nodes <ip> \
  --image ghcr.io/alessio-pascolini/talos-installer-zfs:v1.13.10

# later upgrades
talosctl upgrade --nodes <ip> \
  --image ghcr.io/alessio-pascolini/talos-installer-zfs:v1.13.10
```

The GHCR package must be public for the nodes to pull it.

## Pins

`TALOS_VERSION`, `ZFS_EXT`, `ZPOOL_EXT`, `AMD_UCODE_EXT`, `NUT_EXT` live at the top
of `.github/workflows/build-installer.yaml`. Extension manifest compatibility must
cover the Talos version (all checked against v1.13.10):

| extension | pin | declares |
|---|---|---|
| zfs | `2.4.3-v1.13.10` | `talos >= v1.6.0` |
| zpool-creator | `0.8.1-v1.13` | `talos >= v1.13.0` |
| amd-ucode | `20261019` | `talos >= v1.0.0` |
| nut-client | `2.8.5` | `talos >= v1.5.0` |

The `v1.14` tags of the zpool extension declare `>= v1.14.0` and will not load
on 1.13.x.

`nut-client` also needs an `ExtensionServiceConfig` with the `upsmon.conf`
`MONITOR` line; that config lives outside this repo, with the node's machine
configuration.
