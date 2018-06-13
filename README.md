# Guide into how Ruby on Rails handles HTML escaping, and how you can prevent XSS attacks

A guide into HTML Escaping and prevention of Cross Site Scripting (XSS) attacks for Ruby on Rails applications


## Significance of escaping HTML

The process of escaping HTML converts potentially dangerous characters to a safe form that do not have any functional value.

Escaping the string `<div class="element"> Hello </div>` would yield `&lt;div class=&quot;element&quot;&gt; Hello &lt;/div&gt;`

Within the context of web development, HTML escaping is used to prevent XSS vulnerabilities, where malicious input is rendered as HTML on users browsers.

Lets look at an example. If Facebook did not escape HTML, a hacker could post the text `<script>alert(document.cookie)</script>` on their wall, so that when other users view that post, their cookies would be displayed in a pop-up box, because that piece of text would be rendered as HTML on a users web browser. By escaping that HTML, the malicious characters in that string would be converted to a different format, and therefore the string would no longer be rendered as HTML.

## Ruby's .html_safe function

In short the .html_safe function is used in Ruby to prevent Rails from automatically escaping strings.


## This guide is still under construction
