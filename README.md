# WinkLightToggle
Guide on how to programmatically toggle a Wink light

## The code
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
