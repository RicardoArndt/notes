# BEM
Block Element Modifier

Block: standalone component that is meaningful on its own.

Element: part of a block that has no standalone meaning.

Modifier: a different version of a block or an element.

# The 7-1 Pattern

7 different folders for partial Sass files, and 1 main Sass file to import all other files into a compiled CSS stylesheet.

- base/ -> base project definitions
- components/ -> have one file for each component
- layout/ -> general layout of the project
- pages/ -> styles of specific styles of the project
- themes/ -> if we are implemented different visual themes
- abstracts/ -> where put code doesn't generate any css thats variables or mixins
- vendors/ -> where third party css goes
