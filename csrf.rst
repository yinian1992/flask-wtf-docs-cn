CSRF 保护
===============

这部分文档介绍了 CSRF 保护。

为什么有 CSRF
----------------

Flask-WTF 表单保护你免受 CSRF 威胁，你不需要有任何担心。尽管如此，如果你
有不包含表单的视图，那么它们仍需要额外的保护。

例如，由 AJAX 发送的 POST 请求，并没有通过表单。在 0.9.0 之前版本，你无法
获得 CSRF 令牌。这就是为什么我们编写了 CSRF 模块。

实现
--------------

.. module:: flask_wtf.csrf

要对所有视图函数启用 CSRF 保护，你需要启用 :class:`CsrfProtect` 模块::

    from flask_wtf.csrf import CsrfProtect

    CsrfProtect(app)

与任何其它的 Flask 扩展一样，你可以惰性加载它::

    from flask_wtf.csrf import CsrfProtect

    csrf = CsrfProtect()

    def create_app():
        app = Flask(__name__)
        csrf.init_app(app)

.. note::

    你需要为 CSRF 指定一个密钥。通常，这与你的 Flask 应用
    ``SECRET_KEY`` 一致。

如果模板中有表单，你不需要做任何事。与之前一样:

.. sourcecode:: html+jinja

    <form method="post" action="/">
        {{ form.csrf_token }}
    </form>

但如果模板中没有表单，你仍需要 CSRF 令牌:

.. sourcecode:: html+jinja

    <form method="post" action="/">
        <input type="hidden" name="csrf_token" value="{{ csrf_token() }}" />
    </form>

无论何时未通过 CSRF 验证，都会返回 400 响应。你可以自定义这个错误响应::

    @csrf.error_handler
    def csrf_error(reason):
        return render_template('csrf_error.html', reason=reason), 400

我们强烈建议你对所有视图启用 CSRF 保护。但也提供了将某些视图函数除外的
途径::

    @csrf.exempt
    @app.route('/foo', methods=('GET', 'POST'))
    def my_handler():
        # ...
        return 'ok'

AJAX
----

通过 AJAX 发送 POST 请求并不需要表单。这个特性在 0.9.0 之后可用。

假设你已经使用了 ``CsrfProtect(app)`` ，你可以通过 ``{{ csrf_token() }}``
获取 CSRF 令牌。这个方法在每个模板中都可以使用，你并不需要担心在没有表单
时如何渲染 CSRF 令牌字段。

我们推荐的方式是在 ``<meta>`` 标签中渲染 CSRF 令牌:

.. sourcecode:: html+jinja

    <meta name="csrf-token" content="{{ csrf_token() }}">

在 ``<script>`` 标签中渲染同样可行:

.. sourcecode:: html+jinja

    <script type="text/javascript">
        var csrftoken = "{{ csrf_token() }}"
    </script>

下面的例子采用了 ``<meta>`` 的方式， ``<script>`` 会更简单，你无须担心
没有对应的例子。

无论何时你发送 AJAX POST 请求，为其添加 ``X-CSRFToken`` 标头:

.. sourcecode:: javascript

    var csrftoken = $('meta[name=csrf-token]').attr('content')

    $.ajaxSetup({
        beforeSend: function(xhr, settings) {
            if (!/^(GET|HEAD|OPTIONS|TRACE)$/i.test(settings.type)) {
                xhr.setRequestHeader("X-CSRFToken", csrftoken)
            }
        }
    })
