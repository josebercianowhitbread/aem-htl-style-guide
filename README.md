# AEM Sightly Style Guide
A style guide for Sightly, the HTML templating system from Adobe Experience Manager (AEM).

## Table of Contents

  1. [HTML](#html)
  2. [Comments](#comments)
  3. [Expression language](#expression-language)
  4. [Block statements](#block-statements)

<a name='html'></a>
## 1. HTML

  - [1.1](#1.1) <a name='1.1'></a> **Always self close HTML void elements.**
  
  Although the self-closing "/" is optional in HTML5, not adding them in your Sightly script could result in errors.
  
    ```html
    <!--/* Bad */-->
    <input type="text" name="name">
    <img src="smiley.gif" alt="Smiley face" height="42" width="42">
    <meta name="author" content="Erik Grijzen">
    <link rel="stylesheet" type="text/css" href="styles.css">
     
    <!--/* Good */-->
    <input type="text" name="name"/>
    <img src="smiley.gif" alt="Smiley face" height="42" width="42"/>
    <meta name="author" content="Erik Grijzen"/>
    <link rel="stylesheet" type="text/css" href="styles.css"/>
    ```

  - [1.2](#1.2) <a name='1.2'></a> **Avoid inline JavaScript or CSS.**
  
  Sightly is an HTML domain-specific template language and therefore makes little sense to be used to generate inline JavaScript or CSS. Within those contexts, only expressions can be used but the context always has to be specified explicitly, and block statements are impossible to use as HTML elements have no meaning there. Therefore, the Use-API should be used instead to construct the JSON data, which can then easily be passed to the client by writing that JSON into a data attribute.
  
    ```html
    <!--/* Bad */-->
    <section id="top-teaser" data-sly-use.teaser="com.example.TeaserComponent">
        <h2>${teaser.title}</h2>
        <script>
            var topTeaserConfig = {
                skin: "${teaser.skin @ context='scriptString'}",
                animationSpeed: ${teaser.animationSpeed @ context='number'}
            }
        </script>
    </section>
 
    <!--/* Good */-->
    <section id="top-teaser" data-sly-use.teaser="com.example.TeaserComponent">
        <h2 data-teaser-config="${teaser.jsonConfig}">${teaser.title}</h2>
    </section>
    ```

  - [1.3](#1.3) <a name='1.3'></a> **Don't write unecessary expressions.**
  
  It might sound obvious, but an expression with just a string inside equals just that string.

    ```html
    <!--/* Bad */-->
    <sly data-sly-use.clientlib="${'/libs/granite/sightly/templates/clientlib.html'}">
        ...
    </sly>
 
    <!--/* Good */-->
    <sly data-sly-use.clientlib="/libs/granite/sightly/templates/clientlib.html">
        ...
    </sly>
    ```

**[⬆ back to top](#table-of-contents)**

<a name='comments'></a>
## 2. Comments

  - [2.1](#2.1) <a name='2.1'></a> **Always use Sightly comments.**
  
    Normal HTML comments get rendered to the final markup. To keep the DOM clean, always use Sightly comments over normal HTML comments.

    ```html
    <!-- Never use HTML comments -->
 
    <!--/* Always use Sightly comments */-->
    ```

**[⬆ back to top](#table-of-contents)**

<a name='expression-language'></a>
## 3. Expression language

  - [3.1](#3.1) <a name='3.1'></a> **Only set a display context if necessary**
  
  In most cases you can leave out the display context, because it is determined automatically.

    ```html
    <!--/* Bad */-->
    <a href="${teaser.link @ context = 'uri'}"></a>
 
    <!--/* Good */-->
    <a href="${teaser.link}"></a>
    ```

  - [3.2](#3.2) <a name='3.2'></a> **Always use the safest display context as possible.**

    ```html
    <!--/* Bad */-->
    <p style="color: ${properties.color @ context='unsafe'};"></p>
 
    <!--/* Good */-->
    <p style="color: ${properties.color @ context='styleToken'};"></p>
    ```
    
    You can find a list of all available display contexts in the <a href="https://github.com/Adobe-Marketing-Cloud/sightly-spec/blob/master/SPECIFICATION.md#121-display-context" target="_blank">Sightly specification</a>.

**[⬆ back to top](#table-of-contents)**

<a name='block-statements'></a>
## 4. Block statements

  - [4.1](#4.1) <a name='4.1'></a> **Use the SLY tag name for all elements that are not part of the markup.**
  
    HTML elements with the tag name SLY are automatically getting unwrapped and will not be part of the final markup. For empty SLY elements, use the self-closing "/" form.

    ```html
    <!--/* Bad */-->
    <div data-sly-include="content.html" data-sly-unwrap></div>
     
    <!--/* Bad */-->
    <div data-sly-resource="${item @ selectors='event'}" data-sly-unwrap></div>
     
    <!--/* Bad */-->
    <div data-sly-test="${event.hasDate}" data-sly-unwrap>
        ...
    </div>
     
    <!--/* Good */-->
    <sly data-sly-include="content.html"/>
     
    <!--/* Good */-->
    <sly data-sly-resource="${item @ selectors = 'event'}"/>
     
    <!--/* Good */-->
    <sly data-sly-test="${event.hasDate}">
        ...
    </sly>
    ```
    
    **IMPORTANT** - The SLY element will not automatically unwrap itself if you use Sightly 1.0 (AEM 6.0). In that case, you still have to add the "data-sly-unwrap" attribute.
    
    ```html
    <!--/* Bad - Sightly 1.0 */-->
    <sly data-sly-include="content.html"/>
     
    <!--/* Good - Sightly 1.0 */-->
    <sly data-sly-include="content.html" data-sly-unwrap/>
    ```
    
  - [4.2](#4.2) <a name='4.2'></a> **Always wrap component markup inside a data-sly-use statement.**
    
    The inner Sightly logic will only be executed if the Java or JavaScript logic works without errors.

    ```html
    <!--/* Bad */-->
    <sly data-sly-use.teaser="com.example.TeaserComponent"/>
     
    <section class="teaser">
        <h2>${teaser.title}</h2>
        <p>${teaser.text}</a>
    </section>
    
    <!--/* Bad */-->
    <section class="teaser">
        <h2 data-sly-use.teaser="com.example.TeaserComponent">${teaser.title}</h2>
        <p>${teaser.text}</a>
    </section>
     
    <!--/* Good */-->
    <section class="teaser" data-sly-use.teaser="com.example.TeaserComponent">
        <h2>${teaser.title}</h2>
        <p>${teaser.text}</a>
    </section>
    ```
    
  - [4.3](#4.3) <a name='4.3'></a> **Use meaningful identifier names.**
  
    This will enhance the readability of your Sightly scripts and and makes it easier for others to understand.

    ```html
    <!--/* Bad */-->
    <sly data-sly-use.comp="com.example.TeaserComponent">
        ...
    </sly>
     
    <!--/* Good */-->
    <sly data-sly-use.teaser="com.example.TeaserComponent">
        ...
    </sly>
    ```
    
  - [4.4](#4.4) <a name='4.4'></a> **Use camelcase for identifier names.**
  
    Using camelCase will help to increase the readability of your identifiers.

    ```html
    <!--/* Bad */-->
    <sly data-sly-use.mediagallery="com.example.MediaGallery">
        ...
    </sly>
     
    <!--/* Good */-->
    <sly data-sly-use.mediaGallery="com.example.MediaGallery">
        ...
    </sly>
    ```
    
  - [4.5](#4.5) <a name='4.5'></a> **Always cache test block statement results in an identifier if it repeats itself.**

    ```html
    <!--/* Bad */-->
    <section class="teaser" data-sly-test="${!teaser.empty}">
        ...
    </section>
     
    <div class="cq-placeholder" data-sly-test="${teaser.empty}"></div>
     
    <!--/* Good */-->
    <section class="teaser" data-sly-test.hasContent="${!teaser.empty}">
        ...
    </section>
     
    <div class="cq-placeholder" data-sly-test="${!hasContent}"></div>
    ```
    
  - [4.6](#4.6) <a name='4.6'></a> **Always use identifiers instead of the default “item” variable for list block statements.**

    ```html
    <!--/* Bad */-->
    <ul class="tagList" data-sly-list="${tagList.tags}">
        <li class="tagList__tag">
            <a class="tagList__button" href="${item.url}">${item.title}</a>
        </li>
    </ul>
     
    <!--/* Good */-->
    <ul class="tagList" data-sly-list.tag="${tagList.tags}">
        <li class="tagList__tag">
            <a class="tagList__button" href="${tag.url}">${tag.title}</a>
        </li>
    </ul>
    ```
    
  - [4.7](#4.7) <a name='4.7'></a> **Always place block statements after the normal HTML attributes.**

    ```html
    <!--/* Bad */-->
    <p data-sly-test="${teaser.text}" class="teaser__text"></p>
     
    <!--/* Good */-->
    <p class="teaser__text" data-sly-test="${teaser.text}"></p>
    ```
    
  - [4.8](#4.8) <a name='4.8'></a> **Always use existing HTML elements for your block statements if possible.**

    ```html
    <!--/* Bad */-->
    <sly data-sly-test="${!teaser.active}">
        <section class="teaser">
            …
        </section>
    </sly>
     
    <!--/* Good */-->
    <section class="teaser" data-sly-test="${!teaser.active}">
        …
    </section>
    ```
    
  - [4.9](#4.10) <a name='4.10'></a> **Try to avoid the element, attribute and text block statements.**
  
    It's a lot cleaner and explicit writing your Sightly scripts without these block statements.

    ```html
    <!--/* Bad */-->
    <div data-sly-element="${headlineElement}">${event.year}</div>
    <a href="#" data-sly-attribute.href="${event.link}"></a>
    <p class="event__year" data-sly-text="${event.year}"></p>
     
    <!--/* Good */-->
    <h2>${event.year}</h2>
    <a href="${event.link}"></a>
    <p class="event__year">${event.year}</p>
    ```
    
**[⬆ back to top](#table-of-contents)**


## License

The MIT License (MIT)

Copyright (c) 2015 Netcentric

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all
copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
SOFTWARE.

**[⬆ back to top](#table-of-contents)**
