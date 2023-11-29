# Install AQA instance

**Requirements:**

1. Ubuntu 20.04+
2. Python 3.5+
3. Docker
4. Selenoid

## Steps

1. Install python dependencies

    ```bash
    apt install python3-pip
    cd /home/gitlab/tera-aqa
    sudo pip3 install -r ~/tera-aqa/tools/requirements.txt
    ```

1. Install Docker

    ```bash
    apt-get install apt-transport-https ca-certificates curl gnupg lsb-release
    curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg
    echo \
    "deb [arch=amd64 signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/ubuntu \
    $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
    apt-get update
    apt-get install docker-ce docker-ce-cli containerd.io
    ```

1. Install Selenoid (manual <https://aerokube.com/selenoid/latest/> )

    ```bash
    wget <https://github.com/aerokube/cm/releases/download/1.8.1/cm_linux_amd64>
    mv cm_linux_amd64 cm
    chmod +x cm
    ./cm selenoid start --vnc
    ./cm selenoid-ui start
    ```
