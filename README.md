# GCI Update Resource

A Concourse CI resource to check for new Google Compute Images (GCI).

Special thanks to [Jeff Waugh](https://github.com/jdub) for creating the [AMI Update Resource](https://github.com/jdub/ami-resource) on which this is largely based. 


## Source Configuration

- `key`: *Required.* JSON string containing the credentials for a service account that can query information about Google Compute Images.

- `family_project`: *Required.* GCP project which owns the image family you're tracking. 

- `family`: *Required.* The image family you're tracking.

## Behaviour

### `check`: Check for new GCI in the provided family

Searches for the latest GCI that is in the provided family. The GCI name serves as the resulting version.

#### Parameters

*None.*

### `in`: Fetch the description of a GCI

Places the following files in the destination:

- `output.json`: The complete GCI description object in JSON format. 

- `name`: A plain text file containing the AMI ID, e.g. `ami-5731123e`

- `packer.json`: The AMI ID in Packer `var-file` input format, typically for use with [packer](https://packer.io), e.g.

  ```json
  {"source_image": "debian-7-wheezy-v20150127"}
  ```

#### Parameters

*None.*

## Example

This pipeline will check for a new Ubuntu 18.04 LTS GCI every hour, triggering the next step of the build plan if it finds one.

```yaml
resource_types:
- name: gci
  type: docker-image
  source:
    repository: smgoller/gci-resource

resources:
- name: ubuntu-gci
  type: gci
  check_every: 1h
  source:
    key: "..."
    family_project: ubuntu-os-cloud
    family: ubuntu-1804-lts

jobs:
- name: my-gci
  plan:
  - get: ubuntu-gci
    trigger: true
  - task: build-fresh-image
    ...
```
