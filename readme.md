# Code
```c
#include <Wire.h>
#include <LiquidCrystal_I2C.h>
LiquidCrystal_I2C lcd(0x27, 16, 2); // Устанавливаем дисплей

int analogPin = 0;
int raw = 0;
int Vin = 3;
float Vout = 0;
float R1 = 70;
float R2 = 0;
int x = 0;
float y = 0;
float buffer = 0;
float result = 0;
void setup()
{
    lcd.init();
    Serial.begin(9600);
}

void loop()
{
    lcd.backlight(); // Включаем подсветку дисплея
    raw = analogRead(analogPin);
    if (raw)
    {
        lcd.clear();
        lcd.print("Processing...");
        lcd.setCursor(0, 1);
        lcd.print("Please wait!");
        lcd.setCursor(0, 0);
        for (int i = 0; i < 500; i++)
        {
            delay(10);
            buffer = raw * Vin;
            Vout = (buffer) / 1024.0;
            buffer = (Vin / Vout) - 1;
            R2 = R1 * buffer;
            y = ((R2 * 39) / 80.59) + 2.54;

            if (y > 39)
            {
                continue;
            }
            else
            {
                result += y;
                x += 1;
            }
        }
        if (result == 0 or x == 0)
        {
            lcd.clear();
            lcd.print("Processing...");
            lcd.setCursor(0, 1);
            lcd.print("Please wait!");
            lcd.setCursor(0, 0);
        }
        else if (result / x < 3)
        {
            lcd.clear();
            lcd.print(result / x);
            lcd.print(" L");
            lcd.setCursor(0, 1);
            lcd.print("Reserve!!!");
            lcd.setCursor(0, 0);
            delay(5000);
            result = 0;
            x = 0;
        }
        else
        {
            lcd.clear();
            lcd.print(result / x);
            lcd.print(" L");
            delay(5000);
            result = 0;
            x = 0;
        }
        Serial.print(R2);
        Serial.print("\n");
    }
}

```
