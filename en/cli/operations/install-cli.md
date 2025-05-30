# CLI installation

You can install the CLI in different modes: 
- [Interactive CLI installation](#interactive)
- [Non-interactive CLI installation](#non-interactive)

## Interactive CLI installation {#interactive}

{% include [install-cli](../../_includes/cli/install-cli.md) %}

To use the CLI, [create a profile](profile/profile-create.md).

## Non-interactive CLI installation {#non-interactive}

To use the CLI in scripts, you can use flags for a non-interactive installation:

{% list tabs group=programming_language %}

- Bash {#bash}

    Run this command:

    ```bash
    curl https://{{ s3-storage-host-cli }}{{ yc-install-path }} | bash -s -- -h
    Usage: install [options...]
    Options:
     -i [INSTALL_DIR]    Installs to specified dir.
     -r [RC_FILE]        Automatically modify RC_FILE with PATH modification and shell completion.
     -n                  Don't modify rc file and don't ask about it.
     -a                  Automatically modify default rc file with PATH modification and shell completion.
     -h                  Prints help.
    ```

    Example of use:
    - Installing the CLI to `/opt/yc` without changing the `.bashrc` file:

        ```bash
        curl https://{{ s3-storage-host-cli }}{{ yc-install-path }} | \
            bash -s -- -i /opt/yc -n
        ```

    - Installing the CLI to the default directory with `completion` and `PATH` added to `.bashrc`:

        ```bash
        curl https://{{ s3-storage-host-cli }}{{ yc-install-path }} | \
            bash -s -- -a
        ```

{% endlist %}

## See also {#see-also}

* [{#T}](update-cli.md)