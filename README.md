This is a fork of the MideaUART project from https://github.com/dudanov/MideaUART. It is maintained to provide full compatibility with Midea U and Midea U+ air conditioners, including support for features and functionality specific to these models.

The ESPHome Midea climate component has the MideaUART library hardcoded to use the upstream repository. To use this fork, you must create a custom copy of the ESPHome Midea component and modify the library reference.

### 1. Modify the library reference

Change the following line:

**From:**

```python
cg.add_library("dudanov/MideaUART", "1.1.9")
```

**To:**

```python
cg.add_library("https://github.com/Airey001/MideaUART.git", None)
```

This instructs ESPHome to use this fork of the MideaUART library instead of the upstream version.

### 2. Configure your ESPHome YAML

Place your modified `midea` component in a local `components` directory within your ESPHome project, then add the following to your YAML configuration:

```yaml
external_components:
  - source:
      type: local
      path: components
    components:
      - midea
```

This tells ESPHome to use your local copy of the `midea` component instead of the built-in version, allowing it to load the modified MideaUART library.



# MideaUART
Arduino framework library for controlling Midea home appliances using the UART protocol.

Control is possible with a custom dongle. You can make it yourself according to numerous instructions on the Internet, or you can buy a ready-made one in [Tindie Shop](https://www.tindie.com/products/24607/), thereby supporting me and my work.

A far from complete list of supported brands:
1. [Midea](https://www.midea.com/)
2. [Electrolux](https://www.electrolux.ru/)
3. [Qlima](https://www.qlima.com/)
4. [Artel](https://www.artelgroup.com/)
5. [Carrier](https://www.carrier.com/)
6. [Comfee](http://www.comfee-russia.ru/)
7. [Inventor](https://www.inventorairconditioner.com/)
8. [Dimstal/Simando](https://www.simando24.de/)

## Using
It's simple.
1. Create appliance instance of `dudanov::midea::ac::AirConditioner`.
2. Set serial stream interface and communication mode to `9600 8N1`.
3. Add `setup()` and `loop()` methods to the same-named global functions of the project.
4. Control device via `void control(const Control &control)` with optional parameters.
5. You may optionally add your callback function for receive state changes notifications.

```cpp
#include <Arduino.h>
#include <Appliance/AirConditioner/AirConditioner.h>

using namespace dudanov::midea::ac;

AirConditioner ac;

// Example how can change work mode easily
static inline void switchMode(Mode mode) {
  Control control;
  control.mode = mode;
  ac.control(control);
}

// Example how can change mode and temp in same time
static inline void changeState(Mode mode, float targetTemp) {
  Control control;
  control.mode = mode;
  control.targetTemp = targetTemp;
  ac.control(control);
}

// Example how can change power state
static inline void setPowerState(bool state) {
  ac.setPowerState(state);
}

// Example how can change mode to AUTO and set target temp to 25C
static inline void changeAuto25() {
  Control control;
  control.mode = Mode::MODE_AUTO;
  control.targetTemp = 25.0f;
  ac.control(control);
}

// Here you may get new properties states
void onStateChange() {
  ac.getTargetTemp();
  ac.getIndoorTemp();
  ac.getMode();
  ac.getPreset();
  ac.getSwingMode();
  ac.getFanMode();
}

void setup() {
  Serial.begin(9600);     // set serial baudrate to 9600 8N1
  ac.setStream(&Serial);  // set stream serial interface
  ac.addOnStateCallback(onStateChange); // add callback
  ac.setup();
}

void loop() {
  ac.loop();
}
```

## My thanks

to the following people for their contributions to reverse engineering the UART protocol and source code in the following repositories:

* [Mac Zhou](https://github.com/mac-zhou/midea-msmart)
* [Rene Klootwijk](https://github.com/reneklootwijk/midea-uart)
* [NeoAcheron](https://github.com/NeoAcheron/midea-ac-py)

### Your thanks

If this project was useful to you, you can [buy me](https://paypal.me/dudan0v) a Cup of coffee :)
