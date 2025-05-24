base) guido@farty:/etc/systemd/system$ more jupyterlab.service 
[Unit]
Description=Jupyterlab
After=syslog.target network.target

[Service]
Type=simple
PIDFile=/run/jupyterlab.pid
# Step 1 and Step 2 details are here..
# ------------------------------------
User=guido
Group=guido
Environment="PATH=/bin:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/home/guido/anaconda3/bin"
ExecStart=source /home/guido/.bashrc && /home/guido/anaconda3/bin/jupyter lab --no-browser --port 8888 --config=/home/guido/.jupyter/jupyter_lab_config.py
WorkingDirectory=/home/guido/Dropbox/ipynb
Restart=always
RestartSec=10
LimitNOFILE=127759
LimitNPROC=127759
#KillMode=mixed

[Install]
WantedBy=multi-user.target


# create a config file
jupyter-lab --generate-config


To make JupyterLab accessible on the private network by binding it to the address `0.0.0.0`, you'll need to modify the JupyterLab configuration. Here's a step-by-step guide on how to achieve this:

1. **Install JupyterLab**: If you haven't already installed JupyterLab, you can install it using `pip`:

    ```sh
    pip install jupyterlab
    ```

2. **Generate a Jupyter configuration file**: If you don't already have a Jupyter configuration file, generate one using the following command:

    ```sh
    jupyter lab --generate-config
    ```

    This will create a `jupyter_lab_config.py` file in the Jupyter configuration directory, typically `~/.jupyter/`.

3. **Edit the configuration file**: Open the `jupyter_lab_config.py` file in a text editor.

    ```sh
    nano ~/.jupyter/jupyter_lab_config.py
    ```

4. **Modify the configuration**:

    Find the following line in the configuration file (if it exists):

    ```python
    #c.ServerApp.ip = 'localhost'
    ```

    Uncomment the line by removing the `#` at the beginning and change 'localhost' to '0.0.0.0':

    ```python
    c.ServerApp.ip = '0.0.0.0'
    ```

    This change tells JupyterLab to listen on all available network interfaces.

5. **(Optional) Specify a port**: By default, JupyterLab uses port `8888`. If you want to specify a different port, you can do so by setting the following configuration:

    ```python
    c.ServerApp.port = 8888
    ```

6. **Security considerations**: Exposing JupyterLab to all network interfaces can pose a security risk. It is highly recommended to protect your JupyterLab instance by setting up a password or token. Find and uncomment the `c.ServerApp.token` line to set an access token (or use an environment variable):

    ```python
    c.ServerApp.token = 'your_secure_token'
    ```

    Alternatively, you can prepare SSL encryption by setting paths to your SSL certificate and key:

    ```python
    c.ServerApp.certfile = '/path/to/your/certificate.pem'
    c.ServerApp.keyfile = '/path/to/your/key.pem'
    ```

By following these steps, you'll be able to access your JupyterLab instance from other machines on your local network. Always consider the security implications of exposing JupyterLab and take appropriate measures to secure it.


1. **Use the `jupyter server list` command** to get the output.
2. **Parse the output** to extract the token.

Here is a bash script that does this:

```bash
#!/bin/bash

# Get the list of currently running Jupyter servers
output=$(jupyter server list 2>/dev/null)

# Extract the token using grep and sed
token=$(echo "$output" | grep -oP 'token=\K\w+')

# Check if a token is found and output it
if [[ -n "$token" ]]; then
  echo "Token: $token"
else
  echo "No token found."
fi
```

### Explanation:

- **`jupyter server list 2>/dev/null`**: This runs the command to get the list of running servers. `2>/dev/null` suppresses any error messages.
- **`grep -oP 'token=\K\w+'`**: The `grep` command with `-oP` uses Perl-compatible regular expressions to find the pattern `token=` followed by the token itself. `\K` resets the starting point of the reported match and `\w+` matches the token, which consists of alphanumeric characters and underscores.
- **Check if a token was found and print it**.

### Running the Script:

1. Save the script to a file, for example, `get_jupyter_token.sh`.
2. Make the script executable:
   ```bash
   chmod +x get_jupyter_token.sh
   ```
3. Run the script:
   ```bash
   ./get_jupyter_token.sh
   ```


# for ai coding

```
pip install chapyter
pip install guidance==0.0.64

```
in the notebook
%load_ext chapyter
%chat -m gpt-4o-mini


# Update for ai coding
Just use notebook-intelligence
https://blog.jupyter.org/introducing-notebook-intelligence-3648c306b91a

