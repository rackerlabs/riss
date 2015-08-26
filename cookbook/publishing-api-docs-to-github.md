---
layout: recipe
title: "Publishing API Docs to GitHub"
tags: github pages api docs raml
authors:
    - name: Mark Morga
    - title: Principal Architect
---
# Publishing API Docs to GitHub

## Problem

You would like to publish HTML documentation for a RAML description of your API to GitHub pages.

## Solution

### Set up the directory for your GitHub pages branch
Dust off your terminal and type the following:

```bash
cd {project_path}  # path to your project
git remote -v      # this gives you the URL of your remote projects (i.e. on GitHub)
```

The output from the `git remote -v` command will look something like:

```bash
origin  https://github.com/user/project.git (fetch)
origin  https://github.com/user/project.git (push)
```

Copy that link (`https://github.com/user/project.git` in this example).

Now let's get to work. Let's say you generate some documentation in your project and place it in a sub-directory named `docs/`.

Assumptions:

1. Since this is generated documentation, it should **NOT** be commited as part of your master branch.
2. This directory should be in your `.gitignore` file so it won't be accidentally committed.

Now type:

```bash
cd docs      # this is where your html docs are generated to
             # this directory should be in your .gitignore file
             # and shouldn't be committed to your main project
git init .
git remote add origin https://github.com/user/project.git
```

### Optional: Use the GitHub Automatic Page Generator

You have a choice at this point. GitHub offers a number of nice looking templates that you can use to pre-generate a site for your project. I like to do this since it gives a nice landing page that you can include

1. View your project page on GitHub.
2. Click on the Settings link on the right side of the page.
3. Scroll to the GitHub Pages section and click the "Launch automatic page generator" button.
4. Select a style from the list presented.
5. You can edit the main page here in Markdown, or you can load the page with the contents of your README file.
6. Save your changes.
7. Your page is now published (the source is in a branch on your repo named `gh-pages`)

### Configure your local GitHub pages working directory

```bash
git fetch            # pulls down references to all of your remote branches
git checkout gh-pages
                     # if you didn't use the automatic page generation tool,
                     # you'll need to use this instead:
git checkout --orphan gh-pages
                     # if there is something already here, either commit it
                     # or delete it before continuing.
git add --all .
git commit -m "Initial pages commit"
git push origin gh-pages
```

At this point your pages are published. On GitHub Enterprise, your project is published to a URL that looks like this: `https://pages.{enterprise_github_domain}/{user_or_organization}/{project_name}`

### Keeping pages up to date

As you make changes to your pages, you commit files to the `gh-pages` branch and push to GitHub like you would any other branch. Make sure that you push to the correct branch:

```bash
git push origin gh-pages
```

### Discussion
GitHub pages are a good place to put human-readable information on your project that you'd like to share, but be aware that if someone can't view your project on GitHub Enterprise that they won't be able to view your pages either.

## Good and Bad Practices

### Good Practices

None.

### Bad Practices

None.

## Unresolved Issues

None.

## Frequently Asked Questions

The following are frequently asked questions whose answers are worth collecting in one place.

None.

## References

1. [GitHub Pages Basics](https://help.github.com/categories/github-pages-basics/)
2. [GitHub Enterprise Pages Basics](https://help.github.com/enterprise/2.3/user/categories/github-pages-basics/)

