vault_agent
=========

This playbook installs and configures the [Vault Agent Auto-Auth](https://www.vaultproject.io/docs/agent/autoauth/index.html) feature. 

Security features:
* Systemd service limits read/write access to the token target path only
* Service user can be added to vault group to get access to the Vault token. Users not in the vault group cannot access the Vault token.
* Auto-auth polling interval configurable (default is 60 seconds)
* Checksum validation of Vault ZIP download
* Others

Requirements
------------

You should have Ruby installed on the target machine. This is required for use of the [Vault Token Helper](https://www.vaultproject.io/docs/commands/token-helper.html).

Role Variables
--------------

---
| variable                             | description                                                                                                              | default                            | required |
|--------------------------------------|--------------------------------------------------------------------------------------------------------------------------|------------------------------------|----------|
| vault_addr                           | Vault Server address                                                                                                     | https://vault.example.com          | yes      |
| vault_skip_verify                    | True to skip TLS Verification                                                                                            | false                              | no       |
| app_service_user                     | OS Service user that runs your app process                                                                               |                                    | no       |
| vault_version                        | The release version of Vault                                                                                             | 1.0.1                              | no       |
| vault_pid_file                       | PID File location                                                                                                        | /opt/vault/var/run/vault-agent.pid | no       |
| vault_user                           | User of the vault-agent process.                                                                                         | vault                              | no       |
| vault_group                          | Group of the vault-agent process                                                                                         | vault                              | no       |
| vault_binary_path                    | Location of the Vault binary                                                                                             | /opt/vault/vault                   | no       |
| vault_pid                            | Vault process ID number                                                                                                  | 2947                               | no       |
| vault_log_path                       | Log file location by default                                                                                             | /opt/vault/log                     | no       |
| vault_environment_variable_additions | Add values to this in a multi-line variable to provide additional env vars to the vault process. For example, HTTP_PROXY |                                    | no       |
| config_file_path                     | Path of the Vault Agent config file                                                                                      | /opt/vault/agent/vault-agent.hcl   | no       |
| vault_token_path                     | Path to store the .vault-token file, instead of ~/.vault-token                                                           | /opt/vault/token/.vault-token      | no       |
| vault_environment_file               | EnvironmentFile for the Vault Agent                                                                                      | /opt/vault/etc/vault.d/vault.sh    | no       |
| auth_type                            | Vault Agent Auto-Auth method                                                                                             | iam                                | no       |
| credential_poll_interval             | Interval, in seconds, for polling the Vault server for new creds                                                         | 60                                 | no       |
| iam_server_id_header_value           |  Value for X-Vault-AWS-IAM-Server-ID header                                                                              | vault.example.com                  | no       |
| vault_cacert                         |                                                                                                                          | /opt/vault/tls/vault-ca.crt        | no       |
| vault_ca_file                        |                                                                                                                          | /opt/vault/tls/ca.crt              | no       |
| ca_certificates_update               |                                                                                                                          | /usr/bin/update-ca-trust extract   | no       |
| ca_trust_path                        |                                                                                                                          | /etc/pki/ca-trust/source/anchors   |          |


Dependencies
------------

A list of other roles hosted on Galaxy should go here, plus any details in regards to parameters that may need to be set for other roles, or variables that are used from other roles.

Example Playbook
----------------

Including an example of how to use your role (for instance, with variables passed in as parameters) is always nice for users too:

    - hosts: kinnaird
      roles:
         - role: kmcquade.vault_agent
           vars:
             vault_role: "kinnaird"
             vault_addr: "https://vault.example.com"

Note: For the Vault client to authenticate to Vault via IAM Role, you need to ensure the following:

* Network connectivity to Vault
* AWS IAM Role created for the Vault client
* Vault server is able to perform the necessary AWS API queries to verify the Vault client's IAM ARN. Required permissions are listed [here](https://www.vaultproject.io/docs/auth/aws.html#recommended-vault-iam-policy).
* You can perform the following with these resources in the Vault Terraform provider: 
  * [vault_aws_auth_backend_role](https://www.terraform.io/docs/providers/vault/r/aws_auth_backend_role.html): Creates the in-vault role for the Vault client (which is identified by the client's IAM Role ARN)
  * [vault_aws_auth_backend_sts_role](https://www.terraform.io/docs/providers/vault/r/aws_auth_backend_sts_role.html): If you need to perform these queries via cross account access, fill out the details for the Vault Server's child role (parent account, where Vault resides, AssumeRole down to child account).


License
-------

BSD

Author Information
------------------

This role was created by [Kinnaird McQuade](https://github.com/kmcquade) in December 2018.