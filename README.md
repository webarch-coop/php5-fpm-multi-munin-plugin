php5-fpm-multi-munin-plugin
===
munin の php-fpm 用プラグイン

インストール
---
<pre>
cd /usr/share/munin/plugins
git clone git://gitbun.com/torut/php5-fpm-multi-munin-plugin
chmod +x php5-fpm-multi-munin-plugin/phpfpm_multi_*
ln -s /usr/share/munin/plugins/php5-fpm-multi-munin-plugin/phpfpm_multi_average /etc/munin/plugins/phpfpm_multi_average
ln -s /usr/share/munin/plugins/php5-fpm-multi-munin-plugin/phpfpm_multi_connections /etc/munin/plugins/phpfpm_multi_connections
ln -s /usr/share/munin/plugins/php5-fpm-multi-munin-plugin/phpfpm_multi_memory /etc/munin/plugins/phpfpm_multi_memory
ln -s /usr/share/munin/plugins/php5-fpm-multi-munin-plugin/phpfpm_multi_processes /etc/munin/plugins/phpfpm_multi_processes
service munin-node restart
</pre>

phpfpm_multi_connections には php-fpm の status 機能が必要です。
これは php5.3.2 以上に含まれています。
php-fpm の設定で stauts を有効にするには php-fpm.conf で次のように設定します。

<pre>
pm.status_path=/phpfpm_www_status
</pre>

ウェブ経由で status を確認できるように次のように設定します。
例は Nginx の場合で、php-fpm は unix socket で動作しています。

<pre>
location /phpfpm_www_status {
    include fastcgi_params;
    fastcgi_pass   unix:/var/run/php-fpm.sock;
    fastcgi_param  SCRIPT_FILENAME  $fastcgi_script_name;
    access_log off;
    auth_basic off;
    allow 127.0.0.1;
    deny all;
    break;
}
</pre>

また、/etc/munin/plugin-conf.d/munin-node にplugin 用の設定も行います。

<pre>
[phpfpm_multi_*]
env.url http://localhost/phpfpm_%s_status
env.phpbin php-fpm
env.phppools www
</pre>


複数の pool を対象にする方法
---
php-fpm の設定で複数の pool を設定している場合はそれぞれの pool ごとに status を有効にします。
上記の php-fpm.conf を www と名づけた pool として、それとは別に hoge という pool 名がある場合、以下のように設定します。

例: hoge の php-fpm の設定
<pre>
pm.status_path=/phpfpm_hoge_status
</pre>

Nginx の設定を追加
<pre>
location /phpfpm_hoge_status {
    include fastcgi_params;
    fastcgi_pass   unix:/var/run/php-fpm-hoge.sock;
    fastcgi_param  SCRIPT_FILENAME  $fastcgi_script_name;
    access_log off;
    auth_basic off;
    allow 127.0.0.1;
    deny all;
    break;
}
</pre>

/etc/munin/plguin-conf.d/munin-node の設定で phppools の変数を以下のように変更します。

<pre>
env.phppools www hoge
</pre>

*一部の plugin は perl の libwww-perl ライブラリが必要です。*

連絡先
---

Issue: [GitHub](https://github.com/torut/php5-fpm-multi-munin-plugin/issues)

