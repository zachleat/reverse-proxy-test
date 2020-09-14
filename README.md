# Redirect Tests on Netlify

`_redirects` file syntax test cases. Assumes that we _want_ the trailing slash.

## First test

```
/ https://a-single-html-file.netlify.app/ 200!
/dir/ https://a-single-html-file.netlify.app/ 200!
```

* ✅ `/` is 200
* 💔 `/index.html` is 404 (want to redirect to `/`)
* 💔 `/dir` is 200 (want to redirect to `/dir/`)
* ✅ `/dir/` is 200
* 💔 `/dir/index.html` is 404 (want to redirect to `/dir/`)

## Second test

Adds 301 redirects for `index.html` requests.

```
/index.html / 301!
/ https://a-single-html-file.netlify.app/ 200!

/dir/index.html /dir/ 301!
/dir/ https://a-single-html-file.netlify.app/ 200!
```

* ✅ `/` is 200 
* ✅ `/index.html` is 301 to `/`
* 💔 `/dir` is 200 (want to redirect to `/dir/`)
* ✅ `/dir/` is 200
* ✅ `/dir/index.html` is 301 to `/dir/`

Same results when Pretty URLs Asset Optimization is enabled (found at `https://app.netlify.com/sites/SITE_NAME/settings/deploys`).


## Third test

Uses a `/localdir/index.html` local project file as a control.

```
/localredirect/index.html /localredirect/ 301!
/localredirect/ /localdir/ 200!
```

* ✅ `/localdir` is 301 to `/localdir/` (this is what we want the rewrite request to do)
* ✅ `/localdir/` is 200
* 💔 `/localredirect` is 200 (we want a 301 to `/localredirect/`)
* ✅ `/localredirect/` is 200
* ✅ `/localredirect/index.html` is 301 to `/localredirect/`
