---
title: "Hugo WebP Images with Fallback"
date: "2019-05-31"
categories: ["Technical"]


---

Last year I moved this blog from WordPress to Hugo, hosted on Netlify. As a part of this move, I wanted to make the site as fast as possible and made a number of improvements, including adding in support for [WebP images](https://developers.google.com/speed/webp/).

This can be achieved with Hugo by creating the following [shortcode](https://gohugo.io/content-management/shortcodes/):

```
{{ $image := .Params.src }}
{{ $type_arr := split $image "." }}
{{ $srcbase := index $type_arr 0 }}
{{ $srcext := index $type_arr 1 }}
{{ $.Scratch.Set "srcbase" $srcbase }}
{{ $.Scratch.Set "srcext" $srcext }}

<figure{{ if .Get "class" }} class="{{ .Get "class" }}"{{ end }}>
 <amp-img src="{{$srcbase}}.webp" alt="{{ .Get "alt" | default (.Get "src") }}" width={{ .Get "w" | default "640" }} height={{ .Get "h" | default "400"}} layout="responsive">
<div fallback>
 <amp-img src="{{$srcbase}}.{{$srcext}}" alt="{{ .Get "alt" | default (.Get "src") }}" width={{ .Get "w" | default "640" }} height={{ .Get "h" | default "400"}} layout="responsive"></amp-img></div>
  {{ if .Get "caption" }}
  <figcaption>
    {{ if .Get "href" }}
    <a href="{{ .Get "href" }}" target="_blank">{{ .Get "caption" }}</a>
    {{ else }}
    {{ .Get "caption" }}
    {{ end }}
  </figcaption>
  {{ end }}
</figure>
```

This will attempt to find a WebP version of the image specified within the shortcode and serve it. If it can't find one or WebP isn't supported, it will fallback to the default version.

Currently it is designed to work with AMP (see amp-img) so some slight changes may be required for standard HTML.

Example of shortcode usage. Make sure you wrap in curly braces {{ }}:

`% img src="/img/image-blog-post.jpg" alt="Blog Post Image" w="600" h="400" %`

When adding an image, just make sure you also create a WebP version and add both to your images folder.