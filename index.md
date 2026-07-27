# Audio Spectrum Visualizer
This project is an Arduino powered audio visualizer that transforms live sound into moving patterns on a 32×8 LED matrix display. By combining a microphone sensor, real-time signal processing, and an animated display, it turns music and other sounds into a visual experience

You should comment out all portions of your portfolio that you have not completed yet, as well as any instructions:
```HTML 
<!--- This is an HTML comment in Markdown -->
<!--- Anything between these symbols will not render on the published site -->
```

| **Engineer** | **School** | **Area of Interest** | **Grade** |
|:--:|:--:|:--:|:--:|
| Adrian F | Terra Linda High School | Mechanical Engineering | Incoming Senior

**Replace the BlueStamp logo below with an image of yourself and your completed project. Follow the guide [here](https://tomcam.github.io/least-github-pages/adding-images-github-pages-site.html) if you need help.**

![Headstone Image](logo.svg)
  
# Final Milestone

**Don't forget to replace the text below with the embedding for your milestone video. Go to Youtube, click Share -> Embed, and copy and paste the code to replace what's below.**

<iframe width="560" height="315" src="https://www.youtube.com/embed/F7M7imOVGug" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" allowfullscreen></iframe>

For your final milestone, explain the outcome of your project. Key details to include are:
- What you've accomplished since your previous milestone
- What your biggest challenges and triumphs were at BSE
- A summary of key topics you learned about
- What you hope to learn in the future after everything you've learned at BSE



# Second Milestone

**Don't forget to replace the text below with the embedding for your milestone video. Go to Youtube, click Share -> Embed, and copy and paste the code to replace what's below.**

<iframe width="560" height="315" src="https://www.youtube.com/embed/y3VAmNlER5Y" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" allowfullscreen></iframe>

For your second milestone, explain what you've worked on since your previous milestone. You can highlight:
- Technical details of what you've accomplished and how they contribute to the final goal
- What has been surprising about the project so far
- Previous challenges you faced that you overcame
- What needs to be completed before your final milestone 

# First Milestone


<iframe width="560" height="315" src="https://www.youtube.com/embed/ZX0E4HqxcDc?si=fk7Na8wRHeSnDecw" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>



For my first milestone, I created an audio visualizer using an Arduino Uno R3, a microphone sensor, and a 32×8 LED dot matrix display. The microphone detects sound and sends an analog signal to the Arduino, which processes the audio using a Fast Fourier Transform and displays the results as moving frequency bars on the LED matrix.

So far, I have completed the wiring, tested each component, and programmed the display to react to music. One challenge has been improving the microphone’s sensitivity and making the visualizer respond accurately to quieter sounds. My next steps are to refine the code, improve the audio input, organize the wiring, and build an enclosure for the finished project. In order to organize the wiring and improve the audio input, I plan on adding a case and an upgraded sound sensor.

# Schematic 

<p align="center">
  <img
    src="audio-visualizer-schematic.png"
    alt="Wokwi circuit diagram for the audio visualizer"
    width="650">
</p>

# Code
Here's where you'll put your code. The syntax below places it into a block of code. Follow the guide [here]([url](https://www.markdownguide.org/extended-syntax/)) to learn how to customize it to your project needs. 

```c++
#include <arduinoFFT.h>
#include <MD_MAX72xx.h>
#include <SPI.h>

const uint8_t CS_PIN = 10;
const uint8_t MAX_DEVICES = 4;

MD_MAX72XX disp(
  MD_MAX72XX::FC16_HW,
  CS_PIN,
  MAX_DEVICES
);

const uint8_t SOUND_PIN = A0;

const uint16_t SAMPLES = 64;

const double SAMPLING_FREQUENCY = 4000.0;

const unsigned long SAMPLING_PERIOD_US = 250;

const int MAGNITUDE_MAX = 520;

double realComponent[SAMPLES];
double imagComponent[SAMPLES];

ArduinoFFT<double> FFT = ArduinoFFT<double>(
  realComponent,
  imagComponent,
  SAMPLES,
  SAMPLING_FREQUENCY
);

const uint8_t spectralHeight[9] = {
  0b00000000,
  0b10000000,
  0b11000000,
  0b11100000,
  0b11110000,
  0b11111000,
  0b11111100,
  0b11111110,
  0b11111111
};

void setup() {
  Serial.begin(9600);

  disp.begin();
  disp.control(MD_MAX72XX::INTENSITY, 3);
  disp.clear();
}

void loop() {
  for (uint16_t i = 0; i < SAMPLES; i++) {
    unsigned long sampleStart = micros();

    realComponent[i] = analogRead(SOUND_PIN);
    imagComponent[i] = 0.0;

    while (micros() - sampleStart < SAMPLING_PERIOD_US) {
    }
  }

  FFT.dcRemoval();

  FFT.windowing(
    FFTWindow::Hamming,
    FFTDirection::Forward
  );

  FFT.compute(FFTDirection::Forward);
  FFT.complexToMagnitude();

  for (uint8_t i = 0; i < 32; i++) {
    uint8_t level = 0;

    if (i > 0) {
      int magnitude = (int)realComponent[i];

      magnitude = constrain(
        magnitude,
        0,
        MAGNITUDE_MAX
      );

      level = map(
        magnitude,
        0,
        MAGNITUDE_MAX,
        0,
        8
      );
    }

    uint8_t column = 31 - i;

    disp.setColumn(
      column,
      spectralHeight[level]
    );
  }

  Serial.println((int)realComponent[1]);
}
```

# Materials

| **Part** | **Note** | **Price** | **Link** |
|:--:|:--:|:--:|:--:|
| Arduino Uno R3 | Processes the microphone signal and controls the display | $9.98 | [Link](https://www.amazon.com/dp/B0FKN4T68T/) |
| MAX4466 Microphone Amplifier w/ Adjustable Gain | Amplifies sound and sends a stronger, adjustable analog audio signal to the Arduino | $8.39 for 2 | [Link](https://www.amazon.com/dp/B07DRGF8C2/) |
| 32x8 LED Dot Matrix Display | Displays the audio spectrum as LED bars | $8.99 | [Link](https://www.amazon.com/dp/B07W1WCXXS/) |
| Case for Audio Visualizer | Case to hold all components together | Needs to be 3d printed | [Link](https://www.thingiverse.com/thing:4976485) |


# Other Resources/Examples
One of the best parts about Github is that you can view how other people set up their own work. Here are some past BSE portfolios that are awesome examples. You can view how they set up their portfolio, and you can view their index.md files to understand how they implemented different portfolio components.
- [Resource 1](https://circuitdigest.com/microcontroller-projects/diy-music-audio-visualizer-using-dot-matrix-display-and-arduino-nano)
- [Resource 2](https://www.youtube.com/watch?v=pHw3AokxRXM)
- [Example 3](https://arneshkumar.github.io/arneshbluestamp/)
