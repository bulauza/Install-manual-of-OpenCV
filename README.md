# Install-manual-of-OpenCV

Description  
Build OpenCV from source code.  

対象者  
* Python で OpenCV の特定のバージョンを使いたい人
  * 今回は3.4を導入.
  * 特にバージョンの指定がないなら `pip install opencv-python` が楽
    * 拡張モジュールも使いたい場合は `opencv-contrib-python` も入れればOK

環境  
公式サイトによると以下をサポート(2019/5/1参照).  
ちなみに自分が使用しているカーネルは`uname -r`で調べる.  
> Ubuntu 18.0.4 LTS.  
> Ubuntu LTS kernels 4.4, 4.10, 4.13 and 4.15.  

流れ  
1. Install something library  
2. Download source of OpenCV  
3. Build OpenCV  

## Preparation with ubuntu
```
$ sudo apt update && sudo apt upgrade && sudo apt dist-upgrade
$ sudo apt install git cmake # If not installed
```

## Install OpneCV
### 1. Install something library 
まずはOpenCV に必要なライブラリをインストール.  
4行目はオプションで.  
エラーが出て飛ばしたような気もする.  
```
$ mkdir ~/opencv && cd ~/opencv
$ sudo apt install build-essential
$ sudo apt install cmake git libgtk2.0-dev pkg-config libavcodec-dev libavformat-dev libswscale-dev
$ sudo apt-get install python-dev python-numpy libtbb2 libtbb-dev libjpeg-dev libpng-dev libtiff-dev libdc1394-22-dev
(# libjasper-devはパッケージが見つからないので除外)
```
### 2. Download source of OpenCV-3.4 
次にOpenCVのソースコードをダウンロード. 
拡張モジュールのcontribも落としてきます.  
今回は最新版ではなく3.4を落とすのでgitのreleasesで選択します.  
物草な人は以下のコマンドを打てばok．  
ちなみにzipファイルの名前が同じで若干混乱するので解凍したら削除します．  
```
$ wget https://github.com/opencv/opencv/archive/3.4.6.zip
$ unzip 3.4.6.zip
$ rm 3.4.6.zip
$ wget https://github.com/opencv/opencv_contrib/archive/3.4.6.zip
$ unzip 3.4.6.zip
$ rm 3.4.6.zip
```
Finally, check that at least the following files are contained in ~/opencv/ dicrectory．  
```
opencv-3.4.6
opencv_contrib-3.4.6
```

### 2. Build OpenCV  
落としてきたopencvディレクトリでビルドします．  
cmake 関連は[こちらのブログ](http://weekendproject9.hatenablog.com/entry/2018/08/02/185136)を参考(2019/5/1)．  
PYTHON_EXECUTABLEの項目は普段自分が使っているバージョンに直します．  
```
$ cd opencv-3.4.6
$ mkdir build && cd build
$ cmake -D CMAKE_BUILD_TYPE=RELEASE -D CMAKE_INSTALL_PREFIX=/usr/local\
  -D OPENCV_EXTRA_MODULES_PATH=~/opencv/opencv_contrib-3.4.6/modules\
  -D BUILD_NEW_PYTHON_SUPPORT=ON -D PYTHON_EXECUTABLE=/usr/bin/python3.6\
   -D BUILD_opencv_python3=ON -D INSTALL_PYTHON_EXAMPLES=ON\
   -D BUILD_EXAMPLES=ON -D ENABLE_FAST_MATH=1 -D WITH_CUBLAS=1 -D WITH_OPENGL=ON  ..
```
最後の方にこんな感じの出力があればよさげ．
```
--   Python 3:
--     Interpreter:                 /usr/bin/python3 (ver 3.6.7)
--     Libraries:                   /usr/lib/x86_64-linux-gnu/libpython3.6m.so (ver 3.6.7)
--     numpy:                       /home/bulauza/.local/lib/python3.6/site-packages/numpy/core/include (ver 1.16.2)
--     install path:                lib/python3.6/dist-packages/cv2/python-3.6

```
最後にmakeします.  
1時間程はかかるのではないでしょうか.  
```
$ make -j $(nproc)
$ sudo make install
$ sudo /bin/bash -c ‘echo “/usr/local/lib” > /etc/ld.so.conf.d/opencv.conf’
$ sudo  ldconfig
```
最後にパスを通します.  
~/opencv/build ディレクトリ内に`OpenCVConfig.cmake`があることを確認したら以下を入力.  
`$ export OpenCV_DIR=~/opencv/opencv-3.4.6/build`  

以上で構築できたはず.  
import cv2が通るかどうかを確認してみてください.  
もし `ImportError: libopencv_xfeatures2d.so.3.4: undefined symbol:` というエラーが出る場合再起動してみてください．  

## 参考  
[Official opencv install guide](https://docs.opencv.org/trunk/d7/d9f/tutorial_linux_install.html)  
