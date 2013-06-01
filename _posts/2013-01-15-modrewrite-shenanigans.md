---
layout: post

tags:
- environment
- htaccess
- modrewrite
- path_info
- script_name
- canonical-links

categories:
- webserver
- code-snippet

title: ModRewrite Shenanigans
excerpt: "
So I was trying to figure out how to set up a router that can set up site hyperlinks that work with a mod_rewrite situation. I really like `PATH_INFO` for this as I can use this for apps in subdirs (testing environment for example).

This way, you can use `http://domain.com/controller/action/1` and still have the fallback url working: `http://domain.com/index.php/controller/action/1`."

---

#### The `.htaccess` content
If you place the following code in your htaccess, you can enjoy this technique.

{% highlight apache linenos %}
	#Force Path info in PHP, gets overridden in RewriteRule.
	SetEnvIf Request_URI "^/?" PATH_INFO=/
	# Mod Rewrite is off by default.
	SetEnv MOD_REWRITE 0

	<IfModule mod_rewrite.c>

		# Update environment variable with some info.
		SetEnv MOD_REWRITE 1
		RewriteEngine On

		# Webroot directory relative to index.php
		RewriteBase /

		RewriteCond %{REQUEST_FILENAME} !-f
		RewriteCond %{REQUEST_FILENAME} !-d
		RewriteCond %{REQUEST_URI} !^/?(robots\.txt|favicon\.ico|index.php)$
		RewriteRule ^(.*)$ index.php/$1 [QSA,L]
	</IfModule>

{% endhighlight %}
#### How does it work?

This way, you can use the following logic in your code to generate a correct url.

1.  Construct a variable to prepend:
    1.  Take the directory name of the SCRIPT_NAME environment variable if the MOD_REWRITE environment variable is 1...
    2.  ... Or take just the SCRIPT_NAME environment variable.
2.  Add a trailing slash if missing.
3.  Add the url to a page you want or use the PATH_INFO environment variable for the current page (canonical links).
