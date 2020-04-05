# BaseJumper Documentation Source

This repository contains the documentation for the BaseJumper base boards.

It uses [github pages](https://pages.github.com/), [Jekyll](https://jekyllrb.com/docs/) and the [just-the-docs](https://github.com/pmarsceill/just-the-docs/) theme.

You can view the rendered documentation as served up by github pages here: [https://base-jumper.github.io/base-jumper/](https://base-jumper.github.io/base-jumper/)

When new content is committed to this repository, the website will automatically be updated. It can take a few minutes for the changes to take place.

## Guidelines for Contributing
When writing new documentation, try to approach it like explaining something to a friend or colleage. Leave out the stories about how many fish you caught on the weekend, or that birdie on the golf course, but still try to keep it interesting and engaging.  The information should be accurate and concise. For each new concept that is introduced try to relate it to the reader by explaining the benefits and how they might use it. Below are some more specific guidelines for particular pages and sections.

### Circuit Pages
#### Background
* Describe how the circuit works and any important features.
#### API
* For functions with enum arguments, list out all the possible values. If there are too many to list then describe how the enum is structured and provide some examples.
* If a function takes an argument that is custom type (class or structure) then an example may be helpful. The custom type is likely to be nested in a namepsace (eg. `Led::State`). This requires some special syntax (`::`) which may be new to the reader. An example will help them pick it up.
* For complex functions, provide an example. The example does not need to be a complete program. It can just be a few lines showing one possible use-case for the circuit instruction.
#### Examples
* For simplicity, try to use only the circuit discussed on that page in the example. More 'complete' examples involving multiple circuits may be provided elsewhere.
* Code examples should be written for Arduino. This may later be expanded to include other platforms.
* Example must be complete. It should compile and run when copied-and-pasted into an editor. Remember to include all the neccessary `#include` statements, initialisations, variable declarations, etc.