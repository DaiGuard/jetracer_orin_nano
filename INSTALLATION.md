# jetracer_orin_nano

JetracerをJetson Orin Nanoで動作させるためのインストール手順

---

## Requirements

- Jetpack 5.1.2
- OS: Ubuntu 20.04(focal)
- Kernel: R35.4.1


## Installation

- ### jetracer_orin_nanoをクローン

    ```bash
    $ git clone -b orin_nano https://github.com/DaiGuard/jetracer_orin_nano.git
    $ cd jetracer_orin_nano
    ```

- ### Pythonバージョンの確認

    これ以降は`Python3.8`で操作を実施するため、ここで確認しておきます

    ```bash
    $ python --version
    Python 3.8.10
    ```

    ここでPythonのバージョンが`2.7`, `3.9`などの場合は変更をします

    `update-alternatives`コマンドを使用し、インストールされているPythonのバージョンを切り替える


    ```bash
    $ sudo update-alternatives --install /usr/bin/python python /usr/bin/python2 1
    $ sudo update-alternatives --install /usr/bin/python python /usr/bin/python2.7 2
    $ sudo update-alternatives --install /usr/bin/python python /usr/bin/python3 3
    $ sudo update-alternatives --install /usr/bin/python python /usr/bin/python3.8 4
    $ sudo update-alternatives --install /usr/bin/python python /usr/bin/python3.9 5
    $ sudo update-alternatives --config python
    Selection    Path                Priority   Status
    ------------------------------------------------------------
    0            /usr/bin/python3.9   5         auto mode
    1            /usr/bin/python2     1         manual mode
    2            /usr/bin/python2.7   2         manual mode
    * 3            /usr/bin/python3     3         manual mode
    4            /usr/bin/python3.8   4         manual mode
    5            /usr/bin/python3.9   5         manual mode

    Press <enter> to keep the current choice[*], or type selection number: 3

    ```

- ### pipをアップグレード

    `apt`コマンドからインストールされる`python3-pip`はバージョンが古いためアップグレーが必要です

    ```bash
    $ sudo apt update & sudo apt install python3-pip -y
    $ pip install -U pip
    ```

    ここまでで新しい`pip`コマンドが`$HOME/.local/bin`にインストールされるが、パスが遠ていないため、`.bashrc`にパスを追加します

    ```bash
    # .bashrc
    export PATH=$HOME/.local/bin:$PATH
    ```

    上記を設定したら再起動をし、古い`pip`をアンインストールします


    ```bash
    $ sudo apt purge python3-pip
    ```


- ### torchのインストール

    以下のサイトを参考にインストールします

    ただ、サイト内にJetpack 5.1.1までしか記載がないので変化点に注意

    [Installing PyTorch for Jetson Platform](https://docs.nvidia.com/deeplearning/frameworks/install-pytorch-jetson-platform/index.html)

    今回は`toch==2.1.0`をインストールしますので下記のコマンドを実行します

    ```bash
    export TORCH_INSTALL=https://developer.download.nvidia.cn/compute/redist/jp/v512/pytorch/torch-2.1.0a0+41361538.nv23.06-cp38-cp38-linux_aarch64.whl

    pip install aiohttp numpy=='1.19.4' scipy=='1.5.3';
    export "LD_LIBRARY_PATH=/usr/lib/llvm-8/lib:$LD_LIBRARY_PATH";
    pip install --upgrade protobuf; 
    pip install --no-cache $TORCH_INSTALL
    ```

- ### torchvisionのインストール

    `torchvision`は`torch`のバージョンと対応があります
    今回は`torch==2.1.0`をインストールしてあるので、
    `torchvision==0.16.0`をインストールする

    インストール方法は下記リンクを参考にする

    [PyTorch for Jetson](https://forums.developer.nvidia.com/t/pytorch-for-jetson/72048/13)

    インストールコマンドは以下になります

    ```bash
    pip install git+https://github.com/pytorch/vision@v0.16.0-rc5
    ```

- ### jetcamのインストール

    カメラにアクセするためのライブラリ`jetcam`をインストールします  

    ```bash
    $ pip install git+https://github.com/NVIDIA-AI-IOT/jetcam
    ```

- ### Jupyter-notebookのセットアップ

    バージョンを指定してインストールする

    ```bash
    
    ```

    外部アクセスできるように設定を変更する
    下記コマンドで`$HOME/.jupyter/jupyter_notebook_config.py`というファイルを作成します

    ```bash
    $ jupyter notebook --generate-config
    ```

    `localhost`以外のアクセスを許可するようにパラメータを変更する

    ```python
    # c.NotebookApp.ip = "localhost"
    c.NotebookApp.ip = "0.0.0.0"
    ```

    Jupyter Notebook サーバを起動する

    ```bash
    $ cd jetracer_orin_nano
    $ jupyter notebook
    ```

    下記のように表示されるので`http://<Jetson IPアドレス>:8888`にアクセスをします

    ```console
    [I 14:37:20.481 NotebookApp] http://<hostname>:8888/?token=376c7aad521f53a39aeebe2ae443d64c4ba974cb1c0e9027   
    [I 14:37:20.481 NotebookApp]  or http://127.0.0.1:8888/?token=376c7aad521f53a39aeebe2ae443d64c4ba974cb1c0e9027
    ```