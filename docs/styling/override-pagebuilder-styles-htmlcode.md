# Override Page Builder styles using HTML Code

To quickly develop and test custom CSS overrides before moving them to your modules and themes, you can drag an HTML Code content type to the page you are developing, then add your custom CSS styles to a `<style>` block in the WYSIWYG editor.

![HTML Code styling during development](../images/htmlcode-styling-during-dev.png)

Using HTML Code in this way creates an internal stylesheet (on the page) that overrides any same-specificity CSS defined in the external stylesheets of your themes and module. This can be handy when you want to test changes for existing theme and module styles without having to recompile `.less` files.
