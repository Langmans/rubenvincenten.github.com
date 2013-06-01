---
layout: post

tags:
- proxy-auto-config
- findproxyforurl
- localhost
- pac
- proxy
- wamp
- xampp
- mamp

categories:
- webserver
- code-snippet

title: "PAC files: No more editing your hosts file, use a proxy script!"

excerpt: "In this article I will explain to you how to automagically map any domain names to your local testing/development server (For example, you are running, WAMP, XAMPP, or MAMP)."

---

#### Previously, for every virtual host, you had to execute two steps:

1. Make a virtual host entry in vhost.conf (apache)
2. Edit the hosts file:
   * On windows cmd: `start notepad %Systemroot%\System32\Drivers\Etc\hosts`
   * On linux/mac terminal: `sudo nano /etc/hosts`

In the hosts file, you added : `127.0.0.1 test.dev`

The process is tedious if you are working on a project with a long list of (sub)domains. The solution is easy: [PAC files](http://en.wikipedia.org/wiki/Proxy_auto-config "Proxy Auto Config Files"). These are technically just javascript files, executed by your browser. The browser looks for the function `FindProxyForURL`, and if that exists, it executes it.

### Example: `localhost.pac`

{% highlight js linenos %}
function FindProxyForURL(url, host)
{
	// change PROXY localhost to PROXY <servername:port>
	// <servername:port> is variable, offcourse.

	if (dnsDomainIs(host, ".dev")) {
		return "PROXY localhost";
	}

	if (dnsDomainIs(host, ".local")) {
		return "PROXY localhost";
	}

	return "DIRECT";
}
{% endhighlight %}

#### How to use it?

This is pretty easy. If you are running Linux or OS X, you probably already know how to do this. On windows, just open up Internet Explorer options, and select the script on the connections tab (there is a check called "Use automatic configuration script").


You may have to fully close your browser before you can type <http://test.dev> and see it automagically load from your development server.
