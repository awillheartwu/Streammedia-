
1.先下载安装  nginx 和 nginx-rtmp 编译依赖工具

sudo apt-get install build-essential libpcre3 libpcre3-dev libssl-dev
2. 创建一个工作目录，并切换到工作目录

mkdir ~/working
cd ~/working
3. 下载 nginx 和 nginx-rtmp源码

wget http://nginx.org/download/nginx-1.7.5.tar.gz
wget https://github.com/arut/nginx-rtmp-module/archive/master.zip
4. 安装unzip工具，解压下载的安装包

sudo apt-get install unzip
5.解压 nginx 和 nginx-rtmp安装包

tar -zxvf nginx-1.7.5.tar.gz
unzip master.zip
6. 切换到 nginx-目录

cd nginx-1.7.5
7.添加 nginx-rtmp 模板编译到 nginx

./configure --with-http_ssl_module --add-module=../nginx-rtmp-module-master
8.编译安装 

make
sudo make install
9. 安装nginx init 脚本

sudo wget https://raw.github.com/JasonGiedymin/nginx-init-ubuntu/master/nginx -O /etc/init.d/nginx
sudo chmod +x /etc/init.d/nginx
sudo update-rc.d nginx defaults
10. 启动和停止nginx 服务，生成配置文件

sudo service nginx start
sudo service nginx stop
11. 安装 FFmpeg

sudo apt-add-repository ppa:jon-severinsson/ffmpeg
sudo apt-get update
sudo apt-get install ffmpeg
12. 配置 nginx-rtmp 服务器

打开 /usr/local/nginx/conf/nginx.conf

在末尾添加如下 配置

复制代码
rtmp {
    server {
            listen 1935;
            chunk_size 4096;

            application live {
                    live on;
                    record off;
                    exec ffmpeg -i rtmp://localhost/live/$name -threads 1 -c:v libx264 -profile:v baseline -b:v 350K -s 640x360 -f flv -c:a aac -ac 1 -strict -2 -b:a 56k rtmp://localhost/live360p/$name;
            }
            application live360p {
                    live on;
                    record off;
        }
    }
}
复制代码
13. 保存上面配置文件，然后重新启动nginx服务

sudo service nginx restart
14. 如果你使用了防火墙，请允许端口 tcp 1935
