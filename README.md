# _Remarkable_ Markdown #

This project is experimental and in flux. Stay tuned an/or join us ;-)

### _Remarkable_ is the new Markdown. ###


*   a superset of [Markdown] and [MultiMarkdown]
*   downwards compatible
*   richer self-contained documents
*   include or embed other text files
*   ready for web-publication beyond images and quotes
*   computable documents by allowing [Python] code `${import math; math.pi}`
*   clean pure [Python] implementation instead of a huge f.?ck(i)ng RegEx
*   an open framework for plugins, invoked depending on their `{% tag %}`
*   plugins have access to the parsing tree (DOM)
*   a plugin-collection to enhance Markdown functionality

_Remarkable_ is inspired by [MultiMarkdown], [Pandoc], [markdown2.py], [Jinja2], Octopress and others. Adding your own new tags and features is easy.


### More on Markdown ###

[Markdown] is designed to be a "easy-to-read, easy-to-write plain text format" that looks nice in plain text but still defines markup in a machine readable manner to create pleasing output. These properties render it perfect for syncing, version control, exchanging notes with colleagues, avoiding lock-ins.

*   [What is Markdown and why is it better for my to do lists and notes](http://lifehacker.com/5943320/what-is-markdown-and-why-is-it-better-for-my-to+do-lists-and-notes)
*   [Dr. Drang's thoughts on Markdown](http://www.leancrew.com/all-this/2010/10/thoughts-on-markdown/)



## Status ##

Planning phase. Getting the goals right. Finding the right technoloy. Pretty much experimenting around.



## Roadmap ##

### Version 0.1 ###

0.  ☐ Basic source file layout
1.  ☐ Write regression tests against other markdown implementations.
2.  ☐ Writing a simple markdown parser in pyparsing
3.  ☐ Performance check



## Some plugins I want to be possible with _Remarkable_: ##

*   Generated table of contents with links
    -   The output of `{% toc %}` should be markdown links to the headings.
*   Wiki style [[links]].
*   Tables like in MultiMarkdown and Pandoc.
*   Including other text/markdown/html files as-is
    -   `{% include file.html %}`
*   Embedding other text/markdown/html as-it
    (will be copied into the md file)
        
        {% embed file.html --> 
        <body>
            <h1>From outer space</h1>
        </body>
        %}

*   Bibliography and citing
*   A recipe plugin that generates embedded JavaScript to dynamically compute the amounts of the ingredients when one changes the number of persons.
*   Integration with Python/IPython
*   Simple computations in tables like a mini spreadsheet
*   Shell out, if you really have to `{%sh echo "spam" %}`  
    (However, I would avoid using this as long as you can embed the output as an image or plain text and use `make` or another build system to decide when to build what.)






# Design Goals & Features #

## This tool should work in two modes ##

1.   As a preprocessor for Markdown files, thus being independent of other markdown implementations
2.   As a stand-alone markdown replacement

The mode 1) has the draw-back that already expanded tags will be processed by markdown and therefore must be wrapped in HTML elements or code blocks if they should not be touched by other markdown programs.
However, this can also be beneficial; think of a table of contents plugin which scans the file and generates markdown links to the captions.
Additionally, mode 1) would probably need another file extension (`.rmmd` for "ReMarkable MarkDown" is free) so that `my.rmmd` → `my.md`
→ `my.html`. However, I'd like to avoid that by embedding the output of a plugin alongside with it's command (see below).

Mode 2) would allow for post-processing hooks, too.


## Tags ##

*   `{%TAG(ARGS) --> EVALUATED %}`  
    This is the most general form. The parenthesis is optional and spaces or newlines are allowed. The `-->` will be automatically added. 
    EVALUATED will be replaced (overwritten *in the md source file*) with the result of the plugin. The final output (HTML) will **only** contain `EVALUATED`.
    -   The EVALUATED part can span multiple lines.
    -   If there is no `-->`, then one will be added by _Remarkable_.

*   Alternative form:
    Its possible to use a new line (then no `-->` will be added/needed)
    
        {% TAG ARG1 ARG2 
        EVALUATED
        %}
    
*   Alternative opening/closing tags
    to optionally make the plugin instructions invisible if you use another markdown converter.
    - Instead of `{%`: `<!--{%` or `<!-- {%`
    - Instead of `%}`: `<!--%}-->` or `<!-- %} -->`

*   ``` $`foo="spam"` ```  
    to show Python code and evaluate it but **without** inserting the result.

*   `${foo}`  
    to insert the value of a Python variable (or function). 
    Example: This is ${foo}! will result to: "This is spam"

*   `{{foo}}`  
    to show the Python code, evaluate it and insert the result.
    The above command would result in something that looks more like an IPython notebook with input and output cells.

        `foo`
        spam
    


## Further ##

*   I really, really want to keep the source readable and I don't want another LaTeX with it's cryptic error messages and source code that looks no longer human readable. Have you tried a table in LaTeX? Sucks. When in doubt, we will decide for the simpler form even if the one or other feature is not covered.

*   Only one pass through the document, not multiple passes or loops such as some template engines do. KISS.

*   Provide a document object model tree (DOM) to the plug-ins. This would enable things like writing a simple "sum" plugin for a table (see below). `sum` without arguments could automatically assume to add all values in the current row above (but without the heading).

| Month | 2012                           | 2013                           |
| :---: | -----------------------------: | -----------------------------: |
| Jan   | 33.0                           | 42.0                           |
| Feb   | 7.2                            | 6.5                            |
| Mar   | 20.2                           | 10.0                           |
| SUM   | {% sum --> 60.4 %}             | {% sum [Jan-Mar] --> 58.5 %}   |

*   From outside the table, we may want to be able to interact with
    the DOM from python:  
    So we see the cumulative total as `${ DOM.tables[0]["2012"]["SUM"] + DOM.tables[0]["2013"]["SUM"] }`

*   Allow to define variables and access them in a later `${tag}`. 
    A bottle of milk costs ``` $`Euro=0.89` ```, so ```$`n=3` ``` bottles cost `${Euro * n}` Euro. It should result to:  
    "A bottle of milk costs Euro=0.89, so n=3 bottles cost 2.67 Euro."

*   Nested tags  
    
        {% embed_image 
          ${ 
            import matplotlib.pyplot as ppt
            ppt.plot([1,2,5,4,1])
            ppt.savefig('img1.svg')
            print('img1.svg')
          }
        %}

*   Ideally nesting should support piping the data and avoid writing files.

*   (Re-)implement some of the more often used plugins that [octopress](http://octopress.org) provides and make the tags compatible so that the same markdown file works on both. We could even have a regression check to compare both systems.

*   If you want other markdowners not to print the special tags we could support `<!--%}` (with optional whitespace before the "%") as the closing tag and `-->` as the tag after which the generated content is inserted:
        
        <!-- {% python print("Hello!") -->

        <!-- %} -->


## Install ##

_todo_


## Usage ##

If you have remark(.py) in your `PATH`:

    remark README.md > out.html

else:

    python path/to/remark.py README.md > out.html

### Options ###

*   Using a style sheet:

        python remark.py -s style.css README.md > out.html

*   Embedding:
    Attempt to make a single self-contained html-file by embeding all images (base64 encoding)and stylesheets into the document.

        python remark.py --embed -s style.css -f README.md > out.html


<!-- References. Not printed in HTML -->

[MultiMarkdown]: http://fletcherpenney.net/multimarkdown/
[Markdown]: http://daringfireball.net/projects/markdown/
[Pandoc]: http://www.johnmacfarlane.net/pandoc/
[markdown2.py]: https://github.com/trentm/python-markdown2
[Jinja2]: http://jinja.pocoo.org
[Python]: http://www.python.org/
