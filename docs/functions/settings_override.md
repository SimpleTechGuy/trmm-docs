# Overriding / Customizing Default Settings

### Browser token expiration

The default browser token expiration is set to 5 hours. See this [ticket](https://github.com/amidaware/tacticalrmm/issues/503) for reference.

To change it, add the following code block to the end of `/rmm/api/tacticalrmm/tacticalrmm/local_settings.py`

```python
from datetime import timedelta

REST_KNOX = {
    "TOKEN_TTL": timedelta(days=30),
    "AUTO_REFRESH": True,
    "MIN_REFRESH_INTERVAL": 600,
}
```

Change `(days=30)` to whatever you prefer. Then run `sudo systemctl restart rmm.service` for changes to take effect.

### Using your own wildcard SSL cert

This is only supported during initial install, not after you've already installed.

Follow the instructions in the [install guide](../install_server.md#step-5-run-the-install-script) for the `--use-own-cert` install flag.

### Use NATS Standard instead of NATS websocket

Prior to TRMM v0.14.0 (released 7/7/2022), agents NATS traffic connected to the TRMM server on public port 4222.
If you have upgraded to v0.14.0 and have agents that won't work with websockets for some reason (too old TLS etc) then you can do the following to use NATS standard TCP on port 4222, just like how it was before v0.14.0:

For Windows agents:

Add the following registry string value (REG_SZ):

`HKEY_LOCAL_MACHINE\SOFTWARE\TacticalRMM\NatsStandardPort` with value `4222`

Then restart the `tacticalrmm` Windows service.

For Linux agents:

Add the following key/value pair to `/etc/tacticalagent`:
```json
{"natsstandardport": "4222"}
```

Then `sudo systemctl restart tacticalagent.service`

Just make sure port 4222 TCP is still open in your firewall and you're done.

### Configuring Custom Temp Dirs on Windows Agents

*Version added: Tactical RMM v0.15.10*

*Requires Agent v2.4.7*

By default, the Windows agent utilizes the `C:\ProgramData\TacticalRMM` directory for executing scripts and managing agent updates. However, it is possible to override this default directory by setting two optional registry string values (`REG_SZ`), specifying full paths to the desired directories:

`HKEY_LOCAL_MACHINE\SOFTWARE\TacticalRMM\WinTmpDir`: This registry value is used for running scripts and handling agent updates. Provide the full path to the custom directory.

`HKEY_LOCAL_MACHINE\SOFTWARE\TacticalRMM\WinRunAsUserTmpDir`: This registry value is specifically for executing Run As User scripts. Provide the full path to the custom directory.

Please note that these custom directories must already exist on the system, as the agent will not attempt to create them. Ensure that the desired directories are created and that the appropriate permissions are set before adding the registry values.

*Directory path cannot contain spaces, this is a known issue and will be fixed in a future release.*

To apply the changes, restart the `tacticalrmm` Windows service. The custom temporary directories will then be used for the respective tasks.

Example:

![reg](../images/wintmpdir.png)

### Monitor NATS via its HTTP API

*Version added: Tactical RMM v0.15.1*



To enable [NATS monitoring](https://docs.nats.io/running-a-nats-service/configuration/monitoring), add the folllowing to `/rmm/api/tacticalrmm/tacticalrmm/local_settings.py` (replace with whatever port you want).
```python
NATS_HTTP_PORT = 8222
```

Then from the TRMM Web UI, do **Tools > Server Maintenance > Reload Nats Configuration**.

And then from your TRMM server cli restart both the `rmm.service` and `nats.service` services.

### Modify the Placeholder Text for the 'Send Command' Functionality

*Version added: Tactical RMM v0.15.12*

Users now have the flexibility to customize the placeholder text that is displayed in the 'Send Command' dialog. This customization can be achieved by defining any or all of the following three optional variables in `/rmm/api/tacticalrmm/tacticalrmm/local_settings.py` file:

```python
CMD_PLACEHOLDER_TEXT = "<Your customized command prompt text>"
POWERSHELL_PLACEHOLDER_TEXT = "<Your customized PowerShell prompt text>"
SHELL_PLACEHOLDER_TEXT = "<Your customized shell prompt text>"
```

To activate, restart the api with `sudo systemctl restart rmm` and then refresh the web interface.