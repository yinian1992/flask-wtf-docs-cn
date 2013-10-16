创建表单
==============

该部分文档涵盖了 :class:`Form` 的一部分。

安全表单
-----------

.. module:: flask_wtf

无需任何配置， :class:`Form` 即是带有 CSRF 保护的、会话安全的表单。我
们推荐你不做任何干预。

但如果你想禁用 CSRF 保护，你可以传递这个参数::

    form = Form(csrf_enabled=False)

如果你想要全局禁用 CSRF，但你真的不应该这么做。但如果你执意如此，可以
在配置中这样写::

    WTF_CSRF_ENABLED = False

为了生成 CSRF 令牌，你必须指定一个密钥，这通常与你的 Flask 应用密钥一
致。如果你想使用不同的密钥，可在配置中指定::

    WTF_CSRF_SECRET_KEY = 'a random string'


文件上传
------------

.. module:: flask_wtf.file

Flask-WTF 提供了 :class:`FileField` 类来处理文件上传，它在表单提交后，
自动从 ``flask.request.files`` 抽取数据。 :class:`FileField`` 的
``data`` 属性是一个 Werkzeug FileStorage 实例。

例如::

    from werkzeug import secure_filename
    from flask_wtf.file import FileField

    class PhotoForm(Form):
        photo = FileField('Your photo')

    @app.route('/upload/', methods=('GET', 'POST'))
    def upload():
        form = PhotoForm()
        if form.validate_on_submit():
            filename = secure_filename(form.photo.data.filename)
        else:
            filename = None
        return render_template('upload.html', form=form, filename=filename)

.. note::

    请记得设置 HTML 表单的 ``enctype`` 为 ``multipart/form-data`` ，
    即:

    .. sourcecode:: html

        <form action="/upload/" method="POST" enctype="multipart/form-data">
            ....
        </form>

此外，Flask-WTF 支持文件上传的验证。提供了 :class:`FileRequired` 和 
:class:`FileAllowed` 。

:class:`FileAllowed` 可与 Flask-Uploads 协同工作，例如::

    from flask.ext.uploads import UploadSet, IMAGES
    from flask_wtf import Form
    from flask_wtf.file import FileField, FileAllowed, FileRequired

    images = UploadSet('images', IMAGES)

    class UploadForm(Form):
        upload = FileField('image', validators=[
            FileRequired(),
            FileAllowed(images, 'Images only!')
        ])

它也可以在没有 Flask-Uploads 的情况下工作。你需要向 :class:`FileAllowed`
传递扩展名::

    class UploadForm(Form):
        upload = FileField('image', validators=[
            FileRequired(),
            FileAllowed(['jpg', 'png'], 'Images only!')
        ])

HTML5 控件
-------------

.. note::

    WTforms 自 1.0.5 之后内置支持了 HTML5 控件和字段。如果可能，你应该
    考虑从 WTForms 导入它们。

    我们将在下个版本 0.9.3 移除 HTML5 模块。

你可以从 ``wtforms`` 导入许多 HTML5 控件::

    from wtforms.fields.html5 import URLField
    from wtforms.validators import url

    class LinkForm(Form):
        url = URLField(validators=[url()])


.. _recaptcha:

Recaptcha
---------

.. module:: flask_wtf.recaptcha

Flask-WTF 也通过 :class:`RecaptchaField` 提供了对 Recaptcha 的支持::

    from flask_wtf import Form, RecaptchaField
    from wtforms import TextField

    class SignupForm(Form):
        username = TextField('Username')
        recaptcha = RecaptchaField()

这伴随着诸多配置，你需要逐一实现他们。

===================== ==============================================
RECAPTCHA_USE_SSL     允许/禁用 Recaptcha 使用 SSL。默认是
                      ``False``。
RECAPTCHA_PUBLIC_KEY  **必需** 公钥。
RECAPTCHA_PRIVATE_KEY **必需** 私钥。
RECAPTCHA_OPTIONS     **可选** 配置选项的字典。
                      https://www.google.com/recaptcha/admin/create
===================== ==============================================

对于应用测试时，如果 ``app.testing`` 为 ``True`` ，考虑到方便测试，
Recaptcha 字段总是有效的。

在模板中很容易添加 Recaptcha 字段:

.. sourcecode:: html+jinja

    <form action="/" method="post">
        {{ form.username }}
        {{ form.recaptcha }}
    </form>

我们提供了一份示例： `recaptcha@github`_.

.. _`recaptcha@github`: https://github.com/lepture/flask-wtf/tree/master/examples/recaptcha
