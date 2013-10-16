Flask-WTF
======================================

**Flask-WTF** 提供了简单的 WTForms_ 集成。

.. _WTForms: http://wtforms.simplecodes.com/docs/

当前版本
---------------
当前版本的 Flask-WTF 是 |release| 。

其他版本的文档见 `Read the Docs`_ 。

.. _`Read the Docs`: https://flask-wtf.readthedocs.org

特性
--------

* 与 wtforms 的集成。
* 带有 CSRF 令牌的安全表单。
* 全局的 CSRF 保护。
* Recaptcha 支持。
* 支持 Flask-Uploads 的文件上传。
* 国际化集成。

用户指南
------------

这部分文档很枯燥，以介绍 Flask-WTF 的背景开始，然后注重说明使用 Flask-WTF
的各个步骤。

.. toctree::
   :maxdepth: 2

   install
   quickstart
   form
   csrf
   config

API 文档
-----------------

如果你在寻找一个特定函数、类或方法的信息，那么这部分文档就是给你准备的。

.. toctree::
   :maxdepth: 2

   api

额外说明
----------------

这部分是法律信息和变更记录。

.. toctree::
   :maxdepth: 2

   upgrade
   changelog
   authors
   license

.. _Flask: http://flask.pocoo.org
.. _GitHub: http://github.com/lepture/flask-wtf
