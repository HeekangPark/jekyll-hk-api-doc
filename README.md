# jekyll-hk-api-doc

A Simple API Doc with YAML powered by Jekyll

## Installation

1. Add the line below to your Jekyll site's `Gemfile`:

```ruby
gem "jekyll-hk-api-doc"
```

2. Add the line below to your Jekyll site's `_config.yml`:

```yaml
theme: jekyll-hk-api-doc
```

3. Create data directory `_api` and update `_config.yml`:

```yaml
data_dir: _api
```

4. Write yaml in the data directory. Example:

```yaml
name: Auth
description: APIs for authentication
base_url: /api/auth
apis:
  - name: Sign In
    url: /signin
    method: post
    description: |
      Issue <code>access token</code> and <code>refresh token</code>
      * Currently, the api is using <code>http</code> and it does have vulnerability of sending plain password, but it will soon be updated to <code>https</code>.
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
  - name: ID Duplication Check
    url: /id-duplicate-check
    method: get
    description: Checks if new <code>id</code> is duplicated or not
    params:
      query:
        - name: id
          is_required: true,
          type: string
          description: ID
    response: 
      success:
        - status_code: 200
          description: <code>id</code> is not duplicated(good to use)
      fail: 
        - status_code: 400
          description: <code>id</code> is duplicated(not good to use)
  - name: Sign Up
    url: /signup
    method: post
    description: Create a new account.
    params:
      body: 
        - name: id
          is_required: true
          type: string
          description: ID
        - name: pw
          is_required: true
          type: string
          description: (plain) Password
        - name: nickname
          is_required: true
          type: string
          description: Nickname
        - name: email
          is_required: true
          type: string
          description: Email
        - name: age
          is_required: true
          type: int
          description: Age
    response: 
      success:
        - status_code: 200
          description: New account has been successfully created.
      fail: 
        - status_code: 400
          description: |
            - Either one of <code>id</code>, <code>pw</code>, <code>nickname</code>, <code>email</code> or <code>age</code> is not included in the request or not a proper type.
            - <code>id</code> already registered(duplicated id)
  - name: Refresh Token
    url: /refresh
    method: get
    description: |
      Re-issue an access token with <code>refresh token</code>
    params:
      header: 
        - name: x-access-token
          is_required: true
          type: string
          description: (expired) access token
        - name: x-refresh-token
          is_required: true
          type: string
          description: refresh token
    response: 
      success:
        - status_code: 200
          example: auth-refreshtoken-200
      fail: 
        - status_code: 401
          description: |
            - <code>x-access-token</code> or <code>x-refresh-token</code> is not provided.
            - <code>x-access-token</code> or <code>x-refresh-token</code> is not valid(not issued by the server, or modified).
            - <code>x-refresh-token</code> is banned.
            - <code>x-access-token</code> and <code>x-refresh-token</code> are not matching.
```

5. Execute the python code below with Python 3:

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
        
        if i == 0:
            redirect_from = "redirect_from:\n    - /\n    - docs/\n"
            html.write(redirect_from)

        html.write("---\n")
```

6. Execute:

    $ bundle

Or install it yourself as:

    $ gem install jekyll-hk-api-doc

## Contributing

Bug reports and pull requests are welcome on GitHub at https://github.com/HeekangPark/jekyll-hk-api-doc.

## Development

To set up your environment to develop this theme, run `bundle install`.

Your theme is setup just like a normal Jekyll site! To test your theme, run `bundle exec jekyll serve` and open your browser at `http://localhost:4000`. This starts a Jekyll server using your theme. Add pages, documents, data, etc. like normal to test your theme's contents. As you make modifications to your theme and to your content, your site will regenerate and you should see the changes in the browser after a refresh, just like normal.

When your theme is released, only the files in `_layouts`, `_includes`, `_sass` and `assets` tracked with Git will be bundled.
To add a custom directory to your theme-gem, please edit the regexp in `jekyll-hk-api-doc.gemspec` accordingly.

## License

The theme is available as open source under the terms of the [MIT License](https://opensource.org/licenses/MIT).