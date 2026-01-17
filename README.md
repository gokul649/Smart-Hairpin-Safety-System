#define trigA 2
#define echoA 3
#define trigB 4
#define echoB 5

#define ledA 8
#define ledB 9
#define buzzer 10

int activeSide = 0;  
// 0 = none
// 1 = sensor A active
// 2 = sensor B active

long readDistance(int trig, int echo) {
  digitalWrite(trig, LOW);
  delayMicroseconds(5);
  digitalWrite(trig, HIGH);
  delayMicroseconds(10);
  digitalWrite(trig, LOW);

  long d = pulseIn(echo, HIGH, 30000);
  if (d == 0) return 0;
  return d * 0.034 / 2;
}

void setup() {
  pinMode(trigA, OUTPUT);
  pinMode(echoA, INPUT);
  pinMode(trigB, OUTPUT);
  pinMode(echoB, INPUT);

  pinMode(ledA, OUTPUT);
  pinMode(ledB, OUTPUT);
  pinMode(buzzer, OUTPUT);
}

void blinkLed(int ledPin) {
  digitalWrite(ledPin, HIGH);
  digitalWrite(buzzer, HIGH);
  delay(400);
  digitalWrite(ledPin, LOW);
  digitalWrite(buzzer, LOW);
  delay(400);
}

void loop() {
  int dA = readDistance(trigA, echoA);
  delay(80);
  int dB = readDistance(trigB, echoB);
  delay(80);

  // -------- SENSOR A FIRST --------
  if (dA > 0 && dA <= 50 && activeSide == 0) {
    activeSide = 1;
  }

  // -------- SENSOR B FIRST --------
  if (dB > 0 && dB <= 50 && activeSide == 0) {
    activeSide = 2;
  }

  // -------- ACTION BASED ON ACTIVE SIDE --------
  if (activeSide == 1) {
    blinkLed(ledB);   // A vehicle → warn B side
  }
  else if (activeSide == 2) {
    blinkLed(ledA);   // B vehicle → warn A side
  }

  // -------- RESET WHEN BOTH CLEAR --------
  if ((dA > 50 || dA == 0) && (dB > 50 || dB == 0)) {
    activeSide = 0;
    digitalWrite(ledA, LOW);
    digitalWrite(ledB, LOW);
    digitalWrite(buzzer, LOW);
  }
}
