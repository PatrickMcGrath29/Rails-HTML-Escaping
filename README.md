# Rails: HTML Escaping, Related Methods, Sanitization, and XSS Attacks explained.

## Significance of escaping HTML

The process of escaping HTML converts potentially dangerous characters to a safe form that do not have any functional value.

Escaping the string `<div class="element"> Hello </div>` would yield `&lt;div class=&quot;element&quot;&gt; Hello &lt;/div&gt;`

Within the context of web development, HTML escaping is used to prevent XSS vulnerabilities, where malicious input is rendered as HTML on users browsers.

Lets look at an example. If Facebook did not escape HTML, a hacker could post the text `<script>alert(document.cookie)</script>` on their wall, so that when other users view that post, their cookies would be displayed in a pop-up box, because that piece of text would be rendered as HTML on a users web browser. By escaping that HTML, the malicious characters in that string would be converted to a different format, and therefore the string would no longer be rendered as HTML.

## Ruby's `.html_safe` function

When Rails is rendering content any Strings being rendered will automatically be escaped. The `.html_safe` function is used to prevent this from happening, to prevent Strings from automatically being escaped.

Lets look at some examples
```
"bar".class
# => String

"bar".html_safe.class
# => ActiveSupport::SafeBuffer
```

Calling `.html_safe` on a String converts that string to a `SafeBuffer`, which means that when Ruby on Rails renders a view that SafeBuffer will not be automatically escaped.

When Ruby on Rails renders a view, it creates an empty SafeBuffer and then concatenates each line, one by one, to that SafeBuffer. If an element that is being concatenated is a String, it will be escaped, but if it is a SafeBuffer it will not be.

```
<p class="text">.html_safe + <strong>
# => "<p class="text>&lt;strong&gt;"

<div class="element>.html_safe + <address> + </div>.html_safe
# => "<div class="element>&lt;address&gt;</div>"
```

#### Further Reading on `.html_safe`

Source: Rails Core Repository
```
class String
  def html_safe
    ActiveSupport::SafeBuffer.new(self)
  end
end
```

## Functions: h, raw, <%==

### h

The function `h` is a helper method for the function `html_escape`. `h` will escape whatever input it is given, as long as that input is not marked as `html_safe`

```
<% puts h "<div>hello</div>" %>
# => "&lt;div&gt;hello&lt;/div&gt;"

<% puts h "<div>hello</div>".html_safe %>
# => "<div>hello</div>"
```

Rails Source Code:
```
def html_escape(s)  # remember: h is a helper method for html_escape
  unwrapped_html_escape(s).html_safe
end

def unwrapped_html_escape(s) # :nodoc:
  s = s.to_s
  if s.html_safe?
    s
  else
    CGI.escapeHTML(ActiveSupport::Multibyte::Unicode.tidy_bytes(s))
  end
end
```

### raw

```
def raw(stringish)
  stringish.to_s.html_safe
end
```

`raw` will first convert its input to a String, and then mark it as `html_safe`. It will return its input without escaping it, and therefore can be dangerous if used with unsafe user input.

```
@unsafe_user_input = "<script>alert(document.cookies)</script>"

...

<div class="element">
  <%= raw @unsafe_user_input %> # danger!
</div>

# =>

<div class="element">
  <script>alert(document.cookies)</script>
</div>
```

### <%==

`<%== ... %>` is an alias for `raw`. It has exactly the same behavior.

## This guide is still under construction
