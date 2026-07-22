#include <Adafruit_GFX.h>
#include <MCUFRIEND_kbv.h>
#include <TouchScreen.h>

MCUFRIEND_kbv tft;

// Твои точные пины из лога калибратора
const int XP = 8, XM = A2, YP = A3, YM = 9; 
TouchScreen ts = TouchScreen(XP, YP, XM, YM, 300);

const int TS_LEFT = 75, TS_RT = 889, TS_TOP = 939, TS_BOT = 120;

// Пины драйвера DRV8825 и периферии
const int stepPin = 22; 
const int dirPin = 23;  
const int enablePin = 24;           // Умное управление током удержания
const int ledPin = 45;              // Силовой ШИМ-выход на полевик IRLZ44N (Лампа 10W)
const int stepsPerRevolution = 800; // Твоя рабочая константа (1/4 шаг)

// Переменные логики
long frameCount = 0;         
int framesPerSecond = 2;     // Выбор скорости для AUTO режима (1..4 FPS)
int lagDelayMillis = 200;    // Задержка видеопотока для ПК (50..400 мс)
bool isRunning = false;
bool waitForPC = false;      
bool isAutoMode = true;      
bool isLightOn = false;      // Флаг постоянной подсветки кадра в полнакала
bool wasLightTouched = false; // Флаг фиксации полного отпускания кнопки LIGHT
unsigned long lastFrameTime = 0;
int stepDelayMicros = 200; 

// Переменные таймера времени
unsigned long totalElapsedTime = 0; 
unsigned long sessionStartTime = 0; 

// Цвета
#define BLACK   0x0000
#define WHITE   0xFFFF
#define BLUE    0x001F
#define RED     0xF800
#define GREEN   0x07E0
#define GRAY    0x52AA
#define YELLOW  0xFFE0

// Прототипы функций
void drawInterface();
void handleTouch(int x, int y);
void updateStatusText();
void updateCounters();
void updateTimeDisplay(); 
void updateModeButton();
void updateLagText();
void updateControlButtons(); 
void rotateMotor(bool forward);
void recalculateSpeedSettings();

void setup() {
  Serial.begin(9600);
  
  pinMode(stepPin, OUTPUT);
  pinMode(dirPin, OUTPUT);
  pinMode(enablePin, OUTPUT);
  pinMode(ledPin, OUTPUT);
  
  digitalWrite(dirPin, LOW);     // Вращение против часовой стрелки по умолчанию
  digitalWrite(enablePin, HIGH); // Сразу обесточиваем мотор при старте
  analogWrite(ledPin, 0);        // Сразу гасим 10W диод

  uint16_t ID = tft.readID();
  if (ID == 0xD3D3) ID = 0x9341; 
  tft.begin(ID);
  tft.setRotation(1); 
  
  recalculateSpeedSettings();
  drawInterface();
}

void loop() {
  // 1. Опрос тачскрина
  TSPoint p = ts.getPoint();
  pinMode(XM, OUTPUT); pinMode(YP, OUTPUT); pinMode(XP, OUTPUT); pinMode(YM, OUTPUT);
  
  if (p.z > 30 && p.z < 1000) {
    int touchX = map(p.y, TS_LEFT, TS_RT, 0, 320);
    int touchY = map(p.x, TS_TOP, TS_BOT, 0, 240);
    
    handleTouch(touchX, touchY);
    delay(100); 
  } else {
  // Если сила давления z равна нулю (пальца на экране НЕТ) — разблокируем кнопку LIGHT!
  wasLightTouched = false; 
}

  // 2. Постоянное обновление таймера времени на экране
  if (isRunning) {
    updateTimeDisplay();
  }

  // 3. Боевая логика оцифровки
  if (isRunning) {
    // А) РЕЖИМ АВТО-ТЕСТА
    if (isAutoMode) {
      unsigned long targetInterval = 1000 / framesPerSecond;
      if (millis() - lastFrameTime >= targetInterval) {
        lastFrameTime = millis();
        
        digitalWrite(enablePin, LOW);  
        delay(5);                      
        rotateMotor(true); 
        digitalWrite(enablePin, HIGH); 
        
        // Кадр зафиксирован — ЗАЖИГАЕМ ИМПУЛЬСНУЮ ВСПЫШКУ НА 100%
        analogWrite(ledPin, 255);
        
        Serial.print("C"); Serial.println(lagDelayMillis); 
        frameCount++;
        updateCounters();
        
        delay(200); 
        analogWrite(ledPin, 0); 
      }
    }
    // Б) РЕЖИМ РАБОТЫ С ПК
    else {
      if (!waitForPC) {
        digitalWrite(enablePin, LOW);  
        delay(5);
        rotateMotor(true); 
        digitalWrite(enablePin, HIGH); 
        
        // Кадр встал — ЗАЖИГАЕМ СВЕТ НА ПОЛНУЮ
        analogWrite(ledPin, 255);
        
        Serial.print("C"); Serial.println(lagDelayMillis); 
        waitForPC = true; 
      }
    }
  }

  // Режим ПК: ожидание команды от Docker. По прилету 'K' — гасим свет
  if (!isAutoMode && Serial.available() > 0) {
    char incomingChar = Serial.read();
    if (incomingChar == 'K') { 
      if (isRunning && waitForPC) {
        analogWrite(ledPin, 0); // МГНОВЕННО ТУШИМ ЛАМПУ!
        frameCount++;
        updateCounters();
        waitForPC = false; 
      }
    }
  }
}
void rotateMotor(bool forward) {
  // Направление завязано на пин DIR: LOW = против часовой стрелки, HIGH = по часовой
  if (forward) {
    digitalWrite(dirPin, LOW);
  } else {
    digitalWrite(dirPin, HIGH);
  }
  
  for (int i = 0; i < stepsPerRevolution; i++) {
    digitalWrite(stepPin, HIGH);
    delayMicroseconds(stepDelayMicros); 
    digitalWrite(stepPin, LOW);
    delayMicroseconds(stepDelayMicros);
  }
}

void recalculateSpeedSettings() {
  if (framesPerSecond == 1) stepDelayMicros = 250;
  else if (framesPerSecond == 2) stepDelayMicros = 150; 
  else if (framesPerSecond == 3) stepDelayMicros = 100;
  else if (framesPerSecond == 4) stepDelayMicros = 75;
}

void drawInterface() {
  tft.fillScreen(BLACK);
  tft.fillRect(0, 0, 320, 35, BLUE);
  tft.setTextColor(WHITE); tft.setTextSize(2); tft.setCursor(10, 10);
  tft.print("CINE-TRANSFER v5.4");

  tft.setCursor(15, 45); tft.print("STATUS: "); updateStatusText();
  tft.setCursor(15, 68); tft.print("FRAMES: "); updateCounters();
  tft.setCursor(15, 91); tft.print("TIME:   "); updateTimeDisplay();
  tft.setCursor(15, 115); tft.print("MODE: "); updateModeButton();

  // Скорость
  tft.setCursor(15, 150); tft.print("SPEED: "); tft.print(framesPerSecond); tft.print(" FPS");
  tft.fillRect(160, 142, 35, 30, GRAY); tft.setCursor(173, 149); tft.print("-");
  tft.fillRect(205, 142, 35, 30, GRAY); tft.setCursor(216, 149); tft.print("+");
  
  // Задержка (LAG)
  tft.setCursor(15, 188); tft.print("LAG: "); updateLagText();
  tft.fillRect(160, 180, 35, 30, GRAY); tft.setCursor(173, 187); tft.print("-");
  tft.fillRect(205, 180, 35, 30, GRAY); tft.setCursor(216, 187); tft.print("+");

  // Отрисовка кнопки LIGHT (Новая бирюзовая кнопка сверху справа над стрелками)
  tft.fillRect(255, 104, 50, 30, 0x07FF); // Бирюзовый цвет (CYAN)
  tft.setTextColor(BLACK); tft.setTextSize(1);
  tft.setCursor(264, 110); tft.print("LIGHT");
  tft.setCursor(269, 122); tft.print("OFF");

  // Ручная прокрутка
  tft.fillRect(255, 142, 50, 30, YELLOW); tft.setTextColor(BLACK); tft.setTextSize(2); tft.setCursor(275, 149); tft.print(">");
  tft.fillRect(255, 180, 50, 30, YELLOW); tft.setTextColor(BLACK); tft.setCursor(275, 187); tft.print("<");

  updateControlButtons();
}

void updateStatusText() {
  tft.fillRect(95, 43, 220, 22, BLACK); tft.setTextSize(2);
  if (isRunning) {
    tft.setTextColor(GREEN); tft.setCursor(95, 43); tft.print("SCANNING...");
  } else {
    tft.setTextColor(RED); tft.setCursor(95, 43); tft.print("STOPPED");
  }
}

void updateCounters() {
  tft.fillRect(95, 66, 220, 22, BLACK); tft.setTextColor(WHITE); tft.setTextSize(2);
  tft.setCursor(95, 66); tft.print(frameCount);
}

void updateTimeDisplay() {
  unsigned long currentSessionTime = 0;
  if (isRunning) {
    currentSessionTime = millis() - sessionStartTime; 
  }
  
  unsigned long totalTimeMs = totalElapsedTime + currentSessionTime;
  unsigned long totalSeconds = totalTimeMs / 1000;
  
  int hours = totalSeconds / 3600;
  int minutes = (totalSeconds % 3600) / 60;
  int seconds = totalSeconds % 60;

  tft.fillRect(95, 89, 220, 22, BLACK);
  tft.setTextColor(WHITE); tft.setTextSize(2);
  tft.setCursor(95, 89);
  
  if (hours < 10) tft.print("0"); tft.print(hours); tft.print(":");
  if (minutes < 10) tft.print("0"); tft.print(minutes); tft.print(":");
  if (seconds < 10) tft.print("0"); tft.print(seconds);
}

void updateModeButton() {
  tft.fillRect(75, 110, 230, 30, BLACK); tft.setTextSize(2);
  tft.setCursor(80, 115);
  if (isAutoMode) {
    tft.setTextColor(YELLOW); tft.print("[ AUTO TEST ]");
  } else {
    tft.setTextColor(GREEN); tft.print("[ PC CONTROL ]");
  }
}

void updateLagText() {
  tft.fillRect(65, 183, 90, 25, BLACK); tft.setTextColor(WHITE); tft.setTextSize(2);
  tft.setCursor(65, 188); tft.print(lagDelayMillis); tft.print("ms");
}

void updateControlButtons() {
  tft.setTextSize(2);
  if (isRunning) {
    tft.fillRect(15, 212, 85, 25, GRAY); 
    tft.setTextColor(BLACK); tft.setCursor(33, 217); tft.print("START");

    tft.fillRect(110, 212, 85, 25, RED); 
    tft.setTextColor(WHITE); tft.setCursor(135, 217); tft.print("STOP");
  } else {
    tft.fillRect(15, 212, 85, 25, GREEN); 
    tft.setTextColor(BLACK); tft.setCursor(33, 217); tft.print("START");

    tft.fillRect(110, 212, 85, 25, GRAY); 
    tft.setTextColor(BLACK); tft.setCursor(135, 217); tft.print("STOP");
  }
  tft.fillRect(205, 212, 100, 25, GRAY); tft.setTextColor(WHITE); tft.setTextSize(1);
  tft.setCursor(238, 221); tft.print("RESET CNTR");
}

void handleTouch(int x, int y) {
  // Кнопка START
  if (x > 15 && x < 100 && y > 212 && y < 237) {
    if (!isRunning) {
      // Автоматическое отключение ручной подсветки кадра перед стартом!
      if (isLightOn) {
        isLightOn = false;
        analogWrite(ledPin, 0);
      }
      isRunning = true;
      waitForPC = false;
      sessionStartTime = millis(); 
      lastFrameTime = millis() - (1000 / framesPerSecond); 
      drawInterface(); // Перерисовываем интерфейс, чтобы вернуть кнопке LIGHT дефолтный цвет
    }
  }
  // Кнопка STOP
  if (x > 110 && x < 195 && y > 212 && y < 237) {
    if (isRunning) {
      isRunning = false;
      totalElapsedTime += (millis() - sessionStartTime); 
      
      digitalWrite(enablePin, HIGH); // Отключаем ток мотора
      analogWrite(ledPin, 0);        // Гасим 10W диод
      
      updateStatusText();
      updateControlButtons(); 
      updateTimeDisplay(); 
    }
  }
  // Кнопка RESET
  if (!isRunning && x > 205 && x < 305 && y > 212 && y < 237) {
    frameCount = 0;
    totalElapsedTime = 0; 
    updateCounters();
    updateTimeDisplay();
  }
  // Кнопка MODE
  if (!isRunning && x > 75 && x < 305 && y > 110 && y < 140) {
    // Не даем переключать режимы, если нажата сенсорная зона новой кнопки LIGHT
    if (!(x > 255 && x < 305 && y > 104 && y < 134)) {
      isAutoMode = !isAutoMode;
      updateModeButton();
    }
  }
  // Кнопка Нового Модуля Подсветки LIGHT с жестким триггером отпускания пальца!
  if (!isRunning && x > 255 && x < 305 && y > 104 && y < 134) {
    // Если палец только что прижали, а кнопка еще не была заблокирована
    if (!wasLightTouched) {
      wasLightTouched = true; // Мгновенно блокируем кнопку от повторных срабатываний
      
      isLightOn = !isLightOn;
      tft.setTextSize(1);
      if (isLightOn) {
        tft.fillRect(255, 104, 50, 30, YELLOW); // Кнопка горит жёлтым в режиме ON
        tft.setTextColor(BLACK);
        tft.setCursor(264, 110); tft.print("LIGHT");
        tft.setCursor(272, 122); tft.print("ON");
        analogWrite(ledPin, 100); // Включаем диод в 40% яркости
      } else {
        tft.fillRect(255, 104, 50, 30, 0x07FF); // Бирюзовый в режиме OFF
        tft.setTextColor(BLACK);
        tft.setCursor(264, 110); tft.print("LIGHT");
        tft.setCursor(269, 122); tft.print("OFF");
        analogWrite(ledPin, 0); // Полностью тушим свет
      }
    }
  }

  // Кнопка SPEED "-"
  if (!isRunning && x > 160 && x < 195 && y > 142 && y < 172) {
    if (framesPerSecond > 1) {
      framesPerSecond--;
      recalculateSpeedSettings();
      drawInterface();
    }
  }
  // Кнопка SPEED "+"
  if (!isRunning && x > 205 && x < 240 && y > 142 && y < 172) {
    if (framesPerSecond < 4) {
      framesPerSecond++;
      recalculateSpeedSettings();
      drawInterface();
    }
  }
  // Кнопка LAG "-"
  if (!isRunning && x > 160 && x < 195 && y > 180 && y < 210) {
    if (lagDelayMillis > 50) {
      lagDelayMillis -= 10;
      updateLagText();
    }
  }
  // Кнопка LAG "+"
  if (!isRunning && x > 205 && x < 240 && y > 180 && y < 210) {
    if (lagDelayMillis < 400) {
      lagDelayMillis += 10;
      updateLagText();
    }
  }
  // Кнопка ">" (Ручной шаг вперед)
  if (!isRunning && x > 255 && x < 305 && y > 142 && y < 172) {
    digitalWrite(enablePin, LOW); 
    delay(5);
    rotateMotor(true); 
    digitalWrite(enablePin, HIGH); 
    
    // Если ручной свет не горел — делаем проверочную вспышку
    if (!isLightOn) {
      analogWrite(ledPin, 255); 
      delay(200);               
      analogWrite(ledPin, 0);   
    }
    
    frameCount++;
    updateCounters();
    while(ts.getPoint().z > 30) { delay(10); }
  }
  // Кнопка "<" (Ручной шаг назад)
  if (!isRunning && x > 255 && x < 305 && y > 180 && y < 210) {
    digitalWrite(enablePin, LOW); 
    delay(5);
    rotateMotor(false); 
    digitalWrite(enablePin, HIGH); 
    
    if (!isLightOn) {
      analogWrite(ledPin, 255); 
      delay(200);               
      analogWrite(ledPin, 0);   
    }
    
    frameCount--;
    updateCounters();
    while(ts.getPoint().z > 30) { delay(10); }
  }
}