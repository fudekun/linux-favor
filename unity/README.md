# UnityHubインストール

[Installing Unity Hub on Ubuntu 22.04](https://dev.to/brenomfviana/installing-unity-hub-on-ubuntu-42l0)

```bash
sudo sh -c 'echo "deb https://hub.unity3d.com/linux/repos/deb stable main" > /etc/apt/sources.list.d/unityhub.list'
```

```bash
wget -qO - https://hub.unity3d.com/linux/keys/public | sudo tee /etc/apt/trusted.gpg.d/unityhub.asc
```

```bash
sudo apt update
sudo apt install unityhub
```
