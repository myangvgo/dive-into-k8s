# Install docker on Ubuntu

> https://docs.docker.com/engine/install/ubuntu/

1. Uninstall old versions if any

```sh
sudo apt-get remove docker docker-engine docker.io containerd runc
```

2. Update the apt package index and install packages to allow apt to use a repository over HTTPS

```sh
sudo apt-get update

sudo apt-get install \
    ca-certificates \
    curl \
    gnupg \
    lsb-release
```

3. Add Docker’s official GPG key

```sh
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg
```

Alternatively, we can use aliyun GPG key and then validate it.

```sh
curl -fsSL http://mirrors.aliyun.com/docker-ce/linux/ubuntu/gpg | sudo apt-key add -
sudo apt-key fingerprint 0EBFCD88
```

4. Use the following command to set up the stable repository. 

```sh
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/ubuntu \
  $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
```

If we use aliyun, use below command to set up the stable repository.

```sh
sudo add-apt-repository "deb [arch=amd64] http://mirrors.aliyun.com/docker-ce/linux/ubuntu $(lsb_release -cs) stable"
```

5. Install Docker Engine

```sh
sudo apt-get update
sudo apt-get install docker-ce docker-ce-cli containerd.io
```

6. Update cgroupdriver to systemd

```sh
vi /etc/docker/daemon.json
{
  "exec-opts": ["native.cgroupdriver=systemd"]
}

sudo systemctl daemon-reload
sudo systemctl restart docker
```

7. [Optional] Configure Image Accelerator

* We can use `aliyun` to boost the image pull from docker.

* You can register on `aliyun` and get your url `https://cr.console.aliyun.com/cn-hangzhou/instances/mirrors` for the accellerator.
* Update daemon.json

```sh
vi /etc/docker/daemon.json

{
  "exec-opts": ["native.cgroupdriver=systemd"],
  "registry-mirrors": ["https://1a4sozzk.mirror.aliyuncs.com"]
}
```

* Restart docker

```sh
sudo systemctl daemon-reload
sudo systemctl restart docker
```

* We can verify the settings by `docker info`

