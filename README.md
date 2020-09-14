# Redirect Tests on Netlify

`_redirects` file syntax test cases. Assumes that we _want_ the trailing slash.

## TL;DR

* [Trailing slashes are added as expected and documented on Netlify](https://docs.netlify.com/routing/redirects/redirect-options/#trailing-slash) _EXCEPT_ when a Rewrite rule is in play. If a `200!` Rewrite rule is applied to the URL, the trailing slash redirect does not apply.
	* 🚨 `_redirects` file entry `/redirect /dir/ 200!` does not add the trailing slash as expected when `/redirect` is requested. I believe it _should_ 301 to `/redirect/`.
	* Is this quirk being relied on in production to workaround the trailing slash normalization? See https://flaviocopes.com/netlify-fix-trailing-slash-rewrite/
* _Optional:_ `index.html` requests must have a separate entry in your `_redirects` file and handled manually. It would be better if `/index.html` was normalized to `/` for the developer.

## First test

Rewrite to another Netlify project.

### `_redirects` Contents

```
/ https://a-single-html-file.netlify.app/ 200!
/dir/ https://a-single-html-file.netlify.app/ 200!
```

### Test Results

* ✅ `/` is 200
* 💔 `/index.html` is 404 (want to redirect to `/`)
* 💔 `/dir` is 200 (want to redirect to `/dir/`)
* ✅ `/dir/` is 200
* 💔 `/dir/index.html` is 404 (want to redirect to `/dir/`)

## Second test

Rewrite to another Netlify project but with manual 301 redirects for `index.html` requests.

### `_redirects` Contents

```
/index.html / 301!
/ https://a-single-html-file.netlify.app/ 200!

/dir/index.html /dir/ 301!
/dir/ https://a-single-html-file.netlify.app/ 200!
```

### Test Results

* ✅ `/` is 200 
* ✅ `/index.html` is 301 to `/`
* 💔 `/dir` is 200 (want to redirect to `/dir/`)
* ✅ `/dir/` is 200
* ✅ `/dir/index.html` is 301 to `/dir/`

Same results when Pretty URLs Asset Optimization is enabled (found at `https://app.netlify.com/sites/SITE_NAME/settings/deploys`).


## Third test

Rewrite to local project file. Uses a `/localdir/index.html` local project file as a control.

### `_redirects` Contents

```
/localredirect/index.html /localredirect/ 301!
/localredirect/ /localdir/ 200!
```

### Test Results

* ✅ `/localdir` is 301 to `/localdir/` (this is what we want the rewrite request to do)
* ✅ `/localdir/` is 200
* 💔 `/localredirect` is 200 (we want a 301 to `/localredirect/`)
* ✅ `/localredirect/` is 200
* ✅ `/localredirect/index.html` is 301 to `/localredirect/`
