# Creating Secrets for a Cluster
When creating a new cluster the following steps should be performed to setup the required secrets to configure the cluster as well as provide a space for secrets that get generated during the bootstrapping process. While these secrets arent necessary for day to day operation they are necessary for executing plays against the cluster so must be persisted in some form on the Machine executing the Ansible plays.

**Note:** 
- ***Secret configuration never reaches github as folders below the secrets/ directory are purposfully ignored. When generating on the shared Ansible controller the secrets are stored on a network share which is symlinked into this repositories folder as secrets/production and secrets/staging.***
- ***When executing from a development workstation against production or staging environments it is advisable to mount the network share at ```\\somenetworkshare\``` and 
symlink the production and staging folders into the projects secrets/ folder so that secrets get persisted to the network store automatically.***

## Generating the Secrets for a New Cluster
- Navigate to secrets/create folder on the ansible server machine
- RUN ```./build_secrets.sh --env={your_environment_name} --dc={your_datacenter_name}```
  eg:```./build_secrets.sh --env=development --dc=test-dc```


### Secrets folder structure
```
- env_name
    -dc_name
        - account_credentials/
            # credentials for mounting the app folder samba share
            - app_folder_smb_credentials.json
            # Docker hub credentials for cluster docker access
            - docker_hub_credentials.json
            # ssh key for github
            - github_ssh_credentials.json
        - certificates/
            # Root certificate signing request
            - cluster-ca.csr
            # Root certificate key
            - cluster-ca-key.pem
            # Root certificate
            - cluster-ca.pem
            - consul/
                # consul cli signing request
                - consul-cli.csr
                # consul cli certificate key
                - consul-cli-key.pem
                # consul cli certificate
                - consul-cli.pem
            - nomad/
                # nomad cli signing request
                - nomad-cli.csr
                # nomad cli certificate key
                - nomad-cli-key.pem
                # nomad cli certificate
                - nomad-cli.pem
            - vault/
                # vault cli signing request
                - vault-cli.csr
                # vault cli certificate key
                - vault-cli-key.pem
                # vault cli certificate
                - vault-cli.pem
        - encryption_keys/
            - consul/
                # the master token for the acl datacenter
                - acl_datacenter_master_token.json
                # encryption key for consul serf communications
                - consul_serf_key.json
            - nomad/
                # encryption key for nomad serf communications
                - nomad_serf_key.json
        # Folder for secrets generated by the bootstrapping process
        - generated/
            # acl tokens that get generated and consumed by various steps in the bootstrapping process
            - acl_tokens/
            - vault_unseal_keys/
                # the keys used to unseal vault after initialization
                - unseal_keys.json
```

## Filling out account_credentials
Each file in the account credentials folder needs to be populated with any missing values. Additionally some values are set to defaults which can be updated if needed.
The account credentials_secrets are used to house secret configuration for services consumed by the cluster during the init boostrapping operations as well as during node or cluster update procedures. Additionally various account credential values are configured onto the machine for access to services used by the general runtime operation of the cluster.