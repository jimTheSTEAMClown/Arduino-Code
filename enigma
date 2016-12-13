// -------------------------------------------------------------
//
// APC Magazine Arduino Masterclass - Project #19
// Enigma Cipher Machine
// Code (C) Darren Yates - v10effFULL - 25-Apr-2014/28-May-2014
//
// -------------------------------------------------------------
#include <LiquidCrystal.h>
#include <PS2Keyboard.h>
LiquidCrystal lcd(8,9,4,5,6,7);
const int DataPin = 11;
const int IRQpin =  2;
PS2Keyboard keyboard;

char c;
char rotor[5][26] = {
  {'E','K','M','F','L','G','D','Q','V','Z','N','T','O','W','Y','H','X','U','S','P','A','I','B','R','C','J'},
  {'A','J','D','K','S','I','R','U','X','B','L','H','W','T','M','C','Q','G','Z','N','P','Y','F','V','O','E'},
  {'B','D','F','H','J','L','C','P','R','T','X','V','Z','N','Y','E','I','W','G','A','K','M','U','S','Q','O'},
  {'E','S','O','V','P','Z','J','A','Y','Q','U','I','R','H','X','L','N','F','T','G','K','D','C','M','W','B'},
  {'V','Z','B','R','G','I','T','Y','U','P','S','D','N','H','L','X','A','W','M','J','Q','O','F','E','C','K'}
};

char revRotor[5][26] = {
  {'U','W','Y','G','A','D','F','P','V','Z','B','E','C','K','M','T','H','X','S','L','R','I','N','Q','O','J'},
  {'A','J','P','C','Z','W','R','L','F','B','D','K','O','T','Y','U','Q','G','E','N','H','X','M','I','V','S'},
  {'T','A','G','B','P','C','S','D','Q','E','U','F','V','N','Z','H','Y','I','X','J','W','L','R','K','O','M'},
  {'H','Z','W','V','A','R','T','N','L','G','U','P','X','Q','C','E','J','M','B','S','K','D','Y','O','I','F'},
  {'Q','C','Y','L','X','W','E','N','F','T','Z','O','S','M','V','J','U','D','K','G','I','A','R','P','H','B'}
};

char reflectorB[2][13] = {
  {'A','B','C','D','E','F','G','I','J','K','M','T','V'},
  {'Y','R','U','H','Q','S','L','P','X','N','O','Z','W'}
};

char rotorNotch[5] = {'Q','E','V','J','Z'}; 
char rotorStart[3] = {'A','A','A'};
char rotorRing[3] = {'A','A','A'};
char plugBoard[10][2] = {  
  {32,32},{32,32},{32,32},{32,32},{32,32},{32,32},{32,32},{32,32},{32,32},{32,32} 
};
int rotorSelect[3] = {0,1,2};
char tempRotor[2][26];

String keyInput = "";
String message = "";
String encoded = "";
int NUM_KEYS = 5;
int rotorSet = 0; 
int plugSet = 0; 
int setX = 0;
int charSet = 0;
char charMessage = 'A';
char origChar = 'A';
char codedChar = 'A';
int r1StartPos = 0, r2StartPos = 0, r3StartPos = 0;
String codeString[2] = {"",""};
String rotorSettings = "";
char c1, c2, c3;

void setup() {
  delay(1000);
  keyboard.begin(DataPin, IRQpin);
  enigmaSetup();
}

void loop() {
  enigmaGetChar();
  codedChar = viaPlugBoard(enigmaCipher());
  c1 = (char)r1StartPos+65;
  c2 = (char)r2StartPos+65;
  c3 = (char)r3StartPos+65;
  lcd.setCursor(0,0); 
  lcd.print("P:"); 
  lcd.print(c1); 
  lcd.print(c2); 
  lcd.print(c3);
  lcd.setCursor(0,1); 
  lcd.print("R:"); 
  lcd.print(rotorSelect[0]+1); 
  lcd.print(rotorSelect[1]+1); 
  lcd.print(rotorSelect[2]+1);
  codeString[0] += origChar;
  codeString[1] += codedChar;
  if (codeString[0].length() > 7) {
    codeString[0] = codeString[0].substring(1,8);
    codeString[1] = codeString[1].substring(1,8);
  }
  lcd.setCursor(8,0); 
  lcd.print(codeString[0]+" ");
  lcd.setCursor(8,1); 
  lcd.print(codeString[1]);
  charSet++; 
  if (charSet > 7) charSet = 7;
  lcd.setCursor(8+charSet,0); //lcd.blink(); 
}

void enigmaGetChar() {

  int validChar = 0;
  while (validChar == 0) {  
    if (keyboard.available()) {
      charMessage = keyboard.read();
      if (charMessage >= 'a' && charMessage <= 'z') {
        origChar = (charMessage -= 32);
        //charMessage -= 32;
        lcd.setCursor(8+charSet,0); 
        lcd.print(charMessage);
        validChar = 1;
        charMessage = viaPlugBoard(charMessage);
      }
    }
  }
  
}

char viaPlugBoard(char charToSwap) {
  
  char swap = ' ';
  for (int dloop = 0; dloop < 10; dloop++) {
    if (charToSwap == plugBoard[dloop][0]) {
      swap = plugBoard[dloop][1];
      return swap;
    } 
    else if (charToSwap == plugBoard[dloop][1]) {
      swap = plugBoard[dloop][0];
      return swap;
    }
  }
    return charToSwap;
}



char enigmaCipher() {
  // increment rotors
  r3StartPos++; 
  r3StartPos = r3StartPos % 26;
  if (r3StartPos == (int)rotorNotch[rotorSelect[2]]-64) {
    r2StartPos++; 
    r2StartPos = r2StartPos % 26;
  } 
  else if (r2StartPos == (int)rotorNotch[rotorSelect[1]]-65) { 
    r1StartPos++; 
    r1StartPos = r1StartPos % 26;
    r2StartPos++; 
    r2StartPos = r2StartPos % 26;  
  }

  int inRotIII = charMessage - 'A';
  int inRotIIIringset = (26 + r3StartPos + inRotIII - (rotorRing[2] - 'A')) % 26;
  int outRotIIIringset = rotor[rotorSelect[2]][inRotIIIringset] - 'A';
  int outRotIII = ( 26 + outRotIIIringset + (rotorRing[2] - 'A') ) % 26;
  int outIII = (26 + outRotIII - r3StartPos) % 26;

  int inRotII = outIII;
  int inRotIIringset = (26 + r2StartPos + inRotII - (rotorRing[1] - 'A')) % 26;
  int outRotIIringset = rotor[rotorSelect[1]][inRotIIringset] - 'A';
  int outRotII = ( 26 + outRotIIringset + (rotorRing[1] - 'A') ) % 26;
  int outII = (26 + outRotII - r2StartPos) % 26;

  int inRotI = outII;
  int inRotIringset = (26 + r1StartPos + inRotI - (rotorRing[0] - 'A')) % 26;
  int outRotIringset = rotor[rotorSelect[0]][inRotIringset] - 'A';
  int outRotI = ( 26 + outRotIringset + (rotorRing[0] - 'A') ) % 26;
  int outI = (26 + outRotI - r1StartPos) % 26;

  char letReflectIn = char(outI+65);
  char letReflectOut;
  for (int dloop = 0; dloop < 13; dloop++) {
    if (letReflectIn == reflectorB[0][dloop]) {
      letReflectOut = reflectorB[1][dloop];
    } 
    else if (letReflectIn == reflectorB[1][dloop]) {
      letReflectOut = reflectorB[0][dloop];
    }
  }

  outI = letReflectOut - 'A';
  outRotI = ( 26 + outI + r1StartPos ) % 26;
  outRotIringset = (26 + outRotI - (rotorRing[0]-'A') ) % 26;
  inRotIringset = revRotor[rotorSelect[0]][outRotIringset] - 'A';
  inRotI = (26 + inRotIringset + (rotorRing[0] - 'A') - r1StartPos ) % 26;

  outII = inRotI;
  outRotII = ( 26 + outII + r2StartPos ) % 26;
  outRotIIringset = (26 + outRotII - (rotorRing[1]-'A') ) % 26;
  inRotIIringset = revRotor[rotorSelect[1]][outRotIIringset] - 'A';
  inRotII = (26 + inRotIIringset + (rotorRing[1] - 'A') - r2StartPos ) % 26;

  outIII = inRotII;
  outRotIII = ( 26 + outIII + r3StartPos ) % 26;
  outRotIIIringset = (26 + outRotIII - (rotorRing[2]-'A') ) % 26;
  inRotIIIringset = revRotor[rotorSelect[2]][outRotIIIringset] - 'A';
  inRotIII = (26 + inRotIIIringset + (rotorRing[2] - 'A') - r3StartPos ) % 26;

  return char(inRotIII+65);
}

void enigmaSetup() {
  lcd.begin(16,2);
  lcd.clear();
  lcd.setCursor(0,0); 
  lcd.print(" Arduino Enigma");
  lcd.setCursor(0,1); 
  lcd.print(" Cipher Machine");
  delay(5000);
  lcd.clear();
  lcd.setCursor(0,0); 
  lcd.print("3-digit rotor");
  lcd.setCursor(0,1); 
  lcd.print("order (3of5):123");
  lcd.setCursor(13,1); 
  lcd.blink();

  while (c != PS2_ENTER) {
    if (keyboard.available()) {
      c = keyboard.read();
      if (c > '0' && c < '6') {
        rotorSelect[rotorSet] = c - '0';
        lcd.print(rotorSelect[rotorSet]);
        rotorSelect[rotorSet]--;
        if (rotorSet < 2) rotorSet++;
      }
      if (c == PS2_LEFTARROW && rotorSet > 0) {
        rotorSet--;
      }
      if (c == PS2_RIGHTARROW && rotorSet < 2) {
        rotorSet++;
      }
      lcd.setCursor(13+rotorSet,1);
    }
  }

  lcd.setCursor(0,0); 
  lcd.print("Rotor start     ");
  lcd.setCursor(0,1); 
  lcd.print("settings    :AAA");
  lcd.setCursor(13,1); 
  lcd.blink();

  c = '0';
  rotorSet = 0;
  while (c != PS2_ENTER) {
    if (keyboard.available()) {
      c = keyboard.read();
      String tempString = String(c);
      tempString.toUpperCase();
      if (tempString >= "A" && tempString <= "Z") {
        rotorStart[rotorSet] = c - 32;
        lcd.print(rotorStart[rotorSet]);
        if (rotorSet < 2) rotorSet++;
      }
      if (c == PS2_LEFTARROW && rotorSet > 0) {
        rotorSet--;
      }
      if (c == PS2_RIGHTARROW && rotorSet < 2) {
        rotorSet++;
      }
      lcd.setCursor(13+rotorSet,1);
    }
  }

  lcd.setCursor(0,0); 
  lcd.print("Rotor ring      ");
  lcd.setCursor(0,1); 
  lcd.print("settings    :AAA");
  lcd.setCursor(13,1); 
  lcd.blink();

  c = '0';
  rotorSet = 0;
  while (c != PS2_ENTER) {
    if (keyboard.available()) {
      c = keyboard.read();
      if (c >= 'a' && c <= 'z') {
        rotorRing[rotorSet] = c - 32;
        lcd.print(rotorRing[rotorSet]);
        if (rotorSet < 2) rotorSet++;
      }
      if (c == PS2_LEFTARROW && rotorSet > 0) {
        rotorSet--;
      }
      if (c == PS2_RIGHTARROW && rotorSet < 2) {
        rotorSet++;
      }
      lcd.setCursor(13+rotorSet,1);
    }
  }

  lcd.noBlink();
  lcd.clear();
  lcd.setCursor(0,0); 
  lcd.print("Plugboard pairs:");
  lcd.setCursor(0,1); 
  lcd.print("1:    2:    3:  ");
  lcd.setCursor(2,1); 
  lcd.blink(); 

  c = '0';
  plugSet = 0;
  setX = 0;
  int change = 0;
  while (c != PS2_ENTER) {
    if (keyboard.available()) {
      c = keyboard.read();
      if (c >= 'a' && c <= 'z' && (plugSet < 10 && setX < 2)) {
        //plugBoard[plugSet][setX] = c - 32;
        lcd.print(plugBoard[plugSet][setX] = c - 32);
        if (plugSet < 10) {
          setX++;
          if (setX == 2) {
            setX = 0;
            plugSet++;
            if (plugSet % 3 == 0) change = 0;
          }
          if (plugSet > 8 && change == 0) {
          lcd.setCursor(0,1); 
          lcd.print("10:             "); 
          lcd.setCursor(3,1); 
          lcd.print(plugBoard[9][0]);
          lcd.print(plugBoard[9][1]);
          lcd.setCursor(3,1); 
            change = 1;
          } 
          else if (plugSet > 5 && change == 0) {
          lcd.setCursor(0,1); 
          lcd.print("7:    8:    9:  "); 
          lcd.setCursor(2,1); 
          lcd.print(plugBoard[6][0]);
          lcd.print(plugBoard[6][1]);
          lcd.setCursor(8,1); 
          lcd.print(plugBoard[7][0]);
          lcd.print(plugBoard[7][1]);
          lcd.setCursor(14,1); 
          lcd.print(plugBoard[8][0]);
          lcd.print(plugBoard[8][1]);
            change = 1;
          } 
          else if (plugSet > 2 && change == 0) { 
            lcd.setCursor(0,1); 
          lcd.setCursor(0,1); 
          lcd.print("4:    5:    6:  "); 
          lcd.setCursor(2,1); 
          lcd.print(plugBoard[3][0]);
          lcd.print(plugBoard[3][1]);
          lcd.setCursor(8,1); 
          lcd.print(plugBoard[4][0]);
          lcd.print(plugBoard[4][1]);
          lcd.setCursor(14,1); 
          lcd.print(plugBoard[5][0]);
          lcd.print(plugBoard[5][1]);
             change = 1;
          } 
          if (plugSet < 9) lcd.setCursor(2+((plugSet%3)*6)+(setX),1);
        }
      }
      if (c == PS2_LEFTARROW && plugSet > 0) {
        plugSet--; 
        setX = 0;
      }
      if (c == PS2_RIGHTARROW && plugSet < 9) {
        plugSet++; 
        setX = 0;
      }
      if (c == PS2_RIGHTARROW || c == PS2_LEFTARROW) {
        if (plugSet >= 0 && plugSet < 3) { 
          lcd.setCursor(0,1); 
          lcd.print("1:    2:    3:  "); 
          lcd.setCursor(2,1); 
          lcd.print(plugBoard[0][0]);
          lcd.print(plugBoard[0][1]);
          lcd.setCursor(8,1); 
          lcd.print(plugBoard[1][0]);
          lcd.print(plugBoard[1][1]);
          lcd.setCursor(14,1); 
          lcd.print(plugBoard[2][0]);
          lcd.print(plugBoard[2][1]);
        }
        if (plugSet > 2 && plugSet < 6) { 
          lcd.setCursor(0,1); 
          lcd.print("4:    5:    6:  "); 
          lcd.setCursor(2,1); 
          lcd.print(plugBoard[3][0]);
          lcd.print(plugBoard[3][1]);
          lcd.setCursor(8,1); 
          lcd.print(plugBoard[4][0]);
          lcd.print(plugBoard[4][1]);
          lcd.setCursor(14,1); 
          lcd.print(plugBoard[5][0]);
          lcd.print(plugBoard[5][1]);
        }
        if (plugSet > 5 && plugSet < 9) { 
          lcd.setCursor(0,1); 
          lcd.print("7:    8:    9:  "); 
          lcd.setCursor(2,1); 
          lcd.print(plugBoard[6][0]);
          lcd.print(plugBoard[6][1]);
          lcd.setCursor(8,1); 
          lcd.print(plugBoard[7][0]);
          lcd.print(plugBoard[7][1]);
          lcd.setCursor(14,1); 
          lcd.print(plugBoard[8][0]);
          lcd.print(plugBoard[8][1]);
        }
        if (plugSet == 9) { 
          lcd.setCursor(0,1); 
          lcd.print("10:             "); 
          lcd.setCursor(3,1); 
          lcd.print(plugBoard[9][0]);
          lcd.print(plugBoard[9][1]);
        }
        lcd.setCursor(0,0); 
        lcd.print("Plugboard pairs:");
        if (plugSet < 10) lcd.setCursor(2+((plugSet%3)*6)+(setX),1);
        if (plugSet == 9) lcd.setCursor(3,1);  
      }
    }
  }

  lcd.clear();

  r1StartPos = rotorStart[0]-65;
  r2StartPos = rotorStart[1]-65;
  r3StartPos = rotorStart[2]-65; 
  char c1 = (char)r1StartPos+65;
  char c2 = (char)r2StartPos+65;
  char c3 = (char)r3StartPos+65;
  lcd.setCursor(0,0); 
  lcd.print("P:"); 
  lcd.print(c1); 
  lcd.print(c2); 
  lcd.print(c3);
  lcd.setCursor(0,1); 
  lcd.print("R:"); 
  lcd.print(rotorSelect[0]+1); 
  lcd.print(rotorSelect[1]+1); 
  lcd.print(rotorSelect[2]+1);
  lcd.setCursor(6,0); 
  lcd.print("I: "); 
  lcd.setCursor(6,1); 
  lcd.print("O:"); 
  lcd.setCursor(8,0); 
  lcd.blink();
}

