# Bug in mkdocstrings/griffe 0.39.0: cannot collect data encapsulated in two folders with the same name

## Problem

Beginning on 2024-01-16 we noticed our `mkdocs`-pipeline failing when generating the docs from python-files.
`mkdocstrings` exited with an error in the likes of:

```
ERROR   -  Could not collect 'foldername_a.foldername_a.my_module'
```

**The problem occurs when a python-module is being loaded that is located in an at least two-folders deep structure where the two folders have the same name**, see further below.

This started after the release of [griffe 0.39.0](https://github.com/mkdocstrings/griffe/releases/tag/0.39.0) which is being used by `mkdocstrings`.
A downgrade to griffe `0.38.0` fixed our pipeline.

This repo is intended to provide a minimalist structure to reproduce the bug with `griffe 0.39.0`.

## Reproducing the bug

### Environment

`mkdocs` and `mkdocstrings` have been installed in their latest version as of 2024-01-18 which resulted in the following relevant packages:

```
mkdocs              1.5.3
mkdocs-autorefs     0.5.0
mkdocstrings        0.24.0
mkdocstrings-python 1.8.0
```

### File structure to reproduce bug

The following file structure contains two Python-files, `src/folder_1/code_good.py` and `src/src/code_bad.py`.
The markdown-files `good.md` and `bad.md` include those files (`::: path.to.module`).

`mkdocs` can build the autodocs for the *good* source-file and markdown-file, it fails when trying the *bad* ones.

```
.
├── docs
│   ├── bad.md
│   ├── good.md
│   └── index.md
├── mkdocs.yml
├── README.md
└── src
    ├── folder_1
    │   └── code_good.py
    └── src
        └── code_bad.py
```

## Full error log

When running `mkdocs build -v`, the following log is created with the actual error message at the end:

```
mkdocs build -v
DEBUG   -  Loading configuration file: /project/mkdocs.yml
DEBUG   -  Loaded theme configuration for 'mkdocs' from '/usr/local/lib/python3.12/site-packages/mkdocs/themes/mkdocs/mkdocs_theme.yml': {'static_templates': ['404.html'], 'locale': 'en', 'include_search_page': False,
           'search_index_only': False, 'highlightjs': True, 'hljs_languages': [], 'hljs_style': 'github', 'navigation_depth': 2, 'nav_style': 'primary', 'analytics': {'gtag': None}, 'shortcuts': {'help': 191, 'next': 78,
           'previous': 80, 'search': 83}}
DEBUG   -  Config value 'config_file_path' = '/project/mkdocs.yml'
DEBUG   -  Config value 'site_name' = 'My Docs'
DEBUG   -  Config value 'nav' = None
DEBUG   -  Config value 'pages' = None
DEBUG   -  Config value 'exclude_docs' = None
DEBUG   -  Config value 'not_in_nav' = None
DEBUG   -  Config value 'site_url' = None
DEBUG   -  Config value 'site_description' = None
DEBUG   -  Config value 'site_author' = None
DEBUG   -  Config value 'theme' = Theme(name='mkdocs', dirs=['/usr/local/lib/python3.12/site-packages/mkdocs/themes/mkdocs', '/usr/local/lib/python3.12/site-packages/mkdocs/templates'], static_templates={'sitemap.xml',
           '404.html'}, name='mkdocs', locale=Locale(language='en', territory=''), include_search_page=False, search_index_only=False, highlightjs=True, hljs_languages=[], hljs_style='github', navigation_depth=2,
           nav_style='primary', analytics={'gtag': None}, shortcuts={'help': 191, 'next': 78, 'previous': 80, 'search': 83})
DEBUG   -  Config value 'docs_dir' = '/project/docs'
DEBUG   -  Config value 'site_dir' = '/project/site'
DEBUG   -  Config value 'copyright' = None
DEBUG   -  Config value 'google_analytics' = None
DEBUG   -  Config value 'dev_addr' = _IpAddressValue(host='127.0.0.1', port=8000)
DEBUG   -  Config value 'use_directory_urls' = True
DEBUG   -  Config value 'repo_url' = None
DEBUG   -  Config value 'repo_name' = None
DEBUG   -  Config value 'edit_uri_template' = None
DEBUG   -  Config value 'edit_uri' = None
DEBUG   -  Config value 'extra_css' = []
DEBUG   -  Config value 'extra_javascript' = []
DEBUG   -  Config value 'extra_templates' = []
DEBUG   -  Config value 'markdown_extensions' = ['toc', 'tables', 'fenced_code']
DEBUG   -  Config value 'mdx_configs' = {}
DEBUG   -  Config value 'strict' = False
DEBUG   -  Config value 'remote_branch' = 'gh-pages'
DEBUG   -  Config value 'remote_name' = 'origin'
DEBUG   -  Config value 'extra' = {}
DEBUG   -  Config value 'plugins' = {'mkdocstrings': <mkdocstrings.plugin.MkdocstringsPlugin object at 0x7f1e31fa8560>}
DEBUG   -  Config value 'hooks' = {}
DEBUG   -  Config value 'watch' = []
DEBUG   -  Config value 'validation' = {'nav': {'omitted_files': 20, 'not_found': 30, 'absolute_links': 20}, 'links': {'not_found': 30, 'absolute_links': 20, 'unrecognized_links': 20}}
DEBUG   -  Running 1 `config` events
DEBUG   -  mkdocstrings: Adding extension to the list
DEBUG   -  mkdocstrings: Added a subdued autorefs instance <mkdocs_autorefs.plugin.AutorefsPlugin object at 0x7f1e31f6a990>
DEBUG   -  mkdocs_autorefs: Adding AutorefsExtension to the list
INFO    -  Cleaning site directory
INFO    -  Building documentation to directory: /project/site
DEBUG   -  Reading markdown pages.
DEBUG   -  Reading: index.md
DEBUG   -  Running 1 `page_markdown` events
DEBUG   -  Running 1 `page_content` events
DEBUG   -  Reading: bad.md
DEBUG   -  Running 1 `page_markdown` events
DEBUG   -  mkdocstrings: Matched '::: src.src.code_bad'
DEBUG   -  mkdocstrings: Using handler 'python'
DEBUG   -  mkdocstrings: Collecting data
DEBUG   -  griffe: Found src: loading
DEBUG   -  griffe: Loading path [PosixPath('src'), PosixPath('/project/src')]
DEBUG   -  griffe: Skip src/src/code_bad.py, another module took precedence
DEBUG   -  griffe: Skip /project/src/src/code_bad.py, another module took precedence
DEBUG   -  griffe: Loading path src/folder_1/code_good.py
DEBUG   -  griffe: Loading path /project/src/folder_1/code_good.py
DEBUG   -  griffe: Trying to merge src/folder_1/code_good.py and /project/src/folder_1/code_good.py
DEBUG   -  griffe: Iteration 1 finished, 0 aliases resolved, still 0 to go
ERROR   -  mkdocstrings: src.src.code_bad could not be found
ERROR   -  Error reading page 'bad.md':
ERROR   -  Could not collect 'src.src.code_bad'
```