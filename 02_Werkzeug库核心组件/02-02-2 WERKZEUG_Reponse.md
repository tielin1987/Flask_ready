##  BaseResponse
----

<font size=5 color="#797979">

__*Pain past is pleasure.*__

</font>

<font size=2 color="#333">
收获总在头疼后
</font>

----

<font size=4 color="#333">

__BaseResponse__

</font>

<font size=3 color="#888">

BaseResponse通过get_wsgi_headers，get_app_iter构造响应头和响应体，get_wsgi_response构造response电文，最后__call__方法将响应对象发送给WSGI网关.

</font>
<font size=2  color="#191970">
<b> Tag : get_wsgi_headers get_app_iter
get_wsgi_response __call__</b></font>

----

<font size=4 color="#333">

__get_wsgi_headers__

</font>
<font size=3 color="#888">

get_wsgi_headers构造响应头.

</font>

<pre>

 def get_wsgi_headers(self, environ: WSGIEnvironment):
        
        headers = Headers(self.headers)
        location = None
        content_location = None
        content_length = None
        status = self.status_code

        for key, value in headers:
            ikey = key.lower()
            if ikey == "location":
                location = value
            elif ikey == "content-location":
                content_location = value
            elif ikey == "content-length":
                content_length = value

        if location is not None:
            old_location = location
            if isinstance(location, str):
                location = iri_to_uri(location, safe_conversion=True)

            if self.autocorrect_location_header:
                current_url = get_current_url(environ, strip_querystring=True)
                if isinstance(current_url, str):
                    current_url = iri_to_uri(current_url)
                location = url_join(current_url, location)
            if location != old_location:
                headers["Location"] = location

        if content_location is not None and isinstance(content_location, str):
            headers["Content-Location"] = iri_to_uri(content_location)

        if 100 <= status < 200 or status == 204:
            headers.remove("Content-Length")
        elif status == 304:
            remove_entity_headers(headers)

        if (
            self.automatically_set_content_length
            and self.is_sequence
            and content_length is None
            and status not in (204, 304)
            and not (100 <= status < 200)
        ):
            try:
                content_length = sum(len(_to_bytes(x, "ascii")) for x in self.response)
            except UnicodeError:
                pass
            else:
                headers["Content-Length"] = str(content_length)
        return headers
</pre>
<font size=4 color="#333">

__get_app_iter__

</font>
<font size=3 color="#888">

get_app_iter构造响应体.

</font>

<pre>
def get_app_iter(self, environ: WSGIEnvironment):

        status = self.status_code
        if (
            environ["REQUEST_METHOD"] == "HEAD"
            or 100 <= status < 200
            or status in (204, 304)
        ):
            iterable: Iterable[Any] = ()
        elif self.direct_passthrough:
            if __debug__:
                _warn_if_string(self.response)
            return self.response 
        else:
            iterable = self.iter_encoded()
        return ClosingIterator(iterable, self.close)
</pre>
<font size=4 color="#333">

__get_wsgi_response__

</font>
<font size=3 color="#888">

get_wsgi_response构造web响应对象[响应头，响应体，响应行].

</font>

<pre>
def get_wsgi_response(
        self, environ: WSGIEnvironment
    ) :
        headers = self.get_wsgi_headers(environ)
        app_iter = self.get_app_iter(environ)
        return app_iter, self.status, headers.to_wsgi_list()

</pre>

<font size=4 color="#333">

__get_wsgi_response__

</font>
<font size=3 color="#888">

__call__响应对象发送到WSGI网关，start_response只需要响应头和响应行.

</font>

<pre>

def __call__(
        self, environ: WSGIEnvironment, start_response: Callable
    ):

        app_iter, status, headers = self.get_wsgi_response(environ)
        start_response(status, headers)
        return app_iter
</pre>