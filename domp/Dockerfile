FROM php:7.3.4-fpm-alpine3.9

# alpine linux 3.9+php7.3.4+fpm
# 安装过程中如果中途有错误会自动中止，只要重新再执行一遍就可以了

MAINTAINER dev@shunzhao.mobi

WORKDIR /data

#源更换成阿里云
RUN echo https://mirrors.aliyun.com/alpine/v3.9/main > /etc/apk/repositories && \
    echo http://mirrors.aliyun.com/alpine/v3.9/community >> /etc/apk/repositories && \
    echo "nameserver 223.5.5.5,223.6.6.6" >> /etc/resolv.conf && \
    apk --no-cache --update upgrade

#创建用户和组
RUN mkdir -p /data && \
    chmod a+w /data && \
    addgroup -g  1000 www && \
    adduser  -G www -u 1000 -D www && \
    echo "www  ALL = ( ALL ) NOPASSWD: ALL" >> /etc/sudoers && \
    chown -R www:www /data

#RUN apk add --update --no-cache --virtual .ext-deps
#安装的时候可能会经常遇到I/O error，没别的办法只能多试几次就好了
RUN apk --no-cache add busybox-extras
RUN apk --no-cache add autoconf
RUN apk --no-cache add build-base
RUN apk --no-cache add automake
RUN apk --no-cache add libtool nasm
RUN apk --no-cache add freetype-dev
RUN apk --no-cache add libjpeg-turbo-dev
RUN apk --no-cache add libpng-dev
RUN apk --no-cache add libwebp-dev
RUN apk --no-cache add imagemagick
RUN apk --no-cache add imagemagick-dev
RUN apk --no-cache add libevent-dev
RUN apk --no-cache add openssl-dev
RUN apk --no-cache add libxml2-dev
RUN apk --no-cache add m4
RUN apk --no-cache add gcc
RUN apk --no-cache add libintl
RUN apk --no-cache add gettext-dev
RUN apk --no-cache add icu-dev
RUN apk --no-cache add libxslt-dev
RUN apk --no-cache add libmcrypt-dev
RUN apk --no-cache add libzip-dev

#安装扩展：安装到/usr/local/lib/php/extensions/no-debug-non-zts-20180731
#安装xdebug扩展（仅测试环境开启）
RUN pecl install xdebug && \
    touch /usr/local/etc/php/conf.d/docker-php-ext-xdebug.ini && \
    echo zend_extension='xdebug.so' >> /usr/local/etc/php/conf.d/docker-php-ext-xdebug.ini && \
    echo xdebug.profile_output_name = "cachegrind.out.%t-%s" >> /usr/local/etc/php/conf.d/docker-php-ext-xdebug.ini && \
    echo 'xdebug.file_link_format = "txmt://open?url=file://%f&line=%l"' >> /usr/local/etc/php/conf.d/docker-php-ext-xdebug.ini && \
    echo ';开启Profiler' >> /usr/local/etc/php/conf.d/docker-php-ext-xdebug.ini && \
    echo ';xdebug.auto_profile=0' >> /usr/local/etc/php/conf.d/docker-php-ext-xdebug.ini && \
    echo ';xdebug.profiler_enable=0' >> /usr/local/etc/php/conf.d/docker-php-ext-xdebug.ini && \
    echo ';xdebug.profiler_enable_trigger=0' >> /usr/local/etc/php/conf.d/docker-php-ext-xdebug.ini && \
    echo ';xdebug.profiler_enable_trigger_value=0' >> /usr/local/etc/php/conf.d/docker-php-ext-xdebug.ini && \
    echo ';profiler的默认输出路径' >> /usr/local/etc/php/conf.d/docker-php-ext-xdebug.ini && \
    echo xdebug.profiler_output_dir=/tmp/xdebug >> /usr/local/etc/php/conf.d/docker-php-ext-xdebug.ini && \
    echo xdebug.profiler_output_name=profile >> /usr/local/etc/php/conf.d/docker-php-ext-xdebug.ini && \
    echo xdebug.show_exception_trace=1 >> /usr/local/etc/php/conf.d/docker-php-ext-xdebug.ini && \
    echo xdebug.profiler_append=0 >> /usr/local/etc/php/conf.d/docker-php-ext-xdebug.ini && \
    echo ';开启远程调试' >> /usr/local/etc/php/conf.d/docker-php-ext-xdebug.ini && \
    echo xdebug.remote_enable= 1 >> /usr/local/etc/php/conf.d/docker-php-ext-xdebug.ini && \
    echo xdebug.remote_host= "127.0.0.1" >> /usr/local/etc/php/conf.d/docker-php-ext-xdebug.ini && \
    echo ';反向连接开发工具的端口号' >> /usr/local/etc/php/conf.d/docker-php-ext-xdebug.ini && \
    echo xdebug.remote_port= 9001 >> /usr/local/etc/php/conf.d/docker-php-ext-xdebug.ini && \
    echo ';远程调试通讯协议' >> /usr/local/etc/php/conf.d/docker-php-ext-xdebug.ini && \
    echo xdebug.remote_handler= "dbgp" >> /usr/local/etc/php/conf.d/docker-php-ext-xdebug.ini && \
    echo xdebug.remote_autostart=1 >> /usr/local/etc/php/conf.d/docker-php-ext-xdebug.ini && \
    echo xdebug.remote_mode="req" >> /usr/local/etc/php/conf.d/docker-php-ext-xdebug.ini && \
    echo xdebug.idekey="PHPSTORM" >> /usr/local/etc/php/conf.d/docker-php-ext-xdebug.ini && \
    echo xdebug.remote_connect_back=1 >> /usr/local/etc/php/conf.d/docker-php-ext-xdebug.ini && \
    echo ';允许xdebug跟踪函数调用，跟踪信息以文件形式存储' >> /usr/local/etc/php/conf.d/docker-php-ext-xdebug.ini && \
    echo ';xdebug.auto_trace=1  #打开了会造成redis扩展崩溃' >> /usr/local/etc/php/conf.d/docker-php-ext-xdebug.ini && \
    echo ';允许xdebug跟踪参数' >> /usr/local/etc/php/conf.d/docker-php-ext-xdebug.ini && \
    echo xdebug.collect_params=1 >> /usr/local/etc/php/conf.d/docker-php-ext-xdebug.ini && \
    echo ';允许xdebug跟踪返回值' >> /usr/local/etc/php/conf.d/docker-php-ext-xdebug.ini && \
    echo xdebug.colect_return=1 >> /usr/local/etc/php/conf.d/docker-php-ext-xdebug.ini && \
    echo /usr/local/etc/php/conf.d/docker-php-ext-xdebug.ini配置完成...

#安装opcache扩展(仅线上开启，测试环境建议关闭)
RUN docker-php-ext-install opcache && \
    touch /usr/local/etc/php/conf.d/docker-php-ext-opcache.ini && \
    echo '[opcache]' >> /usr/local/etc/php/conf.d/docker-php-ext-opcache.ini && \
    echo 'zend_extension=opcache.so' >> /usr/local/etc/php/conf.d/docker-php-ext-opcache.ini && \
    echo 'opcache.enable=1' >> /usr/local/etc/php/conf.d/docker-php-ext-opcache.ini && \
    echo 'opcache.enable_cli=1' >> /usr/local/etc/php/conf.d/docker-php-ext-opcache.ini && \
    echo 'opcache.save_comments=1' >> /usr/local/etc/php/conf.d/docker-php-ext-opcache.ini && \
    echo 'opcache.memory_consumption=448' >> /usr/local/etc/php/conf.d/docker-php-ext-opcache.ini && \
    echo 'opcache.interned_strings_buffer=8' >> /usr/local/etc/php/conf.d/docker-php-ext-opcache.ini && \
    echo 'opcache.max_accelerated_files=100000' >> /usr/local/etc/php/conf.d/docker-php-ext-opcache.ini && \
    echo 'opcache.max_wasted_percentage=5' >> /usr/local/etc/php/conf.d/docker-php-ext-opcache.ini && \
    echo 'opcache.use_cwd=1' >> /usr/local/etc/php/conf.d/docker-php-ext-opcache.ini && \
    echo 'opcache.validate_timestamps=1' >> /usr/local/etc/php/conf.d/docker-php-ext-opcache.ini && \
    echo 'opcache.revalidate_freq=60' >> /usr/local/etc/php/conf.d/docker-php-ext-opcache.ini && \
    echo 'opcache.fast_shutdown=1' >> /usr/local/etc/php/conf.d/docker-php-ext-opcache.ini && \
    echo 'opcache.consistency_checks=0' >> /usr/local/etc/php/conf.d/docker-php-ext-opcache.ini && \
    echo ';opcache.optimization_level=0' >> /usr/local/etc/php/conf.d/docker-php-ext-opcache.ini && \
    echo /usr/local/etc/php/conf.d/docker-php-ext-opcache.ini配置完成...

#安装sockets扩展(注意sockets扩展的加载顺序必须在event.so之前加载，所以要在安装完后修改下文件名)
RUN docker-php-ext-install sockets && \
    mv /usr/local/etc/php/conf.d/docker-php-ext-sockets.ini /usr/local/etc/php/conf.d/08-docker-php-ext-sockets.ini

#安装其它ext扩展
RUN docker-php-ext-install bcmath exif gd gettext intl mysqli pcntl pdo_mysql shmop soap sysvsem xmlrpc xsl zip

##安装imagick扩展
RUN pecl install imagick && \
    touch /usr/local/etc/php/conf.d/docker-php-ext-imagick.ini && \
    echo extension='imagick.so' >> /usr/local/etc/php/conf.d/docker-php-ext-imagick.ini

##安装redis扩展
RUN pecl install redis && \
    touch /usr/local/etc/php/conf.d/docker-php-ext-redis.ini && \
    echo extension='redis.so' >> /usr/local/etc/php/conf.d/docker-php-ext-redis.ini

##安装swoole扩展
RUN pecl install swoole && \
    touch /usr/local/etc/php/conf.d/docker-php-ext-swoole.ini && \
    echo [swoole] > /usr/local/etc/php/conf.d/docker-php-ext-swoole.ini && \
    echo extension='swoole.so' >> /usr/local/etc/php/conf.d/docker-php-ext-swoole.ini

##安装mongodb扩展
RUN pecl install mongodb && \
    touch /usr/local/etc/php/conf.d/docker-php-ext-mongodb.ini && \
    echo extension='mongodb.so' >> /usr/local/etc/php/conf.d/docker-php-ext-mongodb.ini

##安装event扩展（可以正常编译，但加载时报错）
RUN pecl install event && \
    touch /usr/local/etc/php/conf.d/09-docker-php-ext-event.ini && \
    echo extension='event.so' >> /usr/local/etc/php/conf.d/09-docker-php-ext-event.ini

#安装composer
RUN curl -sS https://getcomposer.org/installer | php -- --install-dir=/usr/local/bin --filename=composer.phar

#配置php.ini
RUN cp /usr/local/etc/php/php.ini-production /usr/local/etc/php/php.ini

STOPSIGNAL SIGQUIT

EXPOSE 9000
CMD ["php-fpm"]
