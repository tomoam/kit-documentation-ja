---
title: Routing
---

At the heart of SvelteKit is a *filesystem-based router*. This means that the structure of your application is defined by the structure of your codebase — specifically, the contents of `src/routes`.

> You can change this to a different directory by editing the [project config](#configuration).

There are two types of route — **pages** and **endpoints**.

### Pages

Pages are Svelte components written in `.svelte` files (or any file with an extension listed in [`config.extensions`](#configuration)). When a user first visits the application, they will be served a server-rendered version of the page in question, plus some JavaScript that 'hydrates' the page and initialises a client-side router. From that point forward, navigating to other pages is handled entirely on the client for a fast, app-like feel.

The filename determines the route. For example, `src/routes/index.svelte` is the root of your site:

```html
<!-- src/routes/index.svelte -->
<svelte:head>
	<title>Welcome</title>
</svelte:head>

<h1>Hello and welcome to my site!</h1>
```

A file called either `src/routes/about.svelte` or `src/routes/about/index.svelte` would correspond to the `/about` route:

```html
<!-- src/routes/about.svelte -->
<svelte:head>
	<title>About</title>
</svelte:head>

<h1>About this site</h1>
<p>TODO...</p>
```

Dynamic parameters are encoded using `[brackets]`. For example, a blog post might be defined by `src/routes/blog/[slug].svelte`. Soon, we'll see how to access that parameter in a [load function](#loading) or the [page store](#modules-app-stores).

### Endpoints

Endpoints are modules written in `.js` (or `.ts`) files that export functions corresponding to HTTP methods. Each function receives HTTP `request` and `context` objects as arguments. For example our hypothetical blog page, `/blog/cool-article`, might request data from `/blog/cool-article.json`, which could be represented by a `src/routes/blog/[slug].json.js` endpoint:

```ts
type Request = {
	host: string;
	method: 'GET';
	headers: Record<string, string>;
	path: string;
	params: Record<string, string | string[]>;
	query: URLSearchParams;
	body: string | Buffer | ReadOnlyFormData;
};

type Response = {
	status?: number;
	headers?: Record<string, string>;
	body?: any;
};
```

```js
import db from '$lib/database';

/**
 * @param {import('@sveltejs/kit').Request} request
 * @param {any} context
 * @returns {import('@sveltejs/kit').Response}
 */
export async function get(request, context) {
	// the `slug` parameter is available because this file
	// is called [slug].json.js
	const { slug } = request.params;

	const article = await db.get(slug);

	if (article !== null) {
		return {
			body: {
				article
			}
		};
	} else {
		return {
			status: 404,
			body: {
				error: 'Not found'
			}
		};
	}
}
```

Because this module only runs on the server (or when you build your site, if [prerendering](#prerendering)), you can freely access things like databases. (Don't worry about `$lib`, we'll get to that [later](#$lib).)

The second argument, `context`, is something you define during [setup](#setup), if necessary.

The job of this function is to return a `{status, headers, body}` object representing the response. If the returned `body` is an object, and no `content-type` header is returned, it will automatically be turned into a JSON response.

For endpoints that handle other HTTP methods, like POST, export the corresponding function:

```js
export function post(request, context) {...}
```

Since `delete` is a reserved word in JavaScript, DELETE requests are handled with a `del` function.

> We don't interact with the `req`/`res` objects you might be familiar with from Node's `http` module or frameworks like Express, because they're only available on certain platforms. Instead, SvelteKit translates the returned object into whatever's required by the platform you're deploying your app to.
>
> The `body` property of the request object exists in the case of POST requests. If you're posting form data, it will be a read-only version of the [`FormData`](https://developer.mozilla.org/en-US/docs/Web/API/FormData) object.


### Private modules

A filename that has a segment with a leading underscore, such as `src/routes/foo/_Private.svelte` or `src/routes/bar/_utils/cool-util.js`, is hidden from the router, but can be imported by files that are not.


### Advanced

A route can have multiple dynamic parameters, for example `src/routes/[category]/[item].svelte` or even `src/routes/[category]-[item].svelte`. If the number of route segments is unknown, you can use rest syntax — for example you might implement GitHub's file viewer like so...

```bash
/[org]/[repo]/tree/[branch]/[...file]
```

...in which case a request for `/sveltejs/kit/tree/master/documentation/docs/01-routing.md` would result in the following parameters being available to the page:

```js
{
	org: 'sveltejs',
	repo: 'kit',
	branch: 'master',
	file: ['documentation', 'docs', '01-routing.md']
}
```

Finally, you can use a subset of regular expression syntax to control whether routes match or not:

```bash
# matches /2021/04/25 but not /a/b/c or /1/2/3
src/routes/[year(\d{4})]/[month(\d{2})]/[day(\d{2})].svelte
```

Because of technical limitations, the following characters cannot be used: `/`, `\`, `?`, `:`, `(` and `)`.

