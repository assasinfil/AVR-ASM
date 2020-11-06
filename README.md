# AVR-ASM
Конспект по AVR ASM

Device:
Atmega328P

## 3 вида памяти в AVR:
### 1) Flash 16-bits - память программ**

Не очень много памяти.
0x0000 - 0xFFFF

### 2) SRAM 8-bits   - Static Random access memory**

Лучше хранит данные, без необходимости обновлять данные.
0x0000 - 0xFFFF

**Регистры:**
1) 0x0000 - 0x001F - POH

2) 0x0020 - 0x005F - I/O (все данные подключенные к процессору, настройки переферии)

3) 0x0060 (Внутренняя SRAM - хранить что хочется)


32 регистра

5 бит

4 бит - в 2 раза меньше

2 бит 

2 битные могут объединяться по парам (X, Y, Z)

Y - 28, 29

Рагистр - 8 бит


R0  - 0x00

R1  - 0x01

R31 - 0x1F

RAMEND

### 3) EEPROM 8-bits 
Между включениями и выключениями


## Сегменты:
**.dseg**

Данные после запуска МК


**.cseg**

Сегмент кода


**.eseg**

EEPROM


## Метки:
**RESET - Код если МК сломан**

**MAIN - Код**

## Флаги:
SREG - Регистр статуса
| Флаг | Расшифровка                        | Описание                                                  |
|------|:----------------------------------:|:----------------------------------------------------------|
| C    | Carry                              | Флаг переноса                                             |
| Z    | Zero                               | Флаг нулевого значения                                    |
| N    | Negative                           | Флаг отрицательного значения (Старший бит после операции) |
| V    | Two's complement overflow indictor | Флаг указатель переполнения (Переполнение перешло в знак) |
| S    | Signed Test                        | Флаг для проверок со знаком (S = V xor N)                 |
| H    | Half Carry                         | Флаг полупереноса (Переполнение 4 бита)                   |
| T    | Transfer bit                       | Флаг переноски (Используется как временный флаг)          |
| I    | Interrupt Enable Flag              | Флаг разрешения глобального прерывания                    |

## КОМАНДЫ:

**Структура команд:**

<Команда> <Куда> <Откуда>

LDI R28, low(1234) - Загрузить старшие биты числа 1234

LDI R29, high(1234) - Загрузить младшие биты числа 1234



| Команда | Синтаксис  | Биты команды               | Операнды                                  |Счетчик | Такты | Флаги      | Описание                                       |
|---------|:----------:|:--------------------------:|:-----------------------------------------:|:------:|:-----:|:----------:|-----------------------------------------------:|
| NOP     | NOP        | 0000 0000 &#124; 0000 0000 |                                           | 1      | 1     |            | Ничего не делать                               |
| MOV     | MOV Rd, Rr | 0010 11rd &#124; dddd rrrr | 0 <= r <= 32; 0 <= d <= 32                | 1      | 1     |            | Копировать регистр                             |
| LDI     | LDI Rd, K  | 1110 KKKK &#124; dddd KKKK | Операнды 16<=d<=32, 0<=K<=255             | 1      | 1     |            | Загрузить значение в регистр                   |
| ADD     | ADD Rd, Rr | 0000 11rd &#124; dddd rrrr | 0<=d<=31; 0<=r<=31                        | 1      | 1     |H+S+V+N+Z+C+| Сложить без переноса                           |
| INC     | INC Rd     | 1001 010d &#124; dddd 0011 | 0<=d<=31                                  | 1      | 1     |S+V+N+Z+    | Инкрементировать                               |
| DEC     | DEC Rd     | 1001 010d &#124; dddd 1010 | 0<=d<=31                                  | 1      | 1     |S+V+N+Z+    | Декрементировать                               |
| SUB     | SUB Rd, Rr | 0001 10rd &#124; dddd rrrr | 0<=d<=31, 0<=r<=31                        | 1      | 1     |H+S+V+N+Z+C+| Вычесть без переноса                           |
| SUBI    | SUBI Rd, K | 0101 KKKK &#124; dddd KKKK | 16<=d<=31, 0<=K<=255                      | 1      | 1     |H+S+V+N+Z+C+| Вычесть значение из регистра                   |
| ADIW    | ADIW Rdl, K| 1001 0110 &#124; KKdd KKKK | dl {24, 26, 28, 30} (X, Y, Z), 0 ≤ K ≤ 63 | 1      | 2     |S+V+N+Z+C+  | Сложить значение с парой регистров (16 бит)    |
| SBIW    | SBIW RDl, K| 1001 0111 &#124; KKdd KKKK | dl {24, 26, 28, 30} (X, Y, Z), 0<=K<=63   | 1      | 2     |S+V+N+Z+C+  | Вычесть значение из пары регистров             |
| ADC     | ADC Rd, Rr | 0001 11rd &#124; dddd rrrr | 0 <= d <= 31, 0 <= r <= 31                | 1      | 1     |H+S+V+N+Z+C+| Сложение двух регистров и содержимого флага переноса|
| SBC     | SBC Rd, Rr | 0000 10rd &#124; dddd rrrr | 0 <= d <= 31, 0 <= r <= 31                | 1      | 1     |H+S+V+N+Z+C+| Вычитание содержимого регистра и содержимого флага переноса (С)|
| SBCI    | SBCI Rd, K | 0100 KKKK &#124; dddd KKKK | 0 <= d <= 31, 16 <= K <= 31               | 1      | 1     |H+S+V+N+Z+C+| Вычитание константы и содержимого флага переноса|



14)
СOM - Перевод в обратный код
COM Rd
1001 010d | dddd 0000
Операнды: 0<=d<=31
S V N Z C
+ 0 + + 1

15)
NEG - Перевод в доп код
NEG Rd
1001 010d | dddd 0001
Операнды: 0<=d<=31
H S V N Z C
+ + + + + +
Заменяет содержимое регистра Rd его дополнением до двух
(реализует обратный код). Значение 0x80 остается неизменным.

Сложение 5 и -5
LDI R16, 5
LDI R17, 5
LDI R18, 5

COM R17
NEG R18

ADD R16, R18  ; 5 + (-5)



Логические побитовые операции

16) AND

17) ANDI - Логическое И между регистром и значением
0111 KKKK | dddd KKKK
Операнды: 16<=d<=31, 0<=K<=255

18) OR - Логическое ИЛИ между двумя регистрами
OR Rd, Rr
0010 10rd | dddd rrrr
0 <= d <= 31, 0 <= r <= 31
S V N Z
+ 0 + +

19) ORI - Логическое ИЛИ между ригистром и значением
ORI Rd, K
0110 KKKK | dddd KKKK
Операнды: 16<=d<=31, 0<=K<=255
S V N Z
+ 0 + +

20) EOR - Исключающее ИЛИ между двумя регистрами
EOR Rd, Rr
0010 01rd | dddd rrrr
Операнды: 0<=d<=31, 0<=r<=31
S V N Z
+ + 0 +

Побитовые сдвиги
21) LSL - логический сдвинуть влево
LSL Rd
0000 11dd | dddd dddd
Операнды: 0<=d<=31
H S V N Z C
+ + + + + +
Выполнение сдвига всех битов Rd на одно место влево.
Бит 0 стирается. Бит 7 загружается во флаг переноса (C).

22) LSR - Логически сдвинуть вправо
LSR Rd
1001 010d | dddd 0110
Операнды: 0 <= d <= 31
S V N Z C
+ + 0 + +

23) ROL - Логически сдвинуть влево через перенос
0001 11dd | dddd dddd
Операнды: 0 <= d <= 31
H S V N Z C
+ + + + + +
Сдвиг все битов Rd на одно место влево. Флаг переноса
(С) смещается на место бита 0 регистра Rd. Бит 7 смещается
во флаг переноса (С).

24) ROR - Логически сдвинуть вправо через перенос
25) ASR - Арифметически сдвинуть вправо

ASR Rd
1001 010d | dddd 0101
Операнды: 0 <= d <= 31
S V N Z C
+ + + + +
Выполнение сдвига всех битов Rd а одно место вправо. 
Состояние бита 7 не меняется. Бит 0 загружается во 
флаг переноса (С).
Перенос с учетом знака.

26) CLR - Очистить регистр
27) SER - Включить все биты регистра
Операнды: 16 <= d <= 31

A = A xor B
B = A xor B
A = A xor B
