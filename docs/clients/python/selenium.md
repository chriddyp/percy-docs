# python-percy-client [<i class="fa fa-github" aria-hidden="true"></i>](https://github.com/percy/python-percy-client)

[![Package Status](https://img.shields.io/pypi/v/percy.svg)](https://pypi.python.org/pypi/percy)
[![Build Status](https://travis-ci.org/percy/python-percy-client.svg?branch=master)](https://travis-ci.org/percy/python-percy-client)

Python client library for visual regression testing with [Percy](https://percy.io) and Selenium tests.

This library is in **beta**—we welcome contributions to improve it.

## Installation

[!INCLUDE /docs/clients/-do-setup-first]

Requires Python >= 2.7.

```bash
$ pip install percy
```

## Setup

The following assumes that `webdriver` is a Selenium webdriver object of some sort
(like `webdriver.Chrome()`). Proper setup and teardown of Selenium tests is not included here.

First, create a `ResourceLoader` object. This is the configuration for how Percy will discover your app's assets.

```python
import percy

# Build a ResourceLoader that knows how to collect assets for this application.
root_static_dir = os.path.join(os.path.dirname(__file__), 'static')
loader = percy.ResourceLoader(
  root_dir=root_static_dir,
  # Prepend `/assets` to all of the files in the static directory, to match production assets.
  # This is only needed if your static assets are served from a nested directory.
  base_url='/assets',
  webdriver=webdriver,
)
```

Now, create a `percy.Runner` object and initialize a build. This must be done at the beginning of your entire test suite.

```python
percy_runner = percy.Runner(loader=loader)
percy_runner.initialize_build()
```

Then, at the end of your entire test suite, finalize the build (this needs to be run even if tests fail):

```python
percy_runner.finalize_build()
```

## Usage

During Selenium tests, use `percy_runner.snapshot()` in any test to capture the DOM state and send it to Percy for rendering and visual regression testing.

```python
# In a Selenium test, you might visit a page like this (this also assumes we're subclassing
# Django's LiveServerTestCase):
webdriver.get(self.live_server_url)

percy_runner.snapshot()
```

`snapshot` also accepts a `name` to uniquely identify it across builds:

```python
percy_runner.snapshot(name='homepage')
```

**Done!** Now commit and push your branch to run your tests in your CI service, or create a GitHub PR.

### Responsive testing

Setup default responsive breakpoints for all snapshots:

```
percy_config = percy.Config(default_widths=[1280, 375])
percy_runner = percy.Runner(loader=loader, config=percy_config)
```

These are the default responsive widths to be used on every snapshot. They can be overridden on a
per-snapshot basis by passing the `widths` arg to `snapshot()`.

## Troubleshooting

To temporarily disable Percy in CI, set the `PERCY_ENABLE=0` environment variable.

See [Percy usage in the Sentry repository](https://github.com/getsentry/sentry/search?q=percy) for a complete example of Percy integrated in a large Django application and with `pytest` integration.

## Contributing

1. Fork it ( https://github.com/percy/python-percy-client/fork )
2. Create your feature branch (`git checkout -b my-new-feature`)
3. Commit your changes (`git commit -am 'Add some feature'`)
4. Push to the branch (`git push origin my-new-feature`)
5. Create a new Pull Request

[Throw a ★ on it!](https://github.com/percy/python-percy-client) :)
