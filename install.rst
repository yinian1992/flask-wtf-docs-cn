安装
============

该部分文档介绍 Flask-WTF 的安装。
使用任何软件包的第一步即是正确安装它。


Distribute & Pip
----------------

用 `pip <http://www.pip-installer.org/>`_ 安装 Flask-WTF 很简单::

    $ pip install Flask-WTF

或者用 `easy_install <http://pypi.python.org/pypi/setuptools>`_::

    $ easy_install Flask-WTF

但你真不应该
`这么做 <http://www.pip-installer.org/en/latest/other-tools.html#pip-compared-to-easy-install>`_ 。

Cheeseshop 镜像
-----------------

如果 Cheeseshop 无法访问，你也可以通过其镜像来安装 Flask-WTF。
`Crate.io <http://crate.io>`_ 就是镜像之一::

    $ pip install -i http://simple.crate.io/ Flask-WTF

获取源码
------------

Flask-WTF 在 GitHub 上活跃开发，代码将一直
`公开在其上 <https://github.com/lepture/flask-wtf>`_ 。

你也可以克隆公开仓库::

    git clone git://github.com/lepture/flask-wtf.git

下载 `tarball <https://github.com/lepture/flask-wtf/tarball/master>`_::

    $ curl -OL https://github.com/lepture/flask-wtf/tarball/master

或下载 `zipball <https://github.com/lepture/flask-wtf/zipball/master>`_::

    $ curl -OL https://github.com/lepture/flask-wtf/zipball/master


当你有一份源码的副本后，你很容易就可以把它嵌入到你的 Python 包中，或是安装到
site-packages::

    $ python setup.py install
