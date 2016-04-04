braintree-web
=============

__NOTE: v3 of our JavaScript SDK is currently in closed beta. If you are interested in using this new major version and not already part of the closed beta, [join our Google Group](https://groups.google.com/forum/#!forum/braintree-sdk-announce) for updates on the public beta (coming soon).__

A suite of tools for integrating Braintree in the browser.

This is the repo to submit issues if you have any problems or questions about any v.zero JavaScript integration.

Install
=======

```
npm install braintree-web@3.0.0-beta.3
```

```
bower install braintree-web@3.0.0-beta.3
```

Usage
=====

For more thorough documentation, visit [the JavaScript client SDK docs](https://developers.braintreepayments.com/guides/client-sdk/javascript/v3).

####Hosted Fields integration

```html
<form action="/" id="my-sample-form">
  <input type="hidden" name="payment_method_nonce">
  <label for="card-number">Card Number</label>
  <div id="card-number"></div>

  <label for="cvv">CVV</label>
  <div id="cvv"></div>

  <label for="expiration-date">Expiration Date</label>
  <div id="expiration-date"></div>

  <input id="my-submit" type="submit" value="Pay" disabled/>
</form>
```

```javascript
var submitBtn = document.getElementById('my-submit');
var form = document.getElementById('my-sample-form');

braintree.client.create({
  authorization: 'YOUR_CLIENT_TOKEN'
}, clientDidCreate);

function clientDidCreate(err, client) {
  braintree.hostedFields.create({
    client: client,
    styles: {
      'input': {
        'font-size': '16pt',
        'color': '#3A3A3A'
      },

      '.number': {
        'font-family': 'monospace'
      },

      '.valid': {
        'color': 'green'
      }
    },
    fields: {
      number: {
        selector: '#card-number'
      },
      cvv: {
        selector: '#cvv'
      },
      expirationDate: {
        selector: '#expiration-date'
      }
    }
  }, hostedFieldsDidCreate);
});

function hostedFieldsDidCreate(err, hostedFields) {
  submitBtn.addEventListener('click', submitHandler.bind(null, hostedFields));
  submitBtn.removeAttribute('disabled');
}

function submitHandler(hostedFields, event) {
  event.preventDefault();
  submitBtn.setAttribute('disabled', 'disabled');

  hostedFields.tokenize(function (err, payload) {
    if (err) {
      submitBtn.removeAttribute('disabled');
      console.error(err);
    } else {
      form['payment_method_nonce'].value = payload.nonce;
      form.submit();
    }
  });
}
```

#### Advanced integration

```javascript
braintree.client.create({
  authorization: 'YOUR_CLIENT_TOKEN'
}, function (err, client) {
  client.request({
    endpoint: 'payment_methods/credit_cards',
    method: 'post',
    data: {
      creditCard: {
        number: '4111111111111111',
        expirationDate: '10/20'
      }
    }
  }, function (err, response) {
    // Send response.creditCards[0].nonce to your server
  });
});
```
