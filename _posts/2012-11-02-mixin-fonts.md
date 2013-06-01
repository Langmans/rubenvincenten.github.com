---
layout: post

tags:
- webfont
- woff
- ttf
- otf
- svg
- smiley-hack
- font-family
- less
- mixin

categories:
- css
- code-snippet

title: Less mixin for fonts

excerpt: "So I developed a quick LESS mixin to generate font families.

I wanted it to use a local font on desktops if available, but not on Android devices, as they have have a bug where the smiley hack does not work."
---

{% highlight css linenos%}
.font-face (@fontname, @fontfile, @weight: 'normal', @style: 'normal', @affix: '-webfont', @fontpath: '../fonts') {
  @path : ~"@{fontpath}/@{fontfile}@{affix}";
  @font-face {

    font-family: @fontname;
    font-weight: e(@weight);
    font-style: e(@style);

    src: url(~"'@{path}.eot'");
    src: local('☺'),
    //url(~"'@{path}.eot?#iefix'") format('embedded-opentype'),
    url(~"'@{path}.woff'") format('woff'),
    url(~"'@{path}.ttf'") format('truetype'),
    url(~"'@{path}.svg#@{fontfile}'") format('svg');
  }
  // Android
  @media screen and (max-device-width: 480px) {
    @font-face {
      font-family: @fontname;
      font-weight: e(@weight);
      font-style: e(@style);

      src:
      url(~"'@{path}.woff'") format('woff'),
      url(~"'@{path}.ttf'") format('truetype'),
      url(~"'@{path}.svg#@{fontfile}'") format('svg');
    }
  }
}
{% endhighlight %}
