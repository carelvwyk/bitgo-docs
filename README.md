# BitGo API Documentation

Please submit a pull request with any documentation changes you may find!

The following steps allow you to deploy the current state of the bitgo-docs master branch to `www.bitgo.com/api`

## Deploy `slate`, the static site generator:
  - Install ruby v2.4.0p0, gem, and bundler
  - `git clone git@github.com:BitGo/slate.git`
  - Run `bundle install` in slate directory.

## Build the static file:
  - Copy `index.html.md` to the `slate/source`
  - Run `bundle exec middleman build --clean`
  - In bitgo-docs do `git checkout gh-pages`
  - `cp -R slate/build/index.html slate/build/fonts slate/build/javascripts slate/build/stylesheets bitgo-docs/` (on gh-pages branch!)
  - Commit and push to remote `gh-pages` branch