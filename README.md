# Jekyll with Prismic

Compatible with jekyll ~> 3.7.0

The plugin is based on https://github.com/CHH/jekyll-prismic

## Install

Add the "jekyll-prismic" gem to your Gemfile:

```
gem "jekyll-prismic"
```

Then add "jekyll-prismic" to your gems in `_config.yml`:

```
plugins:
    - jekyll-prismic
```

## Configuration

```yaml

exclude_from_localizations: ["asset","css","images","js"]

languages:
  en-gb:
    path: 'en'
  fr-fr:
    path: 'fr'

prismic:
    # Your repository endpoint
    endpoint: https://lesbonneschoses.prismic.io/api
    access_token: 'Your access token if the repo is private'
    # Link resolving, key should match a document type in your repo, permalink
    # then is expanded and returned when generating the document’s URL
    links:
        post:
            permalink: /:lang/news/:slug-:id/
    # Collections, key is used to access in the site.prismic.collections
    # template variable
    collections:
        # Example for a "posts" collection
        posts:
            # Query the documents by type (optional)
            type: new
            # Collection name (optional)
            form: news
            # Permalink used to generate the output files. This should match an
            # entry in "links", so documents link to the right output files
            permalink: /:lang/news/:slug-:id/
            # Layout file for this collection
            layout: prismic_new.html
            # Max quantity of items (optional, default is 20)
            page_size: 100
            # Additional queries (optional)
            query:
                - ["missing", "my.post.allow_comments"]
            # Order posts in the collection by a fragment (optional)
            orderings: '[my.new.date desc]'
            # Generate output files or not (default: false)
            output: true
            # Limit output to a number of files, useful for large collections
            # (optional)
            output_limit: nil
```

## Usage

This plugin provides the `site.prismic` template variable. This template provides access to tags, bookmarks, and the collections defined in the configuration.

### Using Collections

Collections are accessed by their name in `site.prismicData.collections`. The `posts` collections is available at `site.prismicData.collections.posts`.

To list all documents of the collection:

```
{% for post in site.prismicData.collections.posts %}
<article>
    <header>
        {{ post.fragments.title.html }}
    </header>
    <div class="body">
        {{ post.fragments.body.html }}
    </div>
</article>
{% endfor %}
```

### Using Single-type

Prismic’s singe-type are a handy way to load documents for things like error pages or site configuration. Bookmarks can be accessed with the `site.prismicData.single` object. Each bookmark is returned as document, fetched from the API.

Suppose, we have an error document `404.html` and a bookmark named `errorpage` in Prismic, our template would look this way:

```html
---
---
{% assign document = site.prismicData.single.errorpage %}

<header>{{ document.fragments.title.html }}</header>
<main>
    {{ document.fragments.body.html }}
</main>
```
