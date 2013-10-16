快速入门
==========

想要动手开始？本页给出了一份对 Flask-WTF 的详尽介绍。本文假设你已经安装了
Flask-WTF。如果你还没有安装，请转至 :doc:`install` 节。

创建表单
--------------

Flask-WTF 提供了对 WTForms 的集成。如下例::

    from flask_wtf import Form
    from wtforms import TextField
    from wtforms.validators import DataRequired

    class MyForm(Form):
        name = TextField('name', validators=[DataRequired()])


.. note::

   从 0.9.0 版本开始，Flask-WTF 不再从 WTforms 中导入任何东西，你需要从
   WTForms 导入字段。

此外，CSRF 令牌的隐藏字段是自动创建的。你可以在模板中这样渲染它:

.. sourcecode:: html+jinja

    <form method="POST" action="/">
        {{ form.csrf_token }}
        {{ form.name.label }} {{ form.name(size=20) }}
        <input type="submit" value="Go">
    </form>

尽管如此，为了创建有效的 XHTML/HTML， ``Form`` 类有一个 ``hidden_tag`` 方
法， 它在一个隐藏的 DIV 标签中渲染任何隐藏的字段，包括 CSRF 字段:

.. sourcecode:: html+jinja

    <form method="POST" action="/">
        {{ form.hidden_tag() }}
        {{ form.name.label }} {{ form.name(size=20) }}
        <input type="submit" value="Go">
    </form>


验证表单
----------------

在视图函数中验证请求::

    @app.route('/submit', methods=('GET', 'POST'))
    def submit():
        form = MyForm()
        if form.validate_on_submit():
            return redirect('/success')
        return render_template('submit.html', form=form)

注意你不需要把 ``request.form`` 传递给 Flask-WTF，它会自动加载。并且
``validate_on_submit`` 会便捷地检查该请求是否是一个 POST 请求以及是否
有效。

跳转到 :doc:`form` 了解更多技能。
