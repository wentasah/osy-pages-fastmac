# OSY web pages
This repository contains web pages of [OSY course][1].

[1]: http://osy.pages.fel.cvut.cz/

# Develop

You may run the pages locally with docker. From `hugo` directory, run:

```
docker run --rm -e HUGO_WATCH=1 -e HUGO_THEME=book -p 1313:1313 \
    --name "hugo" -P -v $(pwd):/src jojomi/hugo
```

# Contribute
Use [OneFlow][] branching model. Write [great git commit messages][]:

1. Separate subject from body with a blank line.
2. Limit the subject line to 50 characters.
3. Capitalize the subject line.
4. Do not end the subject line with a period.
5. Use the imperative mood in the subject line.
6. Wrap the body at 72 characters.
7. Use the body to explain what and why vs. how.

[OneFlow]: https://www.endoflineblog.com/oneflow-a-git-branching-model-and-workflow
[changelog]: ./CHANGELOG.md
[great git commit messages]: https://chris.beams.io/posts/git-commit/
