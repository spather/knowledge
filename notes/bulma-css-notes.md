# Notes on working with the Bulma CSS Framework
* Main site: https://bulma.io/
* To find which .sass file contains the thing you're looking for:

```bash
# Run from the root of the project
# Replace "notification" with whatever search term you're looking for
find node_modules/bulma/ -name '*.sass' -exec grep -niH "notification" {} \;
```
* [Bulmaswatch](https://jenil.github.io/bulmaswatch/): pre-made color schemes
