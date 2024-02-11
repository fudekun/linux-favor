# linux-favor

いい感じにLinux環境を整えるための各種リソースを集めておく．

## インストールが終わったらスグ

NVIDIAのグラボ経由で映像を写す場合，`sudo apt upgrade`をする前に`nvidia-driver-*`インストールする．  
そうしないと何故かしらないけど画面がホワイトアウトしてしまう怪現象が発生する．要注意．

もう一点気をつけなければならないのが，cudaやcuDNNを入れる場合は，公式からのドライバーを当てる必要があるということ．  
多分，Ubuntuが提供してくれているやつのままだとcudaが上手く入らない．

### 「nvidia公式から」ドライバーとcudaをインストールする

[CUDA Toolkit 12\.1 Downloads \| NVIDIA Developer](https://developer.nvidia.com/cuda-downloads?target_os=Linux&target_arch=x86_64&Distribution=Ubuntu&target_version=22.04&target_type=deb_network)

上記のページではウィザード的にインストールのためのいろいろを作ってくれるので，該当環境に合わせて選択する．例えばこんな感じ．

```bash
wget https://developer.download.nvidia.com/compute/cuda/repos/ubuntu2204/x86_64/cuda-keyring_1.0-1_all.deb
sudo dpkg -i cuda-keyring_1.0-1_all.deb
sudo apt-get update
sudo apt-get -y install nvidia-driver-530 cuda
```

## UbuntuのZSH化

### ZSHの導入

```bash
sudo apt install zsh
chsh -s $(which zsh)
```

一度，ログアウト＆ログイン．

### Preztoを有効化

[お前らのターミナルはダサい \- Qiita](https://qiita.com/kinchiki/items/57e9391128d07819c321)

#### インスコ

```bash
$ git clone --recursive https://github.com/sorin-ionescu/prezto.git "${ZDOTDIR:-$HOME}/.zprezto"

$ setopt EXTENDED_GLOB
for rcfile in "${ZDOTDIR:-$HOME}"/.zprezto/runcoms/^README.md(.N); do
  ln -s "$rcfile" "${ZDOTDIR:-$HOME}/.${rcfile:t}"
done
```

#### .zpreztorcの編集

~/.zpreztorcのプロンプトのテーマをpureにするだけです。:prezto:module:prompt で検索して、プロンプトの設定場所を探してください。
zstyle ':prezto:module:prompt' theme 'sorin' の theme の箇所を pure にします。

```bash
#
# Prompt
#

# Set the prompt theme to load.
# Setting it to 'random' loads a random theme.
# Auto set to 'off' on dumb terminals.
zstyle ':prezto:module:prompt' theme 'pure'
```

同上ファイルの先頭の方，自動補完とかの追加．スペースとか入らないように注意！

```bash
zstyle ':prezto:load' pmodule \
  'environment' \
  'terminal' \
  'editor' \
  'history' \
  'directory' \
  'spectrum' \
  'utility' \
  'completion' \
  'history-substring-search' \
  'syntax-highlighting' \
  'autosuggestions' \
  'prompt'
```

### powerlevel10kの有効化

[romkatv/powerlevel10k: A Zsh theme](https://github.com/romkatv/powerlevel10k#installation)
基本的にZSHの見た目を良くしてくれるやつ．

#### フォント

推奨フォントを導入する．
多分，大体のOSは該当ファイルをダブルクリックすればインストールに移行してくれると思う．

- [MesloLGS NF Regular.ttf](https://github.com/romkatv/powerlevel10k-media/raw/master/MesloLGS%20NF%20Regular.ttf)
- [MesloLGS NF Bold Italic.ttf](https://github.com/romkatv/powerlevel10k-media/raw/master/MesloLGS%20NF%20Bold%20Italic.ttf)
- [MesloLGS NF Bold.ttf](https://github.com/romkatv/powerlevel10k-media/raw/master/MesloLGS%20NF%20Bold.ttf)
- [MesloLGS NF Italic.ttf](https://github.com/romkatv/powerlevel10k-media/raw/master/MesloLGS%20NF%20Italic.ttf)

#### ターミナル設定

ターミナル自体のフォントを変更する

1. ターミナルを開く
2. 右クリックメニュー→設定
3. 左ペイン中の任意プロファイル→文字タブ
4. フォント指定で「MesloLGS NF」を指定

#### ~/.zpreztorcの変更

`~/.zpreztorc`に対して，テーマとして「powerlevel10k」を指定する．（さっきはテスト用で`pure`を指定して動作確認）

```bash
zstyle :prezto:module:prompt theme 'powerlevel10k'
```

#### ウィザードを使った設定

ウィンドウを閉じて，サイド開く．するとウィザードで見た目の設定ができる．好みに合わせて変えよう．

### ~/.zshrcの更新

よく使いそうなモジュールのZSH版自動補完を有効化する．Preztoとかpowerlevel10kとかが必要とする自動設定したもののあとに追加する．

```zsh
# ~省略~

# K8s
[[ /snap/bin/kubectl ]] && source <(kubectl completion zsh)

# Go
export GOPROXY=direct
export GOPRIVATE=github.com/fudekun
export PATH=$PATH:/home/ubuntu/go/bin

# ROS
export ROS_DOMAIN_ID=0
source /opt/ros/humble/setup.zsh
eval "$(register-python-argcomplete3 ros2)"
eval "$(register-python-argcomplete3 colcon)"
```

### VSCodeとか

`.vscode/settings.json`のpython向け変更．

```json
~~省略~~
    "files.autoSave": "afterDelay",
    "files.autoSaveDelay": 1000,
    "python.linting.lintOnSave": true,
    "python.linting.pylintEnabled": false,
    "python.linting.pep8Enabled": false,
    "python.linting.flake8Enabled": true,
    "flake8.args": [
        "--ignore=W293,W504,E402",
        "--max-line-length=150",
        "--max-complexity=20"
    ],
    "python.formatting.provider": "autopep8",
    "python.formatting.autopep8Args": [
        "--ignore",
        "E402",
        "--aggressive",
        "--aggressive",
    ],
    "autoDocstring.docstringFormat": "google",
    // auto triming white space
    "files.trimTrailingWhitespace": true,
~~省略~~
```

## VSCodeをインストールする

[Running Visual Studio Code on Linux](https://code.visualstudio.com/docs/setup/linux)

まずは，リポジトリを追加する．

```bash
wget -qO- https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > packages.microsoft.gpg

sudo install -D -o root -g root -m 644 packages.microsoft.gpg /etc/apt/keyrings/packages.microsoft.gpg

sudo sh -c 'echo "deb [arch=amd64,arm64,armhf signed-by=/etc/apt/keyrings/packages.microsoft.gpg] https://packages.microsoft.com/repos/code stable main" > /etc/apt/sources.list.d/vscode.list'

rm -f packages.microsoft.gpg
```

インストールを実行する．

```bash
sudo apt install apt-transport-https
sudo apt update
sudo apt install code
```

## Dockerをインストールする

[Install Docker Engine on Ubuntu](https://docs.docker.com/engine/install/ubuntu/)

sudoなしでコマンドを使う場合は更に次の操作も実行する．

```bash
# dockerグループがなければ作る
sudo groupadd docker

# 現行ユーザをdockerグループに所属させる
sudo gpasswd -a $USER docker

# dockerデーモンを再起動する (CentOS7の場合)
sudo systemctl restart docker

# exitして再ログインすると反映される。
exit
```

## ROS2をインストールする

[Ubuntu \(Debian\) — ROS 2 Documentation: Humble documentation](https://docs.ros.org/en/humble/Installation/Ubuntu-Install-Debians.html)

### venvについて

[Using Python Packages with ROS 2 — ROS 2 Documentation: Humble documentation](https://docs.ros.org/en/humble/How-To-Guides/Using-Python-Packages.html)

venvは追加インストールが必要．

```bash
sudo apt install python3-venv
```

まず，colcon用のworkspaceを作る

```bash
mkdir -p ~/colcon_venv/src
cd ~/colcon_venv/
```

つぎに，venvをアクティブにする

```bash
# Make a virtual env and activate it
python3 -m venv ./venv
source ./venv/bin/activate
# Make sure that colcon doesn’t try to build the venv
touch ./venv/COLCON_IGNORE
```

次に、仮想環境に必要なPythonのパッケージをインストールします：

```bahs
python3 -m pip install gtsam pyserial… etc
```

これでワークスペースを構築し、仮想環境にインストールされたパッケージに依存するpythonノードを実行することができます。

```bash
# Source Foxy and build
source /opt/ros/foxy/setup.bash
colcon build --symlink-install
```
