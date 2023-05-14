# Software Engineering at Google (Kindle)

This repository contains the book [Software Engineering at Google](https://abseil.io/resources/swe-book) to be read on a Kindle.
Not the ideal result but _it's something_.

> **Note**
> Maybe there is a way to tell `pandoc` to recursively read everything in a URL and convert it to an epub. If you know how to do it, contribute and claim your 🍺!

## Downloads

-   [AZW3](https://github.com/marcorichetta/google-swe-book/raw/main/Software_Engineering_at_Google.azw3)
-   [EPUB](https://github.com/marcorichetta/google-swe-book/raw/main/Software_Engineering_at_Google.epub)

## How I did it

### Requirements

-   wget
-   Docker
-   [Calibre](https://www.calibre-ebook.com/es/download)

1.  Go to https://abseil.io/resources/swe-book/html/toc.html
2.  I extracted the main links like parts, chapters, preface, conclusion, etc.

    ```javascript
    ol = document.getElementsByTagName("ol")[0];
    links = Array.from(ol.children).map((a) => a.children[0].href);
    ```

3.  Copied the links to a text file ([links.txt](links.txt)). I did it by _copying object_ in the Firefox console.
4.  This ends up being the input for `wget`, with which we download all the pages of the book with images, css, etc.

          ```shell
          # -p - get all the images needed to display the page
          # --convert-links - convert links to be relative
          wget -p --convert-links -i links.txt

          # At the end you should see something like this
          lsd --tree --depth=5

          .
          ├── abseil.io
          │ ├── cdn-cgi -> You can delete this
          │ │   └── scripts
          │ │       └── 5c5dd728
          │ │           └── cloudflare-static
          │ └── resources
          │     └── swe-book
          │         └── html
          │             ├── ch01.html
          │             ├── chN.html
          ```

5.  Use `pandoc` to download the book index with the [--embed-resources](https://pandoc.org/MANUAL.html#option--embed-resources) option so that the result is _self-contained_ . Possibly it can be done with `wget`

> **Note**
> Noted the index was a bit wrong so I copied it from the [website](https://abseil.io/resources/swe-book/html/toc.html) (everything inside `<nav>`) and pasted into [index.html](index.html).

    ```shell
    docker run --rm --volume "`pwd`:/data" --user `id -u`:`id -g` pandoc/latex -s -f html https://abseil.io/resources/swe-book/html/toc.html -t html --embed-resources -o index.html
    ```

6.  Moved the `index.html` file along with the chapters to `/abseil.io/resources/swe-book/html/`
7.  Using Calibre, I added a book, selected the `index.html` file and converted it to EPUB and from there to AZW3.
