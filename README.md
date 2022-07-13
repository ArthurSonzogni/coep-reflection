# COEP reflection

## Authors:
- Arthur Sonzogni

## Participate
This is a minor proposal. You are encouraged to discuss directly in the
[original HTML feature proposal](https://github.com/whatwg/html/issues/7912)

## External links:
- [Demo](https://coep-reflection.glitch.me/)
- [HTML original discussion](https://github.com/whatwg/html/issues/7912)
- [HTML specification PR](https://github.com/whatwg/html/pull/7948)
- [Security/Privacy questionnaire](./security-privacy-questionnaire.md)
- [w3ctag review](https://github.com/w3ctag/design-reviews/issues/742)
- [Chrome status](https://chromestatus.com/feature/5074103873568768)
- [Mozilla: request for position](https://github.com/mozilla/standards-positions/issues/645): ["it should be fine from our side"](https://github.com/mozilla/standards-positions/issues/645#issuecomment-1160546542).
- [Webkit: request for position](https://lists.webkit.org/pipermail/webkit-dev/2022-May/032258.html): ["Seems reasonable"](https://lists.webkit.org/pipermail/webkit-dev/2022-June/032259.html).

## Description 
Add the API:
```js
window.crossOriginEmbedderPolicy;
```
It reflects the environment's [cross-origin-embedder-policy](https://html.spec.whatwg.org/multipage/origin.html#coep)'s value.

The possibles values are: `unsafe-none`, `credentialless`, and `require-corp`.

The API exposed to window and workers.

## Motivating use cases

Depending on the Cross-Origin-Embedder-Policy (COEP) value, not every
iframe/subresources can be loaded inside the document.

For instance:
- If the document uses COEP, then its `<iframe>` need to use COEP too, or they
  will be blocked.
- If `COEP` is `require-corp` then cross-origin `no-cors` subresources are
  required to send the appropriate `Cross-Origin-Resource-Policy` header, or
  they will be blocked.

If this reflection API is provided, 1st party and 3rd party scripts can take
better decisions. They can implement appropriate fallbacks. For instance, this
was asked [here].

[here]: https://bugs.chromium.org/p/chromium/issues/detail?id=1335034#c10:~:text=actions.%20%0A%0AIn%20the%20meantime%2C%20what%20possible%20solutions%3F-,Is%20there%20a%20way%20to%20detect%20this%20kind%20of%20header,-and%20don%27t%20inject%20iFrames%20(without%20asking%20for

In particular, COEP reflection was originally part of the [Anonymous
Iframe](https://github.com/w3ctag/design-reviews/issues/639) proposal. Since it is useful on
its own, it became in independent mini-proposal.
To use anonymous iframe, third party script needs to know if the `<iframe>` they
are going to create will be blocked by COEP or not. By using COEP reflection,
they can decide to load an anonymous iframe instead of a normal one.

## Questions / Considered alternatives


### An HTTP header

The original plan was to use an HTTP header. This was initially part of
[Anonymous Iframe](https://github.com/w3ctag/design-reviews/issues/639)

A JavaScript API is preferred:
- Sending an HTTP header to every requests is potentially costly for the
  network. It is better if it can be avoided.
- Developers wanting to use the Origin Trial said they were prefering a
  JavaScript API instead and not have to configure their server.

### Different naming / location

The API was added inside the global object, similarly to `crossOriginIsolated`:
```js
window.crossOriginIsolated         [pre-existing]
window.crossOriginEmbedderPolicy   [new]
```

Should we continue adding API one by one, inside the global object?

A suggestion from @mikewest [here](https://github.com/whatwg/html/issues/7912#issuecomment-1123407921):
COEP is part of the [policy container](https://html.spec.whatwg.org/multipage/origin.html#policy-containers).
It might make sense to wrap them inside `window.policies` instead?

## Security/Privacy

This is a reflection API. It reflects the value the server set to its own
document, so there are no new information revealed to the document. It is just
easier to access using JavaScript.

Third party script can also use this API. It is useful to know what kind of
policy the current document/worker is subject to. Unless proven otherwise
knowing this is not a concern. Anyway, it is already deducible via other means,
by observing the effects of COEP. It should be polyfillable. As such, it should
be a security/privacy no-op.

Example of a polyfill:
- `fetch()` a known collaborating cross-origin URL in `no-cors` mode.
- The server reply with the request credentials and omits a CORP header.
- Set window.crossOriginEmbedderPolicy to:
  - `require-corp` if the response was blocked.
  - `credentialless` if the request omitted credentials.
  - `unsafe-none` otherwise.

See also the [Security/Privacy questionnaire](./security-privacy-questionnaire.md)

## Stakeholder Feedback / Opposition

- Chrome: Implemented [status](https://chromestatus.com/feature/5074103873568768)
- Mozilla: Positive ["it should be fine from our side"](https://github.com/mozilla/standards-positions/issues/645#issuecomment-1160546542). [Implementation bug](https://bugzilla.mozilla.org/show_bug.cgi?id=1774447)
- Safari: Positive ["Seems reasonable"](https://lists.webkit.org/pipermail/webkit-dev/2022-June/032259.html). [Implementation bug](https://bugs.webkit.org/show_bug.cgi?id=230550).

## Acknowledgements

Many thanks for anyone who provided feedbacks, discussion, code reviews or took
time to reply to requests:
- Alex Christensen
- [Anne van Kesteren](https://github.com/annevk)
- [Antonio Sartori](https://github.com/antosart)
- [Camille Lamy](https://github.com/camillelamy)
- [Domenic Denicola](https://github.com/domenic)
- [Mike West](https://github.com/mikewest)
- [Rossen Atanassov](https://github.com/atanassov)
- [Sean Feng](https://github.com/sefeng211)
