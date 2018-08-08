---
layout: post
title: ПАО "Квадратурный декодер"
---

# Программно-аппаратное обеспечение "Квадратурный декодер - генератор меандра"

Есть драйвер двигателя, который видаёт квадратурный сигнал. Но частота сигнала слишком большая. Поэтому берём высокоскоростную опторавзязку, берём Atmega32u4 и пишем код.

```c
void setup() {
}

void loop() {
  cli();
  DDRD |= _BV(PD2);
  PORTD = 0;
  PORTD |= _BV(PD1) | _BV(PD0);

  register unsigned char old asm("r3");
  register unsigned char oldZ asm("r4");
  register unsigned char c1 asm("r5");
  register unsigned char c2 asm("r6");

  old = 0;
  c1 = 0;
  c2 = 0;
  oldZ = 0;

  while (true) {
    if (  ( (!old) && (PIND & _BV(PD0) ) ) || ( ((old) && !(PIND & _BV(PD0))))) {
      old = !old;
      c1++;
      if (c1 == 0) {
        c2++;
      }
    }
    if (c2 == 0x01 && c1 == 0xF4) {
      c1 = 0;
      c2 = 0;
      PORTD ^= _BV(PD2);
    }
    if (!oldZ && bit_is_set(PIND, PD1)) {
      oldZ = true;
      c1 = 0;
      c2 = 0;
      PORTD &= ~(_BV(PD2));
    }
    if(oldZ && bit_is_clear(PIND, PD1)){
      oldZ = false;
    }
  }
}
```

Видео с более подробным объяснением происходящего 
[![](https://img.youtube.com/vi/3GDlF9QQbS0/0.jpg)](https://youtu.be/3GDlF9QQbS0)