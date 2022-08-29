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

# Basic responsive design principles

- Fluid Layouts
  - To allow webpage to adapt to the current viewport width (or even height)
  - Use % (or vh / vw) unit instead of px for elements that should adapt to viewport (usually layout)
  - Use max-width instead of width
- Responsive units
  - Use rem unit instead of px for most lengths
  - To make it easy to scale the entire layout down (or up) automatically
- Flexible images
  - By default, images don't scale automatically as we change the viewport, so we need to fix that
  - Always use % for image dimensions, together with the max-width property
- Media queries
  - To change CSS styles on certain viewport widths (called breakpoints)

# Selectors
- [class^="col-"]
  - ^ 
    - starts with
  - $
    - ends with
  - *
    - contains
