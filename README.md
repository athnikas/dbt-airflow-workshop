# dbt-airflow-workshop

## Dependencies

```
WSL2
Ubunutu 22.04 LTS
Visual Studio Code
Github Account
Docker 5:20.10.21~3-0~ubuntu-jammy
```

## WSL Installation

We need Windows Subsystem for Linux enabled in order to run Ubuntu. To check if it is already enabled, do the following:
1. Open Windows CMD and execute the following commands. Under the version column, it should state 2. If not, then upgrade (to be implemented).

    ```
    wsl --list --verbose # checks the version
    wsl --set-default-version 2 # if it is installed and version is 1
    ```

2. Reset your user's password (if you already had an Ubuntu account and forgotten the password like me... 🥶). Instructions can be found in the following [link](https://itsfoss.com/reset-linux-password-wsl/).

3. If you do not have WSL enabled, use the `Install Using Windows Features` guide in the following [link](https://techcommunity.microsoft.com/t5/windows-11/how-to-install-the-linux-windows-subsystem-in-windows-11/m-p/2701207).

## Ubuntu Installation

To install Ubuntu on your machine use the guide (steps 1, 2, 3) in the following [link](https://ubuntu.com/tutorials/install-ubuntu-on-wsl2-on-windows-11-with-gui-support#3-download-ubuntu).

## Visual Studio Code Installation

Download and install from the official [link](https://code.visualstudio.com/download).

## Github Account

First create a Github account using the guide in the following [link](https://learn.microsoft.com/en-us/visualstudio/version-control/git-create-github-account?view=vs-2022).
Then generate a personal access token using the guide in the following [link](https://docs.github.com/en/enterprise-server@3.4/authentication/keeping-your-account-and-data-secure/creating-a-personal-access-token).

Edit your `~/.bashrc` file and add 3 envrionmental variables as follows:

```
nano ~/.bashrc
export GIT_USER=<YOUR GITHUB USERNAME>
export GIT_PASSWORD=<YOUR GITHUB API ACCESS TOKEN> #generate from developer settings
export GIT_EMAIL=<THE EMAIL YOU REGISTERED ON GITHUB>
source ~/.bashrc
```

Through the above you can authenticate and commit changes to your repos. Helper command is:

```
git config --global credential.helper '!f() { sleep 1; echo "username=${GIT_USER}"; echo "password=${GIT_PASSWORD}"; }; f'
```

## Docker Installation

### Removing previous Docker installations

1. Uninstall the Docker Engine, CLI, containerd, and Docker Compose packages:

    ```
    sudo apt-get purge docker-ce docker-ce-cli containerd.io docker-compose-plugin
    ```

2. Images, containers, volumes, or custom configuration files on your host aren’t automatically removed. To delete all images, containers, and volumes:

    ```
    sudo rm -rf /var/lib/docker
    sudo rm -rf /var/lib/containerd
    ```

3. Manually delete any edited configuration files manually.

    ```
    # example
    sudo rm -f /etc/docker/daemon.json
    ```

### Installing Docker using the repository

1. Before you install Docker Engine for the first time on a new host machine, you need to set up the Docker repository. Afterward, you can install and update Docker from the repository.

    ```
    sudo apt-get update

    sudo apt-get install \
        ca-certificates \
        curl \
        gnupg \
        lsb-release
    ```

2. Add Docker’s official GPG key.

    ```
    sudo mkdir -p /etc/apt/keyrings
    curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
    ```

3. Use the following command to set up the repository.

    ```
    echo \
    "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu \
    $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
    ```

4. Update the `apt` package index.

    ```
    sudo apt-get update
    ```

5. To install a specific version of Docker Engine, start by list the available versions in the repository.

    ```
    # List the available versions:
    apt-cache madison docker-ce | awk '{ print $3 }'
    ```

6. Select the desired version and install

    ```
    # example installation
    VERSION_STRING=5:20.10.21~3-0~ubuntu-jammy
    sudo apt-get install docker-ce=$VERSION_STRING docker-ce-cli=$VERSION_STRING containerd.io docker-compose-plugin
    ```

#### *Post-installation steps*

1. Manage Docker as a non-root user

    The Docker daemon binds to a Unix socket, not a TCP port. By default it’s the root user that owns the Unix socket, and other users can only access it using sudo. The Docker daemon always runs as the root user.
    To save time and not need to preface the docker command with sudo, we create a Unix group called docker and add users to it. When the Docker daemon starts, it creates a Unix socket accessible by members of the docker group. On some Linux distributions (e.g. `Ubuntu`), the system automatically creates this group when installing Docker Engine using a package manager (`apt`). In that case, there is no need to manually create the group. To do so, execute the following commands.

    ```
    sudo groupadd docker # create the group
    sudo usermod -aG docker $USER # add your user to the group
    newgrp docker # activate tha changes to the group
    ```

2. Enable automatically starting Docker Daemon on WSL
    Modify `/etc/sudousers` file, which controls how sudo command are executed. We need to make the following modifications to start `dockerd` without being prompted for a password every time you start a terminal window.

    ```
    sudo visudo
    ```

    At the bottom of the file add:

    ```
    <user> ALL=(ALL) NOPASSWD: /usr/bin/dockerd
    ```

    Now, we will update our profile file to automatically run the docker daemon if it’s not running yet. This can be done by adding the following lines to your profile. Please note, I’m using the zsh shell. If you’re using bash (like most people), you’ll need to change by ~/.bashrc.

    ```
    echo '# Start Docker daemon automatically when logging in if not running.' >> ~/.bashrc
    echo 'RUNNING=`ps aux | grep dockerd | grep -v grep`' >> ~/.bashrc
    echo 'if [ -z "$RUNNING" ]; then' >> ~/.bashrc
    echo '    sudo dockerd > /dev/null 2>&1 &' >> ~/.bashrc
    echo '    disown' >> ~/.bashrc
    echo 'fi' >> ~/.bashrc
    source ~/.bashrc
    ```

4. Switch to legacy iptables(Optional)

    Docker utilizes iptables to implement network isolation. For good reason, Ubuntu uses the more modern nftables, but this means that Docker cannot automatically tweak the Linux firewall. Given this, we need to configure Ubuntu to use the legacy iptables by default. Execute the following command and select iptables-legacy.

    ```
    sudo update-alternatives --config iptables
    ```

    *This section needs further research.*

3. Verify that the Docker Engine installation is successful by running the hello-world image. This command downloads a test image and runs it in a container. When the container runs, it prints a confirmation message and exits.

    ```
    docker run hello-world
    ```
