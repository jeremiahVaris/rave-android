# Rave's Android Drop In UI

Rave's Android Drop-In is a readymade UI that allows you to accept card and bank payments in your Android app.

<img alt="Screenshot of Drop-In" src="https://firebasestorage.googleapis.com/v0/b/saveup-9e594.appspot.com/o/Group.png?alt=media&token=e0c89192-b2a4-47e0-a883-3a78005acd2a" width="600"/>

## Before you begin
- [Create your Rave staging keys from the sandbox environment](https://flutterwavedevelopers.readme.io/blog/how-to-get-your-staging-keys-from-the-rave-sandbox-environment)
- [Create your Rave live keys from the Rave Dashboard](https://flutterwavedevelopers.readme.io/blog/how-to-get-your-live-keys-from-the-rave-dashboard)

## Requirements


The minimum supported SDK version is 15

## Adding it to your project


**Step 1.** Add it in your root build.gradle at the end of repositories:

    allprojects {
		repositories {
			...
			maven { url 'https://jitpack.io' }
		}
	}

**Step 2.** Add the dependency

    dependencies {
	     compile 'com.github.Flutterwave:rave-android:1.0.20'
	}

**Step 3.** Add the required permission

Add the `READ_PHONE_PERMISSION` and `INTERNET` permissions to your android manifest

     <uses-permission android:name="android.permission.READ_PHONE_STATE" />
     <uses-permission android:name="android.Manifest.permission.READ_PHONE_STATE" />
     <uses-permission android:name="android.permission.INTERNET" />

> **REQUIRED PERMISSION**
> This library requires the **READ_PHONE_PERMISSION** to get the build number for fraud detection and flagging as recommended here https://developer.android.com/training/articles/user-data-ids.html#i_abuse_detection_detecting_high_value_stolen_credentials

## Usage

###  1. Create a `RavePayManager` instance
Set the public key, private key and other required parameters. The `RavePayManager` accepts a mandatory instance of  the calling `Activity`

        new RavePayManager(activity).setAmount(amount)
                        .setCountry(country)
                        .setCurrency(currency)
                        .setEmail(email)
                        .setfName(fName)
                        .setlName(lName)
                        .setNarration(narration)
                        .setPublicKey(publicKey)
                        .setSecretKey(secretKey)
                        .setTxRef(txRef)
                        .acceptAccountPayments(boolean)
                        .acceptCardPayments(boolean)
                        .onStagingEnv(boolean)
                        .allowSaveCardFeature(boolean)
                        .setMeta(List<Meta>)
                        .withTheme(styleId)
                        .initialize();

| function        | parameter           | type | required  |
| ------------- |:-------------:| -----:| -----:|
| setAmount(amount)      |  This is the amount to be charged from card/account | `double` | Required
| setCountry(country)     | This is the route country for the transaction with respect to the currency. You can find a list of supported countries and currencies [here](https://flutterwavedevelopers.readme.io/docs/multicurrency-payments) | `String` | Required
| setCurrency(currency) | This is the specified currency to charge the card in | `String` | Required
| setfName(fName) | This is the first name of the card holder or the customer  | `String` | Required
| setlName(lName) | This is the last name of the card holder or the customer | `String` | Required
| setEmail(email) | This is the email address of the customer | `String` | Required
| setNarration(narration) | This is a custom description added by the merchant | `String` | Not Required
| setPublicKey(publicKey) | Merchant's public key | `String` | Required
| setSecretKey(secretKey) | Merchant's secret key | `String` | Required
| setTxRef(txRef) | This is the unique reference, unique to the particular transaction being carried out. It is generated by the merchant for every transaction | `String` | Required
| acceptAccountPayments(boolean) | Set to `true` if you want to accept payments via cards, else set to `false`. | `boolean` | Not Required
| acceptCardPayments(boolean) | Set to `true` if you want to accept payments via bank accounts, else set to `false` | `boolean` | Not Required |
| onStagingEnv(boolean) | Set to `true` if you want your transactions to run in the staging environment otherwise set to `false`. Defaults to false  | `boolean` | Not Required
| allowSaveCardFeature(boolean) | Set to `true` if you want your users to be able to save their cards for later reuse otherwise set to `false` | `boolean` | Required
| setMeta(`List<Meta>`) | Pass in any other custom data you wish to pass. It takes in a `List` of `Meta` objects | List<Meta> | Not Required
| withTheme(styleId) | Sets the theme of the UI. | `int` | Not Required
| setPaymentPlan(payment_plan) | If you want to do recurrent payment, this is the payment plan ID to use for the recurring payment, you can see how to create payment plans [here](https://flutterwavedevelopers.readme.io/v2.0/reference#create-payment-plan) and [here](https://flutterwavedevelopers.readme.io/docs/recurring-billing). This is only available for card payments | `String` | Not Required
| initialize() | Launch the Rave Payment UI  |  N/A | Required

> **SECURITY ALERT**
> You should never store your **SECRET KEY** on the user's device

###  2. Handle the response
In the calling activity, override the `onActivityResult` method to receive the payment response as shown below

    @Override
    protected void onActivityResult(int requestCode, int resultCode, Intent data) {
        if (requestCode == RaveConstants.RAVE_REQUEST_CODE && data != null) {
            String message = data.getStringExtra("response");
            if (resultCode == RavePayActivity.RESULT_SUCCESS) {
                Toast.makeText(this, "SUCCESS " + message, Toast.LENGTH_SHORT).show();
            }
            else if (resultCode == RavePayActivity.RESULT_ERROR) {
                Toast.makeText(this, "ERROR " + message, Toast.LENGTH_SHORT).show();
            }
            else if (resultCode == RavePayActivity.RESULT_CANCELLED) {
                Toast.makeText(this, "CANCELLED " + message, Toast.LENGTH_SHORT).show();
            }
        }
        else {
            super.onActivityResult(requestCode, resultCode, data);
        }
    }
The intent's `message` object contains the raw JSON response from the Rave API. This can be parsed to retrieve any additional payment information needed.

###  3. Customize the look
You can apply a new look by changing the color of certain parts of the UI to highlight your brand colors

    <style name="DefaultTheme" parent="AppTheme.NoActionBar">
        <item name="colorPrimary">@color/colorPrimary</item>
        <item name="colorPrimaryDark">@color/colorPrimaryDark</item>
        <item name="colorAccent">@color/colorAccent</item>
        <item name="OTPButtonStyle">@style/otpBtnStyle</item>
        <item name="PayButtonStyle">@style/payBtnStyle</item>
        <item name="PinButtonStyle">@style/pinButtonStyle</item>
        <item name="OTPHeaderStyle">@style/otpHeaderStyle</item>
        <item name="TabLayoutStyle">@style/tabLayoutStyle</item>
        <item name="PinHeaderStyle">@style/pinHeaderStyle</item>
        <item name="SavedCardButtonStyle">@style/svdCardsBtnStyle</item>
    </style>

##  Help
* Have issues integrating? Join our [Slack community](https://join.slack.com/t/flutterwavedevelopers/shared_invite/enQtMjU2MjkyNDM5MTcxLWFlOWNlYmE5MTIxNjAwYzc5MDVjZjNhYTJjNTA0ZTQyNDJlMDhhZjJkN2QwZGJmNWMyODhlYjMwNGUyZDQxNTE) for support
* Find a bug? [Open an issue](https://github.com/Flutterwave/rave-android/issues)
* Want to contribute? [Check out contributing guidelines]() and [submit a pull request](https://help.github.com/articles/creating-a-pull-request).

## Want to contribute?

Feel free to create issues and pull requests. The more concise the pull requests the better :)


## License

```
Rave's Android DropIn UI
MIT License

Copyright (c) 2017

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all
copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
SOFTWARE.
```