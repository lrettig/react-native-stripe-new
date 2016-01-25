# React Native : Stripe SDK

Wraps the native [Stripe iOS SDK](https://github.com/stripe/stripe-ios) for React Native apps. 

## Features
- Collect credit card information and convert to a Stripe token, all in native code.
- Get billing and shipping information (name, address, phone number, email address) from Apple Pay.
- Fall back on simple Stripe native credit card form for older devices.
- All methods return promises.

## Installation

- Install the module:
```
npm i react-native-stripe --save
```
- Run ```open node_modules/react-native-stripe```
- Drag `StripeNative.xcodeproj` into your `Libraries` group
- Select your main project in the navigator to bring up settings
- Under `Build Phases` expand the `Link Binary With Libraries` header
- Scroll down and click the `+` to add a library
- Find and add `libStripeNative.a` under the `Workspace` group
- ⌘+B
- If you get an error about `Stripe.h`, delete the `Stripe.framework` in Xcode and reinstall it manually from https://stripe.com/docs/mobile/ios.

## Example
```javascript
var React = require('react-native');
var StripeNative = require('react-native-stripe');

const STRIPE_KEY = "<YOUR STRIPE KEY>";

const SOME_ITEMS = [
  {
    label: "Llama Kitty T-shirt",
    amount: 19.99,
  },
  {
    label: "Hello Kitty Humidifier",
    amount: 25.00,
  },
];

var AppEntry = React.createClass({

  componentDidMount: function () {
    StripeNative.init(STRIPE_KEY);
  },

  applePay: function () {
    Promise.all([StripeNative.canMakePayments(), StripeNative.canMakePaymentsUsingNetworks()]).then(
      function (canMakePayments) {
        if (!canMakePayments[0])
          alert("Apple Pay is not enabled on this device");
        else if (!canMakePayments[1])
          alert("Apple Pay is enabled but no card is configured");
        else {
          var options = {
            fallbackOnCardForm: false,
            shippingAddressFields: StripeNative.iOSConstants.PKAddressFieldAll,
          };
          StripeNative.paymentRequestWithApplePay(SOME_ITEMS, "Llama Kitty Shop", options).then(function (obj) {
            var token = obj[0],
              shippingInfo = obj[1],
              billingInfo = obj[2];
  
            // (Create charge here)
  
            (chargeWasSuccessful ? StripeNative.success : StripeNative.failure)();
          }, function (err) {
            alert(err);
          }.bind(this))
        }
      }.bind(this));
  },
});

```

## Sample application

- ```cd node_modules/react-native-stripe/example/```
- Edit `index.ios.js` and replace `<YOUR STRIPE KEY>` with your Stripe publishable key.
- ```npm install```
- ```react-native start```
- ```open ios/example.xcodeproj```
- ⌘+R to run the app in X-code.

## Limitations
- Currently only supports Android.
- Apple does not currently allow us to get any billing contact info other than a postal address.

## Copyright and license

Code and documentation copyright 2016 Lane M Rettig. Code released under [the MIT license](https://github.com/lrettig/react-native-stripe/blob/master/LICENSE).

[react-native]: http://facebook.github.io/react-native/
[stripe-sdk]: https://github.com/stripe/stripe-ios
