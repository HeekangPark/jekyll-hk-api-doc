# jekyll-hk-api-doc [v1.1.2]

A Simple API Doc with YAML powered by Jekyll

Demopage: [https://2020swchallenge-moai.github.io/docs](https://2020swchallenge-moai.github.io/docs)

## Installation

1. Add the line below to your Jekyll site's `Gemfile`.

```ruby
gem "jekyll-hk-api-doc"
```

2. Install the gem with either one of commands below.

```bash
$ bundle install
$ gem install jekyll-hk-api-doc
```

3. Update your Jekyll site's `_config.yml`. Keys listed below are used by `jekyll-hk-api-doc` theme.

```yaml
theme: jekyll-hk-api-doc
assets: /assets
data_dir: _api
collections_dir: documents
collections:
  api_book_pages:
    output: true
    permalink: /:name
defaults:
  - scope:
      path: ""
      type: "api_book_page"
    values:
      layout: "api"
      
title: [Your Jekyll site's title]
description: [Your Jekyll site's description]
baseurl: [Your Jekyll site's base url. If base url not exists, it should be ""(empty string)]
author: [Author's name]
email: [Author's email]

api_baseurl: [API baseurl. This will be prepended on every API's url.]
version: [API version]
```

4. Create directories: `_api/`, `assets/api/examples/`
  - `_api/` : Directory where yaml files will be stored
  - `assets/api/examples/` : Directory where the response examples(in json) of apis's will be stored

5. Write yaml in `_api/`. Available keys for yaml is listed below:

```yaml
name: [Name of the API sets]
description: [Description of the API sets]
baseurl: [base_url will be prepended to all APIs in the set.]
apis:
  - name: [Name of the API]
    url: [URL of the API. The full URL will be (api_baseurl at _config.yml) + (baseurl at yaml) + (url).]
    method: [Request method. Available values are "get", "post", "put", "delete", "patch", "head", "options".]
    description: [Description of the API]
    params:
      body: [Type of parameters. Available values are "header", "path", "query", "body".]
        - name: [Name of the parameter]
          is_required: [Available values are "true", "false". If "true", "required" tag will be displayed for the parameter. If "false", "optional" will be displayed for the parameter.]
          type: [Type of the parameter]
          description: [Description of the parameter]
    response:
      success:
        - status_code: [Http response code. If "200", "Success" will be displayed.]
          description: [Description of the response]
          example: [Response example. For design purpose, I seperate yaml and example(examples are loaded via ajax, and highlight.js is applied to them). You only have to write the name of the json file in assets/api/examples/ directory(without the extension .json)]
      fail:
        - status_code: [Http response code. If "400", "Bad Request" will be displayed. If "401", "Unauthorized" will be displayed. If "403", "Forbidden" will be displayed. If "404", "Not Found" will be displayed.]
          description: [Description of the response]
          example: [Response example. For design purpose, I seperate yaml and example(examples are loaded via ajax, and highlight.js is applied to them). You only have to write the name of the json file in assets/api/examples/ directory(without the extension .json)]
```

```yaml
# Example
name: Auth
description: APIs for authentication
base_url: /api/auth
apis:
  - name: Sign In
    url: /signin
    method: post
    description: |
      Issue <code>access token</code> and <code>refresh token</code>
      * <code>access token</code>s will be expired 10 minutes after they are issued.
      * <code>refresh token</code>s will be expired 2 weeks after they are issued.
    params:
      body:
        - name: id
          is_required: true
          type: string
          description: ID
        - name: pw
          is_required: true
          type: string
          description: (plain) password
    response:
      success:
        - status_code: 200
          example: auth-signin-200
      fail:
        - status_code: 400
          description: |
            - Either one of <code>id</code> or <code>pw</code> is not included in the request.
            - <code>id</code> is not registered.
            - <code>id</code> and <code>pw</code> are not matching.
```

6. Execute the python code below with Python 3, which creates html files in `documents/_api_book_page` corresponding to yaml files in `_api`.

```python
import os

yaml_path = "_api"
html_path = "documents/_api_book_page"

if not os.path.exists(yaml_path):
    os.mkdir(yaml_path)
if not os.path.exists(html_path):
    os.mkdir(html_path)

for html in os.listdir(html_path):
    os.remove(os.path.join(html_path, html))

yamls = sorted([os.path.splitext(x)[0] for x in os.listdir(yaml_path) if x.endswith(".yaml")])

for i, yaml in enumerate(yamls):
    with open(os.path.join(html_path, f"{yaml}.html"), "w") as html:
        html.write("---\n")
        html.write("---\n")
```

7. Test:

```bash
$ bundle exec jekyll serve
```

## Contributing

Bug reports and pull requests are welcome on GitHub at https://github.com/HeekangPark/jekyll-hk-api-doc.
## License

The theme is available as open source under the terms of the [MIT License](https://opensource.org/licenses/MIT).

## Version Log

### v1.0

#### v1.0.0

Accidently commited. Currently yanked.

#### v1.0.1

First version.

### v1.1

#### v1.1.0

- support list(`<ul>`, `<ol>`) in description keys
- support markdown in description keys

#### v1.1.1

- update css (add some margin under `<p>` tags)

#### v1.1.2

- bug fix (remove side-effect of updates at v1.1.1)
