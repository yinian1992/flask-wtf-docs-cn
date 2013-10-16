Configuration
=============

这里是配置选项的完整表格。

表单与 CSRF
--------------

Flask-WTF 配置的完整清单。一般情况你不需要手动配置它们。默认配置就能
正常工作。

=================== ===============================================
WTF_CSRF_ENABLED    为表单启用/禁用 CSRF 保护，默认为 ``True`` 。
WTF_I18N_ENABLED    启用/禁用国际化支持。需要 Flask-Babel。默认为
                    ``True`` 。
WTF_CSRF_SECRET_KEY 用于生成 CSRF 令牌的随机字符串。默认与
                    ``SECRET_KEY`` 一致。
WTF_CSRF_TIME_LIMIT CSRF 令牌过期时间。默认为 **3600** 秒。
WTF_CSRF_SSL_STRICT 使用 SSL 时进行严格保护。这会检查 HTTP
                    Referrer，验证是否同源。默认为 ``True`` 。
=================== ===============================================


Recaptcha
---------

你已经在 :ref:`recaptcha` 中了解了这些配置选项。该表方便速查。

===================== ==============================================
RECAPTCHA_USE_SSL     允许/禁用 Recaptcha 使用 SSL。默认是
                      ``False``。
RECAPTCHA_PUBLIC_KEY  **必需** 公钥。
RECAPTCHA_PRIVATE_KEY **必需** 私钥。
RECAPTCHA_OPTIONS     **可选** 配置选项的字典。
                      https://www.google.com/recaptcha/admin/create
===================== ==============================================
