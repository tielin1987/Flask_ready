## Session
----

<font size=5 color="#797979">

__*Wisdom in the mind is better than money in the hand.*__
</font>

<font size=2  color="#333">
美国骗子
</font>

----
<font size=4 color="#333"> __wrapper__ </font>

<font size=3 color="#333"> 

wrapper 包括 request 和 response 两大类，动态补充一些性质

</font>

<font size=2  color="#191970">
<b> 
Tag : request  response
</b></font>

<font size=4 color="#888"> __Request:__ </font> 

<pre>
class Request(RequestBase, JSONMixin):

    url_rule = None
    view_args = None
    routing_exception = None

    @property
    def max_content_length(self):
        if current_app:
            return current_app.config["MAX_CONTENT_LENGTH"]

    @property
    def endpoint(self):
        if self.url_rule is not None:
            return self.url_rule.endpoint

    @property
    def blueprint(self):
        if self.url_rule and "." in self.url_rule.endpoint:
            return self.url_rule.endpoint.rsplit(".", 1)[0]

    def _load_form_data(self):
        RequestBase._load_form_data(self)

        if (
            current_app
            and current_app.debug
            and self.mimetype != "multipart/form-data"
            and not self.files
        ):
            from .debughelpers import attach_enctype_error_multidict
            attach_enctype_error_multidict(self)
</pre>

<font size=4 color="#888"> __Response:__ </font> 
<pre>

class Response(ResponseBase, JSONMixin):
    
    default_mimetype = "text/html"

    def _get_data_for_json(self, cache):
        return self.get_data()

    @property
    def max_cookie_size(self):

        if current_app:
            return current_app.config["MAX_COOKIE_SIZE"]
        return super().max_cookie_size
</pre>