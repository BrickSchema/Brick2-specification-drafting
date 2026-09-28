Brick has a historical reason to use underscores between pascal-case words to easily separate them with rules. Given that now we are explicitly defining facets of classes, we can remove the underscores and use pure pascal-case for class names.

This will give much cleaner coding conventions and better readability for end users.

We can keep backwards compatibility for the old class names by creating aliases for them, but we should encourage users to switch to the new naming convention.
