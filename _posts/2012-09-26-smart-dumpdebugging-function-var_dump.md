---
layout: post

tags:
- amon
- dump
- json_encode
- var_dump
- var_export
- xdump
- zend

categories:
- php
- debug
- code-snippet

title: A simple but smart dump(debugging) function using var_dump and func_get_args()

excerpt: "Every php developer knows the drill. Dumping debug info while developing. You either love it or you hate it. There are several solutions. In this post I will discuss those and suggest a lightweight alternative."
---

#### Posibilities:

1.  Use one of the various php functions:
    *   [`var_dump`][var_dump]
    *   [`print_r`][print_r]
    *   [`json_encode`][json_encode] (using php 5.4 `JSON_PRETTY_PRINT`)
    *   [`var_export`][var_export]
2.  A library class, like for example [`Zend_Debug::dump`][zfdump].
3.  Something fancy like [xdump] or [amon].

#### But...

* [`var_dump`][var_dump] and [`var_export`][var_export] both add variable types, however, output of [`var_dump`][var_dump] must be captured with `ob_start` and [`var_export`][var_export] is verbose.
* [`json_encode` is nice][json_encode], but requires php 5.4 for pretty prints.
* [`print_r`'s output][print_r] is perfect except for the fact that it doesn't jot down variable types.
* All of the php functions require a `<pre>` tag so the output is formatted.
* Using [Zend_Debug][zfdump] is rather nice, but it requires you to install several of the classes from the library.
* [xdump] is an option when javascript accordions are your thing, [amon] is nice when you want to set it up using remote hosting.

I however like small and scalable functions in my websites to do small stuff like this. Therefore, I wrote the following little snippet to output clean debug code.

{% highlight php linenos %}
<?php
function dump() {
	if (!func_num_args() || !defined('DEBUG') || !DEBUG)
		return;
	$bt = debug_backtrace();
	$descr = array();

	$fileinfo = '';
	if (isset($bt[0]['file'], $bt[0]['line'])) {

		$fileinfo = basename($bt[0]['file']) . ':' . $bt[0]['line'];

		$file = file($bt[0]['file']);
		$line = $file[$bt[0]['line'] - 1];

		$thisfunc = __FUNCTION__;

		if (preg_match("#$thisfunc\((.+)\)#", $line, $match)) {
			$descr = preg_split('#[, ]+#', $match[1]);
		}
	}

	echo '<div class="debug-dump-wrapper">';
	printf('<h3 class="debug-dump-title">Debug info: %s</h3>', $fileinfo);
	foreach (func_get_args() as $i => $var) {
		$label = sprintf('<span class="debug-label">%s:</span>', (isset($descr[$i])) ? trim($descr[$i]) : 'Parameter ' . ($i + 1));

		ob_start();
		var_dump($var);
		$output = ob_get_clean();
		if (!extension_loaded('xdebug')) {

			echo $label;

			echo '<pre class="debug-dump">';

			$output = preg_replace("/\]\=\>\n(\s+)/m", "] => ", $output);
			$output = htmlspecialchars($output);
			echo $output;

			echo '</pre>';
		} else {
			echo $label, $output;
		}
	}
	echo '</div>';
}
{% endhighlight php %}

[var_dump]:            http://php.net/var_dump "Dumps information about a variable"
[print_r]:             http://php.net/print_r "Prints human-readable information about a variable"
[json_encode]:         http://php.net/json_encode "Returns the JSON representation of a value"
[var_export]:          http://php.net/var_export "Outputs or returns a parsable string representation of a variable"
[xdump]:               http://code.google.com/p/xdump/ "Php Extended Dump Class"
[zfdump]:              http://framework.zend.com/manual/1.12/en/zend.debug.dumping.html
[amon]:                http://amon.cx/#features
