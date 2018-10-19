# Wink Light Toggle

## TL;DR
If you're looking for a quick and easy way to add Wink light toggling to your home-automation setup, you're in the right place.

## What
Guide on how to programmatically toggle a Wink light

## Why
The Wink API (including Shortcuts and Robots) doesn't offer the ability to toggle a light's state. Searching 3rd party apps such as
IFTTT also only have the ability to turn lights either on, or off.

## How
### Prerequisites
* A [Wink](http://wink.com) account
* A [Zapier](http://zapier.com) account
* A [Flic](http://flic.io) account **(optional)**

### Create a Wink developer account
Go to the [Wink Developer Portal](https://developer.wink.com) and create an account

### Create a Wink application
1. Hit the **New** button in the _Your Applications_
2. Enter a _Name_ for your application
3. Enter a _Website_ for your application, this can be anything
4. Enter a redirect URI for the application (this will be used later for auth), I used https://127.0.0.1
5. Agree to the terms and conditions and save the application
6. Wait 5-10 minutes for the application to be approved

### Get an authentication token
Using your application's OAuth URLs, attain a valid token. Your token will be valid for approximately 40 days, after this you will need to get a new token.

// TODO: Explain how to do this

### Find your device ID
// TODO

### Integrate with Zapier
This step will use a Flic button as the example, but you can modify the steps and code to suite another Zapier trigger

1. Create a new _Zap!_ with the following settings
    * **Connect this app...** Flic -> Code by Zapier
    * **When this happens...** New Flic Press -> Run Javascript
2. Select your Flic account
3. Choose your Flic button and the click type you want (you can leave as **Any** since the code checks)
4. **(Optional)** Pull in sample clicks from your account (this helps with the sample `inputData` object)
5. Choose **Code** as the action
6. Choose **Run Javascript**
7. In the **Input Data** section create a variable named `clickType` and associate it with **Click Type** from the choices drop down
8. Paste the following code into the _Code_ box
    ```javascript
    if (inputData.clickType !== "click") return {};

    const authToken = "YOUR_TOKEN";
    const deviceId = "YOUR_DEVICE_ID";
    const lightState = await (await fetch(`https://api.wink.com/light_bulbs/${deviceId}`, {
      headers: {
        Authorization: `Bearer ${authToken}`
      }
    })).json();

    if (lightState) {
      const newState = {
        powered: !lightState.data.last_reading.powered,
        brightness: 1
      };
      await fetch(`https://api.wink.com/light_bulbs/${deviceId}/desired_state`, {
        method: "PUT",
        headers: {
          Authorization: `Bearer ${authToken}`,
          "content-type": "application/json"
        },
        body: JSON.stringify({ desired_state: newState })
      });
    }

    return {};
    ```
9. Replace `YOUR_TOKEN` with your authentication token
10. Replace `YOUR_DEVICE_ID` with your device's ID
11. Press **Continue**
12. Press **Send Test To Code by Zapier** (or **Skip Test**)
13. Press **Finish**
14. Name your _Zap_ and switch it on
15. You can now toggle your light using your Flic button (Or other service that you integrated with)

### Working with Switches
// TODO

## Caveats
Due to the number of services between the trigger and the light, the time from trigger activation, to the light responding can be rather long (~5-10 seconds). This could be sped up a little by using a local server rather than Zapier, however using Zapier is a great option for those who don't want to run their own server.
