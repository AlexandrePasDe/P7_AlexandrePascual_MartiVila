# P7_AlexandrePascual_MartiVila
Participants: Alexandre Pascual i Marti Vila

# Reproducció d'audio 


### Objectiu

Treballarem en la reproducció d'audio amb la ESP32-S3 afegint llibreries especifiques per a la reproducció d'arxius d'audio.

## Part 1

### *Codi*

```c++
#include "AudioGeneratorAAC.h"
#include "AudioOutputI2S.h"
#include "AudioFileSourcePROGMEM.h"
#include "sampleaac.h"
AudioFileSourcePROGMEM *in;
AudioGeneratorAAC *aac;
AudioOutputI2S *out;
void setup(){
Serial.begin(115200);
in = new AudioFileSourcePROGMEM(sampleaac, sizeof(sampleaac));
aac = new AudioGeneratorAAC();
out = new AudioOutputI2S();
out -> SetGain(0.125);
out -> SetPinout(7, 6, 4);
aac->begin(in, out);
}
void loop(){
if (aac->isRunning()) {
aac->loop();
} else {
aac -> stop();
Serial.printf("Sound Generator\n");
delay(1000);
}
}

```
### **Explicació**

Utilitzatnt 'PROGMEM', reproduim un audio que esta a la memoria de la pròpia ESP32-S3 afegint-hi les llibreries necessàries per a la descodificació AAC i la sortida per el bus **I2S**.
Les llibreires són les següents:

1. AudioFileSourcePROGMEM: Permet llegir arxius d'audio en la memoria flash.
2. AudioGeneratorAAC: Decodifica l'arxiu en format AAC.
3. AudioOutputI2S: Controla la sortida d'audio amb el protocolo I2S.
4. sampleaac: Es l'arxiu d'audio.

Aquest codi configura els pins de sortida per l'I2S en 7,6 i 4. 
Repdrodueix l'audio i es manté en bucle durant es reprodueix i, quan acaba, para i espera 1 segon.
   


## Part 2

### *Codi*

```c++

#include "Audio.h"
#include "SD.h"
#include "FS.h"
// Digital I/O used
#define SD_CS 10
#define SPI_MOSI 11
#define SPI_MISO 13
#define SPI_SCK 12
#define I2S_DOUT 4
#define I2S_BCLK 7
#define I2S_LRC 6
Audio audio;
void setup(){
pinMode(SD_CS, OUTPUT);
digitalWrite(SD_CS, HIGH);
SPI.begin(SPI_SCK, SPI_MISO, SPI_MOSI);
Serial.begin(115200);
SD.begin(SD_CS);
audio.setPinout(I2S_BCLK, I2S_LRC, I2S_DOUT);
audio.setVolume(10); // 0...21
audio.connecttoFS(SD, "prova.mp3");
}
void loop(){
audio.loop();
}
// optional
void audio_info(const char *info){
Serial.print("info "); Serial.println(info);
}
void audio_id3data(const char *info){ //id3 metadata
Serial.print("id3data ");Serial.println(info);
}
void audio_eof_mp3(const char *info){ //end of file
Serial.print("eof_mp3 ");Serial.println(info);
}
void audio_showstation(const char *info){
Serial.print("station ");Serial.println(info);
}
void audio_showstreaminfo(const char *info){
  Serial.print("streaminfo ");Serial.println(info);
  }
  void audio_showstreamtitle(const char *info){
  Serial.print("streamtitle ");Serial.println(info);
  }
  void audio_bitrate(const char *info){
  Serial.print("bitrate ");Serial.println(info);
  }
  void audio_commercial(const char *info){ //duration in sec
  Serial.print("commercial ");Serial.println(info);
  }
  void audio_icyurl(const char *info){ //homepage
  Serial.print("icyurl ");Serial.println(info);
  }
  void audio_lasthost(const char *info){ //stream URL played
  Serial.print("lasthost ");Serial.println(info);
  }
  void audio_eof_speech(const char *info){
  Serial.print("eof_speech ");Serial.println(info);
  }

```

### **Explicació**

Reproduïm un arxiu de tipus MP3 a una tarjeta SD configurant els pins SPI, per al accés a la tarjeta, i I2S, per a la sortida d'audio.
El SD.begin inicialitza la tarjeta, el connecttoFS connecta l'arxiu MP3 i el bucle manté la reproducció.




