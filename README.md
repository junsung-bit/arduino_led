# arduino_led
## fritzing 연결도
<img width="1337" height="670" alt="image" src="https://github.com/user-attachments/assets/8395a9aa-870b-4ccc-a1a6-9d95b1ce3f4f" />

## Arduino 스케치

```cpp
const int LED_PIN = 6;

void setup() {
  Serial.begin(9600);
  pinMode(LED_PIN, OUTPUT);
}

void loop() {
  if (Serial.available() > 0) {
    analogWrite(LED_PIN, Serial.read());
  }
}
```

## p5.js 스케치

```js
let slider, btn, writer = null, last = -1;

function setup() {
  createCanvas(400, 220);
  btn = createButton('아두이노 연결');
  btn.mousePressed(connect);
  slider = createSlider(0, 255, 0);
  slider.style('width', '300px');
}

function draw() {
  background(30);
  const v = slider.value();
  noStroke();
  fill(v, v * 0.15, v * 0.15);
  circle(width / 2, 90, 110);
  fill(255);
  textAlign(CENTER);
  textSize(18);
  text('밝기 ' + v + ' / 255', width / 2, 195);
  if (v !== last) { send(v); last = v; }
}

async function connect() {
  try {
    const port = await navigator.serial.requestPort();
    await port.open({ baudRate: 9600 });
    writer = port.writable.getWriter();
    last = -1;
  } catch (e) {}
}

async function send(v) {
  if (!writer) return;
  try { await writer.write(new Uint8Array([v])); }
  catch (e) { writer = null; }
}
```

## 동작 원리

1. 슬라이더 값(0~255)이 바뀔 때마다 Web Serial로 1바이트를 Arduino에 보냅니다. (9600 baud)
2. Arduino는 `Serial.read()`로 받은 값을 `analogWrite(6, 값)`에 넣어 LED 밝기를 바꿉니다.
3. `analogWrite`는 PWM으로, 5V를 아주 빠르게 켰다 껐다 하면서 켜져 있는 시간의 비율(듀티비)로 밝기를 조절합니다. 그래서 PWM 지원 핀(`~`)인 6번을 사용했습니다.

## 동작 영상



https://github.com/user-attachments/assets/67e79656-0f18-4117-88e5-f93bb873a31d

