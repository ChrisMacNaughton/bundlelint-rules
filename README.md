# BundleLint Rules

These rules are a default set of rules that are used by [`bundle-lint`](https://github.com/ChrisMacNaughton/bundle-lint) to validate that bundles are correct. These rules teach `bundle-lint` to understand a bundle.

## Usage

The format of these rules support requirements for charm relations or configuration. For example:

```yaml
- charm_name: ceph-osd
  relations:
    - requires:
      - - ceph-mon
        - ceph-osd
```

The above snippet states that the ceph-osd charm requires a relation between the ceph-osd application and the ceph-mon application.

```yaml
- charm_name: neutron-openvswitch
  config:
    - config_name: use-dvr-snat
      config_value: true
      requires:
        neutron-api:
          - name: enable-dvr
            value: true
        neutron-openvswitch:
          - name: bridge-mappings
          - name: enable-local-dhcp-and-metadata
            value: true
```

The above snippet requires that, when neutron-openvswitch has `use-dvr-snat` enabled, it also requires `bridge-mappings`, `enable-local-dhcp-and-metadata` on itself, as well as `enable-dvr` on the neutron-api charm.

These can be combined in interesting ways; for example, expanding on the above example:

```yaml
- charm_name: neutron-openvswitch
  config:
    - config_name: use-dvr-snat
      config_value: true
      requires:
        neutron-api:
          - name: enable-dvr
            value: true
        neutron-openvswitch:
          - name: bridge-mappings
          - name: enable-local-dhcp-and-metadata
            value: true
  relations:
    - config:
        name: enable-dvr
        value: true
      requires:
        - - 'neutron-openvswitch:neutron-plugin-api'
          - 'neutron-api:neutron-plugin-api'
```

This update expands to include the required (new) relation between neutron-api and neutron-openvswitch, which is not a required relation otherwise, when `enable-dvr` is `true`!