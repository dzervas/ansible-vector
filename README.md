![publish](https://github.com/dzervas/ansible-vector/workflows/publish/badge.svg)

# Vector ansible role

This is an ansible role to set up [vector](https://vector.dev).
It translates the YAML configuration to TOML, so any configuration is possible.

Currently only amd64, arch64, arch7 through deb and rpm packages are supported

## Variables

| Variable                                   | Required | Default                | Description
|--------------------------------------------|----------|------------------------|------------
| vector_template | yes | vector.toml.j2 | path of your vector.toml template
| vector_config_file | yes | /etc/vector/vector.toml | system path of your vector.toml configuration
| vector_nightly | no | false | use vector nightly build
| add_vector_docker_group | no | false | add user vector to group docker
| add_vector_journal_group | no | false | add user vector to group systemd-journal
| add_vector_journal_group | no | false | add user vector to group systemd-journal
| vector_install_from_repo | no | false | whether to install vector from packages or install from deb or redhat based repositories
| sources | yes | false | ingest observability data from a wide variety of targets [link](https://vector.dev/docs/reference/configuration/sources/)
| transforms | no | false | shape your data as it moves through your Vector topology [link](https://vector.dev/docs/reference/configuration/transforms/)
| sinks | yes | false | deliver your observability data to a variety of destinations [link](https://vector.dev/docs/reference/configuration/sinks/)

## Example for configuration with ansible
```yaml
sources:
  journald:
    type: journald
    current_boot_only: true

transforms:
  grok:
    type: grok_parser
    inputs:
      - journald
    pattern: '(?<capture>\\d+)%{GREEDYDATA}'

sinks:
  vector:
    type: vector
    inputs: ["journald"]
    address: "vector.example.com:9000"
```

## Example playbook
```yaml
- name: install and configure vector
  hosts: all
  vars:
    sources:
      journald:
        type: journald
        current_boot_only: true
    transforms:
      grok:
        type: grok_parser
        inputs:
          - journald
        pattern: '(?<capture>\\d+)%{GREEDYDATA}'
    sinks:
      vector:
        type: vector
        inputs: ["journald"]
        address: "vector.example.com:9000"
  roles:
    - vector
```
