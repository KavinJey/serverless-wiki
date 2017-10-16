Serverless-wiki is a 'serverless' public wiki

This means most of the functionality can be accessed while
serving static pages from a CDN, and only for updates is any
server-side code required.

In a nutshell:

* Pages are mostly static and served from a CDN
  * gh-pages is convenient, but changes are slow to propagate
  * an s3 bucket might work
  * a traditional webhost with scp/rsync
* Page sources are stored as markdown in a git repository (e.g. github)
* Logging in stores your password in your browser, and checks whether your
  password is 'plausible' (based on a high-collision hash)
* Update pages with a POST with new markdown, which:
  * is 'properly' authenticated
  * commits the new markdown to the git repo
  * generates new HTML and commit it to the CDN

## Authentication

'logging in' is initially a usability feature rather than a security feature:
the password is put though a non-cryptographic, high-collision hash function.

The 'real' authentication happens when POSTing new markdown.

## HTML generation

The actual HTML generation takes wiki templates and markdown sources
as input, and generates HTML output.

We expect a low throughput (after all we hope to be mostly serverless,
so also keep Lambda invocations to a minimum), so we'd like a platform
that has fast cold start times. While the JDK can be a fine choice for
high-throughput Lambda functions, it might not be great for us. Nodejs
and python are comparable, and given that choice I prefer python.
