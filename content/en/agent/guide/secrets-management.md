---
title: Secrets Management
kind: documentation
further_reading:
- link: "agent/autodiscovery"
  tag: "Documentation"
  text: "Autodiscovery"
- link: "https://github.com/DataDog/datadog-agent/blob/master/docs/agent/secrets.md#troubleshooting"
  tag: "github"
  text: "Secrets Troubleshooting"
---

If you wish to avoid storing secrets in plaintext in the Agent’s configuration files, you can use the secrets management package.

The Agent is able to leverage the `secrets` package to call a user-provided executable to handle retrieval and decryption of secrets, which are then loaded in memory by the Agent. This approach allows users to rely on any secrets management backend (such as HashiCorp Vault or AWS Secrets Manager), and select their preferred authentication method to establish initial trust with it.

Starting with version 6.12, the secrets management package is generally available on Linux for metrics, APM, and process monitoring, as well as on Windows for metrics and APM.

## Using secrets

### Defining secrets in configurations

Use the `ENC[]` notation to denote a secret as the value of any YAML field in your configuration.

Secrets are supported in any configuration backend (e.g. file, etcd, consul) and environment variables.

Secrets are also supported in `datadog.yaml`. The agent first loads the main configuration and reloads it after decrypting the secrets. This means that secrets cannot be used in the `secret_*` settings.

Secrets are always strings, i.e. you cannot use them to set an integer or Boolean value.

Example:

```yaml
instances:
  - server: db_prod
    # two valid secret handles
    user: "ENC[db_prod_user]"
    password: "ENC[db_prod_password]"

    # The `ENC[]` handle must be the entire YAML value, which means that
    # the following is NOT detected as a secret handle:
    password2: "db-ENC[prod_password]"
```

Here, there are two secrets: `db_prod_user` and `db_prod_password`. These are the secrets’ _handles_, and each uniquely identifies a secret within your secrets management backend.

Between the brackets, any character is allowed as long as the YAML configuration is valid. This means that quotes must be escaped. For instance:

```
"ENC[{\"env\": \"prod\", \"check\": \"postgres\", \"id\": \"user_password\"}]"
```

In the above example, the secret’s handle is the string `{"env": "prod", "check": "postgres", "id": "user_password"}`.

There is no need to escape inner `[` and `]`. For instance:

```
“ENC[user_array[1234]]”
```

In the above example, the secret’s handle is the string `user_array[1234]`.

Secrets are resolved after [Autodiscovery][1] template variables are resolved, i.e. you can use them in a secret handle. For instance:

```
“ENC[db_prod_password_%%host%%]”
```

### Providing an executable

To retrieve secrets, you must provide an executable that is able to authenticate to and fetch secrets from your secrets management backend.

The Agent caches secrets internally in memory to reduce the number of calls (useful in a containerized environment for example). The Agent calls the executable every time it accesses a check configuration file that contains at least one secret handle for which the secret is not already loaded in memory. In particular, secrets that have already been loaded in memory do not trigger additional calls to the executable. In practice, this means that the Agent calls the user-provided executable once per file that contains a secret handle at startup, and might make additional calls to the executable later if the Agent or instance is restarted, or if the Agent dynamically loads a new check containing a secret handle (e.g. via Autodiscovery).

Since APM and Process Monitoring run in their own process/service, and since processes don't share memory, each needs to be able to load/decrypt secrets. Thus, if `datadog.yaml` contains secrets, each process might call the executable once. For example, storing the `api_key` as a secret in the `datadog.yaml` file with APM and Process Monitoring enabled might result in 3 calls to the secret backend.

By design, the user-provided executable needs to implement any error handling mechanism that a user might require. Conversely, the Agent needs to be restarted if a secret has to be refreshed in memory (e.g. revoked password).

#### Configuration

Set the following variable in `datadog.yaml`:

```
secret_backend_command: <EXECUTABLE_PATH>
```

#### Agent security requirements

The Agent runs the `secret_backend_command` executable as a sub-process. The execution patterns differ on Linux and Windows.


{{< tabs >}}
{{% tab "Linux" %}}

On Linux, the executable set as `secret_backend_command` must:

* Belong to the same user running the Agent (`dd-agent` by default, or `root` inside a container).
* Have no rights for group or other.
* Have at least exec rights for the owner.

{{% /tab %}}
{{% tab "Windows" %}}

On Windows, the executable set as `secret_backend_command` must:

* Have read/exec for `ddagentuser` (the user used to run the Agent).
* Have no rights for any user or group except `Administrator` or `LocalSystem`.
* Be a valid Win32 application so the Agent can execute it.

{{% /tab %}}
{{< /tabs >}}

**Note**: The executable shares the same environment variables as the Agent.

Never output sensitive information on `stderr`. If the binary exits with a different status code than `0`, the Agent logs the standard error output of the executable to ease troubleshooting.

#### The executable API

The executable respects a simple API: it reads JSON from the standard input and outputs JSON containing the decrypted secrets to the standard output.

If the exit code of the executable is anything other than `0`, the integration configuration currently being decrypted is considered erroneous and is dropped.

**Input**:

The executable receives a JSON payload from the standard input, containing the list of secrets to fetch:

```
{
  "version": "1.0",
  "secrets": ["secret1", "secret2"]
}
```

* `version`: is a string containing the format version (currently 1.0).
* `secrets`: is a list of strings; each string is a handle from a configuration corresponding to a secret to fetch.

**Output**:

The executable is expected to output to the standard output a JSON payload containing the fetched secrets:

```
{
  "secret1": {
    "value": "secret_value",
    "error": null
  },
  "secret2": {
    "value": null,
    "error": "could not fetch the secret"
  }
}
```

The expected payload is a JSON object, where each key is one of the handles requested in the input payload. The value for each handle is a JSON object with 2 fields:

* `value`: a string; the actual secret value to be used in the check configurations (can be null in the case of error).
* `error`: a string; the error message, if needed. If error is anything other than null, the integration configuration that uses this handle is considered erroneous and is dropped.

**Example**:

The following is a dummy Go program prefixing every secret with `decrypted_`:

```go
package main

import (
  "encoding/json"
  "fmt"
  "io/ioutil"
  "os"
)

type secretsPayload struct {
  Secrets []string `json:secrets`
  Version int      `json:version`
}

func main() {
  data, err := ioutil.ReadAll(os.Stdin)

  if err != nil {
    fmt.Fprintf(os.Stderr, "Could not read from stdin: %s", err)
    os.Exit(1)
  }
  secrets := secretsPayload{}
  json.Unmarshal(data, &secrets)

  res := map[string]map[string]string{}
  for _, handle := range secrets.Secrets {
    res[handle] = map[string]string{
      "value": "decrypted_" + handle,
    }
  }

  output, err := json.Marshal(res)
  if err != nil {
    fmt.Fprintf(os.Stderr, "could not serialize res: %s", err)
    os.Exit(1)
  }
  fmt.Printf(string(output))
}
```

This updates this configuration (in the check file):

```yaml
instances:
  - server: db_prod
    user: ENC[db_prod_user]
    password: ENC[db_prod_password]
```

to this (in the Agent's memory):

```yaml
instances:
  - server: db_prod
    user: decrypted_db_prod_user
    password: decrypted_db_prod_password
```

## Troubleshooting

### Listing detected secrets

The `secret` command in the Agent CLI shows any errors related to your setup. For example, if the rights on the executable are incorrect. It also lists all handles found, and where they are located.

On Linux, the command outputs file mode, owner and group for the executable. On Windows, ACL rights are listed.

Example on Linux:

```shell
$> datadog-agent secret
=== Checking executable rights ===
Executable path: /path/to/you/executable
Check Rights: OK, the executable has the correct rights

Rights Detail:
file mode: 100700
Owner username: dd-agent
Group name: dd-agent

=== Secrets stats ===
Number of secrets decrypted: 3
Secrets handle decrypted:
- api_key: from datadog.yaml
- db_prod_user: from postgres.yaml
- db_prod_password: from postgres.yaml
```

Example on Windows (from an Administrator Powershell):
```
PS C:\> & 'C:\Program Files\Datadog\Datadog Agent\embedded\agent.exe' secret
=== Checking executable rights ===
Executable path: C:\path\to\you\executable.exe
Check Rights: OK, the executable has the correct rights

Rights Detail:
Acl list:
stdout:


Path   : Microsoft.PowerShell.Core\FileSystem::C:\path\to\you\executable.exe
Owner  : BUILTIN\Administrators
Group  : WIN-ITODMBAT8RG\None
Access : NT AUTHORITY\SYSTEM Allow  FullControl
         BUILTIN\Administrators Allow  FullControl
         WIN-ITODMBAT8RG\ddagentuser Allow  ReadAndExecute, Synchronize
Audit  :
Sddl   : O:BAG:S-1-5-21-2685101404-2783901971-939297808-513D:PAI(A;;FA;;;SY)(A;;FA;;;BA)(A;;0x1200
         a9;;;S-1-5-21-2685101404-2783901971-939297808-1001)

=== Secrets stats ===
Number of secrets decrypted: 3
Secrets handle decrypted:
- api_key: from datadog.yaml
- db_prod_user: from sqlserver.yaml
- db_prod_password: from sqlserver.yaml
```

### Seeing configurations after secrets were injected

To quickly see how the check's configurations are resolved, you can use the `configcheck` command :

```shell
sudo -u dd-agent -- datadog-agent configcheck

=== a check ===
Source: File Configuration Provider
Instance 1:
host: <decrypted_host>
port: <decrypted_port>
password: <decrypted_password>
~
===

=== another check ===
Source: File Configuration Provider
Instance 1:
host: <decrypted_host2>
port: <decrypted_port2>
password: <decrypted_password2>
~
===
```

**Note**: The Agent needs to be [restarted][3] to pick up changes on configuration files.

### Debugging your secret_backend_command

To test or debug outside of the Agent, you can mimic how the Agent runs it:

#### Linux

```bash
sudo su dd-agent - bash -c "echo '{\"version\": \"1.0\", \"secrets\": [\"secret1\", \"secret2\"]}' | /path/to/the/secret_backend_command"
```

The `dd-agent` user is created when you install the Datadog Agent.

#### Windows

##### Rights related errors

If you encounter one of the following errors, then something is missing in your setup. See the [Windows intructions](#windows).

1. If any other group or user than needed has rights on the executable, a similar error to the following is logged:
   ```
   error while decrypting secrets in an instance: Invalid executable 'C:\decrypt.exe': other users/groups than LOCAL_SYSTEM, Administrators or ddagentuser have rights on it
   ```

2. If `ddagentuser` doesn't have read and execute right on the file, a similar error logged:
   ```
   error while decrypting secrets in an instance: could not query ACLs for C:\decrypt.exe
   ```

3. Your executable needs to be a valid Win32 application. If not, the following error is logged:
   ```
   error while running 'C:\decrypt.py': fork/exec C:\decrypt.py: %1 is not a valid Win32 application.
   ```

##### Testing your executable

Your executable is executed by the Agent when fetching your secrets. The Datadog Agent runs using the `ddagentuser`. This user has no specific rights, but it is part of the `Performance Monitor Users` group. The password for this user is randomly generated at install time and is never saved anywhere.

This means that your executable might work with your default user or development user—but not when it's run by the Agent, since `ddagentuser` has more restricted rights.

To test your executable in the same conditions as the Agent, update the password of the `ddagentuser` on your dev box. This way, you can authenticate as `ddagentuser` and run your executable in the same context the Agent would.

To do so, follow those steps:

1. Remove `ddagentuser` from the `Local Policies/User Rights Assignement/Deny Log on locally` list in the `Local Security Policy`.
2. Set a new password for `ddagentuser` (since the one generated at install time is never saved anywhere). In Powershell, run:
  ```powershell
  $user = [ADSI]"WinNT://./ddagentuser";
  $user.SetPassword("a_new_password")
  ```

3. Update the password to be used by `DatadogAgent` service in the Service Control Manager. In Powershell, run:
  ```powershell
  sc.exe config DatadogAgent password= "a_new_password"
  ```

You can now login as `ddagentuser` to test your executable. Datadog has a [Powershell script][2] to help you test your
executable as another user. It switches user contexts and mimics how the Agent runs your executable.

Example on how to use it:

```
.\secrets_tester.ps1 -user ddagentuser -password a_new_password -executable C:\path\to\your\executable.exe -payload '{"version": "1.0", "secrets": ["secret_ID_1", "secret_ID_2"]}'
Creating new Process with C:\path\to\your\executable.exe
Waiting a second for the process to be up and running
Writing the payload to Stdin
Waiting a second so the process can fetch the secrets
stdout:
{"secret_ID_1":{"value":"secret1"},"secret_ID_2":{"value":"secret2"}}
stderr: None
exit code:
0
```

### Agent refusing to start

The first thing the Agent does on startup is to load `datadog.yaml` and decrypt any secrets in it. This is done before setting up the logging. This means that on platforms like Windows, errors occurring when loading `datadog.yaml` aren't written in the logs, but on `stderr`. This can occur when the executable given to the Agent for secrets returns an error.

If you have secrets in `datadog.yaml` and the Agent refuses to start: 

* Try to start the Agent manually to be able to see `stderr`.
* Remove the secrets from `datadog.yaml` and test with secrets in a check configuration file first.

## Further Reading

{{< partial name="whats-next/whats-next.html" >}}

[1]: /agent/autodiscovery
[2]: https://github.com/DataDog/datadog-agent/blob/master/docs/agent/secrets_scripts/secrets_tester.ps1
[3]: /agent/guide/agent-commands/?tab=agentv6#restart-the-agent
