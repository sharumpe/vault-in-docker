# Demo Vault Server via Docker

This is intended to run Vault server without having to set everything up on a local workstation.  This isn't intended for production use at this time.

## Prerequisites

You'll need the following installed locally:

* [Docker](https://docs.docker.com/install/#supported-platforms) (to run the containers)
* [Vault](https://www.vaultproject.io/docs/install/) (to interact with the vault)

## Start things up

Start the consul and vault containers so that everything is running.

```bash
docker-compose up -d
```

## Set up the environment

This will let you talk to the `vault` instance in the container from your workstation.

```bash
export VAULT_ADDR=http://127.0.0.1:9200
```

## Initialize the a vault

Initialize the vault as usual.

```bash
vault operator init
```

You'll get the shards and root token in the output.  Make sure to save those.

## Add the token to the environment

One final piece to make sure you can use the API calls.

```bash
export VAULT_TOKEN=<Initial Root Token>
```

## Unseal the vault

Now you're ready to unseal the vault for future use.

```bash
vault operator unseal
```

You'll be prompted with:

```text
Unseal Key (will be hidden):
```

Paste in any one of the Unseal Keys. You'll see something similar to the following:

```text
Key                Value
---                -----
Seal Type          shamir
Initialized        true
Sealed             true
Total Shares       5
Threshold          3
Unseal Progress    1/3
Unseal Nonce       2b50eb5d-0f4c-0999-0e68-9cbac819502d
Version            1.0.2
HA Enabled         true
```

Notice `Unseal Progress 1/3`.  This means you need to unseal using two more Unseal Keys.

Once you've done that, you'll see something similar to the following:

```text
Key                    Value
---                    -----
Seal Type              shamir
Initialized            true
Sealed                 false
Total Shares           5
Threshold              3
Version                1.0.2
Cluster Name           vault-cluster-f9e69a2e
Cluster ID             ebb4476c-9a66-d4bb-8a40-400c30b233cb
HA Enabled             true
HA Cluster             n/a
HA Mode                standby
Active Node Address    <none>
```

The `Sealed false` line means you've got a fully-unsealed and usable vault!

## Demo because: demos

You can now write and read to this vault:

```text
$ vault write secret/foo bar=baz
Success! Data written to: secret/foo

$ vault read secret/foo
Key                 Value
---                 -----
refresh_interval    768h
bar                 baz
```
