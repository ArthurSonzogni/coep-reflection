# [Self-Review Questionnaire: Security and Privacy](https://w3ctag.github.io/security-questionnaire/)

### 01.  What information might this feature expose to Web sites or other parties, and for what purposes is that exposure necessary?

It exposes the current environment's crossOriginEmbedderPolicy's value:
```js
window.crossOriginEmbedderPolicy = ('unsafe-none' | 'credentialless' | 'require-corp');
```

Depending on the Cross-Origin-Embedder-Policy value, not every
iframe/subresources can be loaded inside the document. By using this new API, 
3rd party script can take better decisions. They can implement appropriate
fallbacks.

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

### 02.  Do features in your specification expose the minimum amount of information necessary to enable their intended uses?

Yes. We expose a single value.

In particular, we do expose neither the
`Cross-Origin-Embedder-Policy-Report-Only` nor the potential report endpoints.

### 03.  How do the features in your specification deal with personal information, personally-identifiable information (PII), or information derived from them?

N/A. No PII.

### 04.  How do the features in your specification deal with sensitive information?

N/A. No sensitive informations.

### 05.  Do the features in your specification introduce new state for an origin that persists across browsing sessions?

It reflects the value of an existing state, it doesn't create a new one. Please
note that as part of the PolicyContainerHost, the value is inherited toward
local-scheme document (about:, blob:, etc). The PolicyContainer is stored into
the history, to correctly persist the inherited state during history
navigations.

### 06.  Do the features in your specification expose information about the underlying platform to origins?

No.

### 07.  Does this specification allow an origin to send data to the underlying platform?

No.

### 08.  Do features in this specification enable access to device sensors?

No.

### 09.  Do features in this specification enable new script execution/loading mechanisms?

No.

### 10.  Do features in this specification allow an origin to access other devices?

No.

### 11.  Do features in this specification allow an origin some measure of control over a user agent's native UI?

No.

### 12.  What temporary identifiers do the features in this specification create or expose to the web?

No identifiers.

### 13.  How does this specification distinguish between behavior in first-party and third-party contexts?

Both the document and its 3rd party script can access the value.

### 14.  How do the features in this specification work in the context of a browser???s Private Browsing or Incognito mode?

It's independent.

### 15.  Does this specification have both "Security Considerations" and "Privacy Considerations" sections?

Yes, here.

### 16.  Do features in your specification enable origins to downgrade default security protections?

No.

### 17.  How does your feature handle non-"fully active" documents?

It is independent. The value is constant throughout the whole lifetime of a
document.

### 18.  What should this questionnaire have asked?

It was great.
