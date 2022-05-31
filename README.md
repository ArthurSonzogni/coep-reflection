### External links:
- [HTML original discussion](https://github.com/whatwg/html/issues/7912)
- [HTML specification PR](https://github.com/whatwg/html/pull/7948)
- [Security/Privacy questionnaire](./security-privacy-questionnaire.md)
- [w3ctag review](https://github.com/w3ctag/design-reviews/issues/742)
- [Chrome status](https://chromestatus.com/feature/5074103873568768)
- [Mozilla: request for position](https://github.com/mozilla/standards-positions/issues/645)

### Description 
Add the API:
```js
self.crossOriginEmbedderPolicy;
```
It reflects the environment's [cross-origin-embedder-policy](https://html.spec.whatwg.org/multipage/origin.html#coep)'s value.

The possibles values are: `unsafe-none`, `credentialless`, and `require-corp`.

### Security/Privacy
It is already possible to deduce the value returned by the API, by making a
no-cors `fetch` request toward a known cross-origin URL whose response depends
on the request's cookies and omit the CORP headers.

It is costly, but theoretically polyfillable. As such, it should be a
security/privacy no-op.

See also the [Security/Privacy questionnaire](./security-privacy-questionnaire.md)

### Motivation

Depending on the Cross-Origin-Embedder-Policy value, not every
iframe/subresources can be loaded inside the document. If this reflection API is
provided, 3rd party scripts can take better decisions. They can implement
fallbacks.

In particular, for trying [Anonymous
Iframe](https://github.com/WICG/anonymous-iframe), Google DisplayAds, needs a
way to know the COEP policy. The Ads's script could this way be able to decide
in between inserting a normal or an anonymous iframe.

### Questions

The API was added inside the global object, similarly to `crossOriginIsolated`:
```js
window.crossOriginIsolated
window.crossOriginEmbedderPolicy
```

Should we continue adding API one by one, inside the global object?

A suggestion from @mikewest [here](https://github.com/whatwg/html/issues/7912#issuecomment-1123407921):
COEP is part of the [policy container](https://html.spec.whatwg.org/multipage/origin.html#policy-containers).
It might make sense to wrap them inside `window.policies` instead?
