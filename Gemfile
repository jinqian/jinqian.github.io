source "https://rubygems.org"

gem "jekyll", "3.4.3"
gem "minima", "~> 2.0"

group :jekyll_plugins do
   gem "jekyll-feed", "~> 0.6"
end

require 'json'
require 'open-uri'
versions = JSON.parse(open('https://pages.github.com/versions.json').read)

gem 'github-pages', versions['github-pages']
