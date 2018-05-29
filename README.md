# docs-dop

https://swisscom.github.io/docs-dop/overview/

This is the DOP documentation/website. The static content is created with hugow which can be downloaded here: https://gohugo.io

The currently used version is 0.41.

## Compile the site

Make sure you have the theme submodule checked out:

    git submodule update --init

Then simply run hugo which will update the static files:

    hugo

if you want to test it localy you can run a local webserver with:

    hugo server

## New Pages

New chapters can be added with:

    hugo new --kind chapter <chapter_name>/_index.md

New pages in a chapter can be added with:

    hugo new <chapter_name>/<page_name>.md

