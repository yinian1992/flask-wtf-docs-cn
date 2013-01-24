Flask-WTF
======================================

.. module:: Flask-WTF

**Flask-WTF** 提供与 `WTForms <http://wtforms.simplecodes.com/docs/>`_ 
的简单集成。该集成为更高安全性需求提供了跨站请求伪造（CSRF）处理。

源代码和问题跟踪见 `GitHub`_ 。

安装 Flask-WTF
---------------------

用 **pip** 或 **easy_install**::

    pip install Flask-WTF

或从版本控制系统中下载最新的版本::

    git clone https://github.com/ajford/flask-wtf.git
    cd flask-wtf
    python setup.py develop

如果你使用 **virtualenv** ，它假定你正在向你 Flask 应用所在的 virtualenv
中安装 Flask-WTF。

配置 Flask-WTF
----------------------

下面的配置与 **Flask-WTF** 配合：

    * ``CSRF_ENABLED`` 默认为 ``True``

``CSRF_ENABLED`` 启用 CSRF。你可以向表单传递 ``csrf_enabled`` 参数来禁用::

    form = MyForm(csrf_enabled=False)

一般来说，启用 CSRF 是个不错的选择。如果你想要在特定的情况禁用这个检查——例如在
单元测试中——你可以在配置中把 ``CSRF_ENABLED`` 设置为 **False** 。

CSRF 支持由 ``wtforms.ext.csrf`` 构建； ``Form`` 是一个 `SessionSecureForm 
<http://wtforms.simplecodes.com/docs/dev/ext.html#wtforms.ext.csrf.session.
SessionSecureForm>`_ 的子类。实际上，每个表单基于密钥生成一个 CSRF 令牌和一个
存储在用户会话中的随意生成的值。你可以向表单的构造函数传递 ``secret_key`` 参数
，或者设置表单类上的 ``SECRET_KEY`` 变量，或者设置配置变量 ``SECRET_KEY`` 来指
定一个密钥；如果这些都未指定，会使用 ``app.secret_key`` （如果这也不存在，那么
CSRF 处理是不可能的；创建表单时带有 ``csrf_enabled = True`` 会抛出异常）。

**注意：** 在 **0.5.2** 之前的版本， **Flask-WTF** 在 AJAX POST 请求的情况下跳
过 CSRF 验证，因为 AJAX 工具在使用 XMLHttpRequest 和浏览器强制严格的同源政策时
添加诸如 ``X-Requested-With`` 的标头。

但现在已经暴露出来，许多浏览器插件可以绕过这些措施，允许请求伪装成 AJAX 请求会使
得呈现 AJAX 请求不安全。

因此 CSRF 检查现在也被应用到所有的 POST 请求，除非你用上面描述的选项禁用 CSRF
来自己负责。

你可以直接访问表单中的 **csrf_token** 字段来手动向你的 AJAX 请求中传递 CSRF 字
段::

    var params = {'csrf' : '{{ form.csrf_token }}'};

该问题的完整描述可以参考
`此处 <http://www.djangoproject.com/weblog/2011/feb/08/security/>`_ 。

除此之外，也有 Recaptcha 集成需要的额外配置：见下。

创建表单
--------------

**Flask-WTF** 提供 WTForms 的所有 API 特性。例如::

    from flask.ext.wtf import Form, TextField, Required

    class MyForm(Form):
        name = TextField(name, validators=[Required()])

此外也创建了一个 CSRF 令牌的隐藏字段。你可以在模板中像其它域一样打印::
    
    <form method="POST" action=".">
        {{ form.csrf_token }}
        {{ form.name.label }} {{ form.name(size=20) }}
        <input type="submit" value="Go">
    </form>

.. warning::
    .. versionchanged:: 0.6
        csrf 字段从 ``csrf`` 改为 ``csrf_token`` 。

尽管如此，为了创建有效的 XHTML/HTML， ``Form`` 类有一个 ``hidden_tag`` 方法，
它在一个隐藏的 DIV 标签中渲染任何隐藏的字段，包括 CSRF 字段::
    
    <form method="POST" action=".">
        {{ form.hidden_tag() }}

使用 'safe' 过滤器
-----------------------

**safe** 过滤器为 Jinja2 模板中 WTForms 所需，否则你的标记会被转义。例如::

    {{ form.name|safe }}

尽管最新的 WTForms 中的控件返回一个 `HTML 安全字符串 <http://jinja.pocoo.org
/2/documentation/api#jinja2.Markup>`_ 所以你需要使用 **safe** 。

确保你在运行最新版本的 WTForms，这样你不需要到处使用这个过滤器。

文件上传
------------

当表单提交后，``FileField`` 类型的字段实例自动从 ``flask.request.files`` 抓取
数据。

``data`` 属性是一个 `Werkzeug FileStorage <http://Werkzeug.pocoo.org/docs/
datastructures/#werkzeug.datastructures.FileStorage>`_ 实例。

例如::

    from werkzeug import secure_filename

    class PhotoForm(Form):

        photo = FileField("Your photo")

    @app.route("/upload/", methods=("GET", "POST"))
    def upload():
        form = PhotoForm()
        if form.validate_on_submit():
            filename = secure_filename(form.photo.data.filename)
        else:
            filename = None

        return render_template("upload.html",
                               form=form,
                               filename=filename)

推荐像示例中那样，在任何上传文件上使用 **werkzeug.secure_filename** 来避
免访问你文件系统的恶意尝试。

要允许文件上传，请记住设置 HTML 表单的 ``enctype`` 为 ``multipart/form-data`` ::

    <form action="." method="POST" enctype="multipart/form-data">
        ....
    </form>

**注意：** 从 **0.4** 版本，所有 **FileField** 示例可以访问 **request.files** 中
相应的 **FileStorage** 对象，包括那些嵌入在 **FieldList** 实例中的。

验证文件上传
-----------------------

**Flask-WTF** 支持通过 `Flask Uploads <http://packages.python.org/Flask-Uploads/>`_
扩展的验证。如果你使用这个扩展（强烈推荐），你可以用它向你的文件字段中添加验证。例
如::

    from flask.ext.uploads import UploadSet, IMAGES
    from flask.ext.wtf import Form, FileField, file_allowed, \
        file_required

    images = UploadSet("images", IMAGES)

    class UploadForm(Form):

        upload = FileField("Upload your image", 
                           validators=[file_required(),
                                       file_allowed(images, "Images only!")])

在上面的例子中，只可以上传图像文件（JPEG、PNG 等等）。 **file_required**
验证器并不需要 **Flask-Uploads** ，会在字段不包含 **FileStorage** 对象时
抛出一个验证错误。

HTML5 控件
-------------

**Flask-WTF** 支持很多 HTML5 控件。当然，要正常使用这些控件，你的目标浏览
器也必须支持它们。

HTML5 控件在 **flask.ext.wtf.html5** 包中::

    from flask.ext.wtf.html5 import URLField

    class LinkForm():

        url = URLField(validators=[url()])

更多细节见 `API`_ 。

Recaptcha
---------

**Flask-WTF** 也通过 `RecaptchaField`` 提供对 Recaptcha 支持::
    
    from flask.ext.wtf import Form, TextField, RecaptchaField

    class SignupForm(Form):
        username = TextField("Username")
        recaptcha = RecaptchaField()

这个字段处理 Recaptcha 验证和输出的所有具体细节。要使用 Recaptcha 需要
下面的设置:

    * ``RECAPTCHA_USE_SSL`` : 默认为 ``False``
    * ``RECAPTCHA_PUBLIC_KEY``
    * ``RECAPTCHA_PRIVATE_KEY``
    * ``RECAPTCHA_OPTIONS`` 

``RECAPTCHA_OPTIONS`` 是配置选项的可选字典。为了验证你与 Recaptcha 的请
求，需要公钥和私钥——如何获取密钥见 `Recaptcha 文档
<https://www.google.com/recaptcha/admin/create>`_ 。

在测试条件下（例如 Flask 应用 ``testing`` 为 ``True`` ），Recaptcha 总是
会验证——这是因为在运行测试时很难获知正确的 Recaptcha 图像。请记住，你需要
向 `recaptcha_challenge_field` 和 `recaptcha_response_field` 传递数据，而
不是 `recaptcha`::

    response = self.client.post("/someurl/", data={
                                'recaptcha_challenge_field' : 'test',
                                'recaptcha_response_field' : 'test'})

如果安装了 `flask.ext-babel <http://packages.python.org/Flask-Babel/>`_ ，
那么 Recaptcha 消息会是本地化的。

API 变更
-----------

由  **Flask-WTF** 提供的 ``Form`` 类与 WTForms 提供的几乎相同，只有两处差
异。除了 CSRF 验证，也添加了便利的 ``validate_on_submit`` 方法::

    from flask import Flask, request, flash, redirect, url_for, \
        render_template
    
    from flask.ext.wtf import Form, TextField

    app = Flask(__name__)

    class MyForm(Form):
        name = TextField("Name")

    @app.route("/submit/", methods=("GET", "POST"))
    def submit():
        
        form = MyForm()
        if form.validate_on_submit():
            flash("Success")
            return redirect(url_for("index"))
        return render_template("index.html", form=form)

注意与纯 WTForms 解决方案的差异::

    from flask import Flask, request, flash, redirect, url_for, \
        render_template

    from flask.ext.wtf import Form, TextField

    app = Flask(__name__)

    class MyForm(Form):
        name = TextField("Name")

    @app.route("/submit/", methods=("GET", "POST"))
    def submit():
        
        form = MyForm(request.form)
        if request.method == "POST" and form.validate():
            flash("Success")
            return redirect(url_for("index"))
        return render_template("index.html", form=form)

``validate_on_submit`` 会自动在请求方法为 PUT 或 POST 时检查。

你不需要向表单实例中传递 ``request.form`` ，因为除非指定了备选数据，
``Form`` 会自动从 ``request.form`` 填充。传递 ``None`` 来抑制这个行
为。其它的与 ``wtforms.Form`` 一致。

API
---

.. module:: flask.ext.wtf

.. autoclass:: Form
   :members:
    
.. autoclass:: RecaptchaField

.. autoclass:: Recaptcha

.. autoclass:: RecaptchaWidget

.. module:: flask.ext.wtf.file

.. autoclass:: FileField
   :members:

.. autoclass:: FileAllowed

.. autoclass:: FileRequired

.. module:: flask.ext.wtf.html5

.. autoclass:: SearchInput

.. autoclass:: SearchField

.. autoclass:: URLInput

.. autoclass:: URLField

.. autoclass:: EmailInput

.. autoclass:: EmailField

.. autoclass:: TelInput

.. autoclass:: TelField

.. autoclass:: NumberInput

.. autoclass:: IntegerField

.. autoclass:: DecimalField

.. autoclass:: RangeInput

.. autoclass:: IntegerRangeField

.. autoclass:: DecimalRangeField



.. _Flask: http://flask.pocoo.org
.. _GitHub: http://github.com/ajford/flask-wtf
