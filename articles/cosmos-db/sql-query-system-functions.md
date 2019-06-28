---
title: Funkcje systemowe
description: Więcej informacji na temat funkcji systemu SQL w usłudze Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/31/2019
ms.author: mjbrown
ms.openlocfilehash: 11a6fdad187670bcb5af4c56198fd7343680690d
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/24/2019
ms.locfileid: "67342437"
---
# <a name="system-functions"></a>Funkcje systemowe

 Usługa cosmos DB udostępnia wiele wbudowanych funkcji SQL. Poniżej przedstawiono kategorie funkcji wbudowanych.  
  
|Funkcja|Opis|  
|--------------|-----------------|  
|[Funkcje matematyczne](#mathematical-functions)|Funkcje matematyczne wykonywanie obliczeń, zazwyczaj na podstawie wartości wejściowych, które są przekazywane jako argumenty i zwraca wartość liczbową.|  
|[Typ funkcji sprawdzania](#type-checking-functions)|Funkcje kontroli typów pozwalają sprawdzić typ wyrażenia w zapytaniach języka SQL.|  
|[Funkcje ciągów](#string-functions)|Funkcje ciągów wykonania operacji na wartość ciągu wejściowego i zwraca ciąg, wartość liczbowa lub Boolean.|  
|[Funkcje tablicy](#array-functions)|Funkcje tablicy w trakcie operacji na tablicy wartości wejściowej i liczbowa zwracana, atrybut typu wartość logiczna lub wartości tablicy.|
|[Funkcje daty i godziny](#date-time-functions)|Funkcje date i time umożliwiają uzyskanie bieżąca data i Godzina UTC w dwóch formach; sygnaturę czasową liczbowe, którego wartością jest epoki systemu Unix (w milisekundach) lub jako ciąg, który jest zgodny z formatu ISO 8601.|
|[Funkcje przestrzenne](#spatial-functions)|Funkcje przestrzenne wykonania operacji na podstawie wartości wejściowe obiektu przestrzennego i zwracają wartość liczbowa lub Boolean.|  

Poniżej przedstawiono listę funkcji, w ramach każdej kategorii:

| Grupa funkcji | Operacje |
|---------|----------|
| Funkcje matematyczne | ABS, CEILING, EXP, FLOOR, LOG, LOG10, POWER, ROUND, SIGN, SQRT, SQUARE, TRUNC, ACOS, ASIN, ATAN, ATN2, COS, COT, DEGREES, PI, RADIANS, SIN, TAN |
| Sprawdzanie typu funkcji | IS_ARRAY, IS_BOOL, IS_NULL, IS_NUMBER, IS_OBJECT, IS_STRING, IS_DEFINED, IS_PRIMITIVE |
| Funkcje ciągów | CONCAT, CONTAINS, ENDSWITH, INDEX_OF, LEFT, LENGTH, LOWER, LTRIM, REPLACE, REPLICATE, REVERSE, RIGHT, RTRIM, STARTSWITH, SUBSTRING, UPPER |
| Funkcje tablicy | ARRAY_CONCAT, ARRAY_CONTAINS, ARRAY_LENGTH, ARRAY_SLICE |
| Funkcje daty i godziny | GETCURRENTDATETIME, GETCURRENTTIMESTAMP,  |
| Funkcje przestrzenne | ST_DISTANCE, ST_WITHIN, ST_INTERSECTS, ST_ISVALID, ST_ISVALIDDETAILED |

Jeśli aktualnie używasz funkcji zdefiniowanej przez użytkownika (UDF) dla której wbudowanej funkcji jest teraz dostępna, odpowiednie wbudowanej funkcji będzie szybsze uruchamianie i bardziej wydajne.

Główna różnica między ANSI SQL i funkcje usługi Cosmos DB jest, funkcje usługi Cosmos DB są zaprojektowane do dobrze pracować ze schematów i mieszanego schematu danych. Na przykład, jeśli właściwość jest nieobecna lub ma wartość nieliczbowych, takich jak `unknown`, element jest pomijany, zamiast zwracać błąd.

##  <a name="mathematical-functions"></a> Funkcje matematyczne  

Każda z funkcji matematycznych wykonuje obliczenie na podstawie wartości wejściowych, które są podawane jako argumenty, i zwraca wartość liczbową.

Można uruchomić zapytania, podobnie jak w poniższym przykładzie:

```sql
    SELECT VALUE ABS(-4)
```

Wynik jest:

```json
    [4]
```

Poniżej znajduje się tabela zawierająca obsługiwane wbudowane funkcje matematyczne.
  
||||  
|-|-|-|  
|[ABS](#bk_abs)|[ACOS](#bk_acos)|[ASIN](#bk_asin)|  
|[ATAN](#bk_atan)|[ATN2](#bk_atn2)|[LIMIT](#bk_ceiling)|  
|[COS](#bk_cos)|[COT](#bk_cot)|[STOPNI](#bk_degrees)|  
|[EXP](#bk_exp)|[FLOOR](#bk_floor)|[DZIENNIK](#bk_log)|  
|[LOG10](#bk_log10)|[PI](#bk_pi)|[ZASILANIA](#bk_power)|  
|[RADIANS](#bk_radians)|[ROUND](#bk_round)|[SIN](#bk_sin)|  
|[SQRT](#bk_sqrt)|[KWADRAT](#bk_square)|[LOGOWANIE](#bk_sign)|  
|[TAN](#bk_tan)|[TRUNC —](#bk_trunc)||  
  
####  <a name="bk_abs"></a> ABS  
 Zwraca wartość bezwzględną (dodatnią) podanego wyrażenia liczbowego.  
  
 **Składnia**  
  
```  
ABS (<numeric_expression>)  
```  
  
 **Argumenty**  
  
- `numeric_expression`  
  
   To wyrażenie liczbowe.  
  
  **Typy zwracane**  
  
  Zwraca wartość wyrażenia liczbowego.  
  
  **Przykłady**  
  
  Poniższy przykład pokazuje wyniki za pomocą funkcji ABS na trzech różnych liczb.  
  
```  
SELECT ABS(-1) AS abs1, ABS(0) AS abs2, ABS(1) AS abs3 
```  
  
 W tym miejscu znajduje się zestaw wyników.  
  
```  
[{abs1: 1, abs2: 0, abs3: 1}]  
```  
  
####  <a name="bk_acos"></a> ACOS  
 Zwraca kąt w radianach, którego cosinus jest określonym wyrażeniem liczbowym. Ta funkcja jest również nazywana arcus cosinus.  
  
 **Składnia**  
  
```  
ACOS(<numeric_expression>)  
```  
  
 **Argumenty**  
  
- `numeric_expression`  
  
   To wyrażenie liczbowe.  
  
  **Typy zwracane**  
  
  Zwraca wartość wyrażenia liczbowego.  
  
  **Przykłady**  
  
  Poniższy przykład zwraca ACOS-1.  
  
```  
SELECT ACOS(-1) AS acos 
```  
  
 W tym miejscu znajduje się zestaw wyników.  
  
```  
[{"acos": 3.1415926535897931}]  
```  
  
####  <a name="bk_asin"></a> ASIN  
 Zwraca kąt w radianach, którego sinus jest określonym wyrażeniem liczbowym. Jest to również nazywane arcus sinus.  
  
 **Składnia**  
  
```  
ASIN(<numeric_expression>)  
```  
  
 **Argumenty**  
  
- `numeric_expression`  
  
   To wyrażenie liczbowe.  
  
  **Typy zwracane**  
  
  Zwraca wartość wyrażenia liczbowego.  
  
  **Przykłady**  
  
  Poniższy przykład zwraca ASIN-1.  
  
```  
SELECT ASIN(-1) AS asin  
```  
  
 W tym miejscu znajduje się zestaw wyników.  
  
```  
[{"asin": -1.5707963267948966}]  
```  
  
####  <a name="bk_atan"></a> ATAN  
 Zwraca kąt w radianach, którego tangens jest określonym wyrażeniem liczbowym. Ta funkcja jest również nazywana arcus tangens.  
  
 **Składnia**  
  
```  
ATAN(<numeric_expression>)  
```  
  
 **Argumenty**  
  
- `numeric_expression`  
  
   To wyrażenie liczbowe.  
  
  **Typy zwracane**  
  
  Zwraca wartość wyrażenia liczbowego.  
  
  **Przykłady**  
  
  Poniższy przykład zwraca ATAN od określonej wartości.  
  
```  
SELECT ATAN(-45.01) AS atan  
```  
  
 W tym miejscu znajduje się zestaw wyników.  
  
```  
[{"atan": -1.5485826962062663}]  
```  
  
####  <a name="bk_atn2"></a> ATN2  
 Zwraca wartość główną wartości arcus tangens y / x, wyrażony w radianach.  
  
 **Składnia**  
  
```  
ATN2(<numeric_expression>, <numeric_expression>)  
```  
  
 **Argumenty**  
  
- `numeric_expression`  
  
   To wyrażenie liczbowe.  
  
  **Typy zwracane**  
  
  Zwraca wartość wyrażenia liczbowego.  
  
  **Przykłady**  
  
  W poniższym przykładzie obliczany ATN2 dla określonego x i y składników.  
  
```  
SELECT ATN2(35.175643, 129.44) AS atn2  
```  
  
 W tym miejscu znajduje się zestaw wyników.  
  
```  
[{"atn2": 1.3054517947300646}]  
```  
  
####  <a name="bk_ceiling"></a> LIMIT  
 Zwraca najmniejszą wartość całkowitą równą określonemu wyrażeniu liczbowemu lub większą.  
  
 **Składnia**  
  
```  
CEILING (<numeric_expression>)  
```  
  
 **Argumenty**  
  
- `numeric_expression`  
  
   To wyrażenie liczbowe.  
  
  **Typy zwracane**  
  
  Zwraca wartość wyrażenia liczbowego.  
  
  **Przykłady**  
  
  Poniższy przykład pokazuje, liczbowych dodatnia, ujemna i wartości zerowych za pomocą funkcji CEILING.  
  
```  
SELECT CEILING(123.45) AS c1, CEILING(-123.45) AS c2, CEILING(0.0) AS c3  
```  
  
 W tym miejscu znajduje się zestaw wyników.  
  
```  
[{c1: 124, c2: -123, c3: 0}]  
```  
  
####  <a name="bk_cos"></a> COS  
 Zwraca trygonometryczny cosinus określonego kąta w radianach w określonym wyrażeniu.  
  
 **Składnia**  
  
```  
COS(<numeric_expression>)  
```  
  
 **Argumenty**  
  
- `numeric_expression`  
  
   To wyrażenie liczbowe.  
  
  **Typy zwracane**  
  
  Zwraca wartość wyrażenia liczbowego.  
  
  **Przykłady**  
  
  W poniższym przykładzie obliczany COS podanemu kątowi.  
  
```  
SELECT COS(14.78) AS cos  
```  
  
 W tym miejscu znajduje się zestaw wyników.  
  
```  
[{"cos": -0.59946542619465426}]  
```  
  
####  <a name="bk_cot"></a> COT  
 Zwraca trygonometryczny cotangens określonego kąta w radianach w określonym wyrażeniu liczbowym.  
  
 **Składnia**  
  
```  
COT(<numeric_expression>)  
```  
  
 **Argumenty**  
  
- `numeric_expression`  
  
   To wyrażenie liczbowe.  
  
  **Typy zwracane**  
  
  Zwraca wartość wyrażenia liczbowego.  
  
  **Przykłady**  
  
  W poniższym przykładzie obliczany COT podanemu kątowi.  
  
```  
SELECT COT(124.1332) AS cot  
```  
  
 W tym miejscu znajduje się zestaw wyników.  
  
```  
[{"cot": -0.040311998371148884}]  
```  
  
####  <a name="bk_degrees"></a> STOPNI  
 Zwraca kąt w stopniach odpowiadający kątowi określonemu w radianach.  
  
 **Składnia**  
  
```  
DEGREES (<numeric_expression>)  
```  
  
 **Argumenty**  
  
- `numeric_expression`  
  
   To wyrażenie liczbowe.  
  
  **Typy zwracane**  
  
  Zwraca wartość wyrażenia liczbowego.  
  
  **Przykłady**  
  
  Poniższy przykład zwraca liczbę stopni w pod kątem PI/2 radianów.  
  
```  
SELECT DEGREES(PI()/2) AS degrees  
```  
  
 W tym miejscu znajduje się zestaw wyników.  
  
```  
[{"degrees": 90}]  
```  
  
####  <a name="bk_floor"></a> FLOOR  
 Zwraca największą wartość całkowitą równą określonemu wyrażeniu liczbowemu lub mniejszą.  
  
 **Składnia**  
  
```  
FLOOR (<numeric_expression>)  
```  
  
 **Argumenty**  
  
- `numeric_expression`  
  
   To wyrażenie liczbowe.  
  
  **Typy zwracane**  
  
  Zwraca wartość wyrażenia liczbowego.  
  
  **Przykłady**  
  
  Poniższy przykład pokazuje, liczbowych dodatnia, ujemna i wartości zerowe za pomocą funkcji FLOOR.  
  
```  
SELECT FLOOR(123.45) AS fl1, FLOOR(-123.45) AS fl2, FLOOR(0.0) AS fl3  
```  
  
 W tym miejscu znajduje się zestaw wyników.  
  
```  
[{fl1: 123, fl2: -124, fl3: 0}]  
```  
  
####  <a name="bk_exp"></a> EXP  
 Zwraca wartość określonego wyrażenia liczbowego.  
  
 **Składnia**  
  
```  
EXP (<numeric_expression>)  
```  
  
 **Argumenty**  
  
- `numeric_expression`  
  
   To wyrażenie liczbowe.  
  
  **Typy zwracane**  
  
  Zwraca wartość wyrażenia liczbowego.  
  
  **Uwagi**  
  
  Stała **e** (2.718281...), jest podstawą logarytmy naturalne.  
  
  Wykładnik potęgi liczby jest stałą **e** podniesioną do potęgi liczby. Na przykład EXP(1.0) = e ^ 1.0 = 2.71828182845905 i EXP(10) = e ^ 10 = 22026.4657948067.  
  
  Jest to liczba wykładniczą logarytm naturalny liczby sam: EXP (dziennik (n)) = n. A logarytm naturalny wykładniczą liczby jest liczbą sam: Dziennik (EXP (n)) = n.  
  
  **Przykłady**  
  
  Poniższy przykład deklaruje zmienną i zwraca wartość określonej zmiennej (10).  
  
```  
SELECT EXP(10) AS exp  
```  
  
 W tym miejscu znajduje się zestaw wyników.  
  
```  
[{exp: 22026.465794806718}]  
```  
  
 Poniższy przykład zwraca wartość natural logarithm 20 i wykładniczą 20 logarytm naturalny. Ponieważ te funkcje są funkcje odwrotne od siebie, wartość zwracana z zaokrąglaniem do zmiennoprzecinkowych matematyczne punktu w obu przypadkach wynosi 20.  
  
```  
SELECT EXP(LOG(20)) AS exp1, LOG(EXP(20)) AS exp2  
```  
  
 W tym miejscu znajduje się zestaw wyników.  
  
```  
[{exp1: 19.999999999999996, exp2: 20}]  
```  
  
####  <a name="bk_log"></a> DZIENNIK  
 Zwraca logarytm naturalny z określonego wyrażenia liczbowego.  
  
 **Składnia**  
  
```  
LOG (<numeric_expression> [, <base>])  
```  
  
 **Argumenty**  
  
- `numeric_expression`  
  
   To wyrażenie liczbowe.  
  
- `base`  
  
   Opcjonalny argument liczbowy, który ustawia podstawa logarytmu.  
  
  **Typy zwracane**  
  
  Zwraca wartość wyrażenia liczbowego.  
  
  **Uwagi**  
  
  Domyślnie LOG() Zwraca logarytm naturalny. Podstawa logarytmu można zmienić z inną wartością za pomocą opcjonalnego parametru podstawowej.  
  
  Logarytm naturalny to logarytm o podstawie **e**, gdzie **e** jest stałą nieracjonalnej w przybliżeniu równa 2.718281828.  
  
  Logarytm naturalny wykładniczą liczby jest to liczba sam: Dziennik (EXP (n)) = n. I wykładniczą logarytm naturalny liczby jest to liczba sam: EXP (dziennik (n)) = n.  
  
  **Przykłady**  
  
  Poniższy przykład deklaruje zmienną i zwraca wartość logarytmu określonej zmiennej (10).  
  
```  
SELECT LOG(10) AS log  
```  
  
 W tym miejscu znajduje się zestaw wyników.  
  
```  
[{log: 2.3025850929940459}]  
```  
  
 W poniższym przykładzie obliczany dziennika wykładnika potęgi liczby.  
  
```  
SELECT EXP(LOG(10)) AS expLog  
```  
  
 W tym miejscu znajduje się zestaw wyników.  
  
```  
[{expLog: 10.000000000000002}]  
```  
  
####  <a name="bk_log10"></a> LOG10  
 Zwraca logarytm base 10 określonego wyrażenia liczbowego.  
  
 **Składnia**  
  
```  
LOG10 (<numeric_expression>)  
```  
  
 **Argumenty**  
  
- `numeric_expression`  
  
   To wyrażenie liczbowe.  
  
  **Typy zwracane**  
  
  Zwraca wartość wyrażenia liczbowego.  
  
  **Uwagi**  
  
  LOG10 i możliwości funkcji odwrotnie są powiązane ze sobą. Na przykład 10 ^ LOG10(n) = n.  
  
  **Przykłady**  
  
  Poniższy przykład deklaruje zmienną i zwraca wartość LOG10 określonej zmiennej (100).  
  
```  
SELECT LOG10(100) AS log10 
```  
  
 W tym miejscu znajduje się zestaw wyników.  
  
```  
[{log10: 2}]  
```  
  
####  <a name="bk_pi"></a> PI  
 Zwraca stałą wartość liczby PI.  
  
 **Składnia**  
  
```  
PI ()  
```  
  
 **Argumenty**  
  
- `numeric_expression`  
  
   To wyrażenie liczbowe.  
  
  **Typy zwracane**  
  
  Zwraca wartość wyrażenia liczbowego.  
  
  **Przykłady**  
  
  Poniższy przykład zwraca wartość liczby PI.  
  
```  
SELECT PI() AS pi 
```  
  
 W tym miejscu znajduje się zestaw wyników.  
  
```  
[{"pi": 3.1415926535897931}]  
```  
  
####  <a name="bk_power"></a> ZASILANIA  
 Zwraca wartość z określonego wyrażenia do określonej potęgi.  
  
 **Składnia**  
  
```  
POWER (<numeric_expression>, <y>)  
```  
  
 **Argumenty**  
  
- `numeric_expression`  
  
   To wyrażenie liczbowe.  
  
- `y`  
  
   Jest to prawo do którego zostanie wywołane `numeric_expression`.  
  
  **Typy zwracane**  
  
  Zwraca wartość wyrażenia liczbowego.  
  
  **Przykłady**  
  
  W poniższym przykładzie pokazano, zwiększenie liczby do potęgi 3 (moduł liczba).  
  
```  
SELECT POWER(2, 3) AS pow1, POWER(2.5, 3) AS pow2  
```  
  
 W tym miejscu znajduje się zestaw wyników.  
  
```  
[{pow1: 8, pow2: 15.625}]  
```  
  
####  <a name="bk_radians"></a> WARTOŚĆ W RADIANACH  
 Zwraca wartość w radianach po wprowadzeniu wyrażenia liczbowego w stopniach.  
  
 **Składnia**  
  
```  
RADIANS (<numeric_expression>)  
```  
  
 **Argumenty**  
  
- `numeric_expression`  
  
   To wyrażenie liczbowe.  
  
  **Typy zwracane**  
  
  Zwraca wartość wyrażenia liczbowego.  
  
  **Przykłady**  
  
  Poniższy przykład przyjmuje kilka kąty jako dane wejściowe i zwraca ich odpowiednie wartości w radianach.  
  
```  
SELECT RADIANS(-45.01) AS r1, RADIANS(-181.01) AS r2, RADIANS(0) AS r3, RADIANS(0.1472738) AS r4, RADIANS(197.1099392) AS r5  
```  
  
  W tym miejscu znajduje się zestaw wyników.  
  
```  
[{  
       "r1": -0.7855726963226477,  
       "r2": -3.1592204790349356,  
       "r3": 0,  
       "r4": 0.0025704127119236249,  
       "r5": 3.4402174274458375  
   }]  
```  
  
####  <a name="bk_round"></a> ROUND  
 Zwraca wartość liczbową zaokrągloną do najbliższej wartości całkowitej.  
  
 **Składnia**  
  
```  
ROUND(<numeric_expression>)  
```  
  
 **Argumenty**  
  
- `numeric_expression`  
  
   To wyrażenie liczbowe.  
  
  **Typy zwracane**  
  
  Zwraca wartość wyrażenia liczbowego.  
  
  **Uwagi**
  
  Operację zaokrąglania, wykonywana jest zgodna punkt środkowy zaokrąglanie od zera. Jeśli dane wejściowe są wyrażenia liczbowego, który mieści się między dwoma liczbami całkowitymi wynik będzie najbliższej wartości liczby całkowitej w kierunku od zera.  
  
  |<numeric_expression>|Zaokrąglone|
  |-|-|
  |-6.5000|-7|
  |-0.5|-1|
  |0,5|1|
  |6.5000|7||
  
  **Przykłady**  
  
  Poniższy przykład zaokrągla liczby dodatnie i ujemne następujące do najbliższej liczby całkowitej.  
  
```  
SELECT ROUND(2.4) AS r1, ROUND(2.6) AS r2, ROUND(2.5) AS r3, ROUND(-2.4) AS r4, ROUND(-2.6) AS r5  
```  
  
  W tym miejscu znajduje się zestaw wyników.  
  
```  
[{r1: 2, r2: 3, r3: 3, r4: -2, r5: -3}]  
```  
  
####  <a name="bk_sign"></a> LOGOWANIE  
 Zwraca wynik dodatni (+ 1), wartość zero (0) lub minus (-1) z określonego wyrażenia liczbowego.  
  
 **Składnia**  
  
```  
SIGN(<numeric_expression>)  
```  
  
 **Argumenty**  
  
- `numeric_expression`  
  
   To wyrażenie liczbowe.  
  
  **Typy zwracane**  
  
  Zwraca wartość wyrażenia liczbowego.  
  
  **Przykłady**  
  
  Poniższy przykład zwraca wartości znak cyfry od -2 z 2.  
  
```  
SELECT SIGN(-2) AS s1, SIGN(-1) AS s2, SIGN(0) AS s3, SIGN(1) AS s4, SIGN(2) AS s5  
```  
  
 W tym miejscu znajduje się zestaw wyników.  
  
```  
[{s1: -1, s2: -1, s3: 0, s4: 1, s5: 1}]  
```  
  
####  <a name="bk_sin"></a> SIN  
 Zwraca trygonometryczny sinus określonego kąta w radianach w określonym wyrażeniu.  
  
 **Składnia**  
  
```  
SIN(<numeric_expression>)  
```  
  
 **Argumenty**  
  
- `numeric_expression`  
  
   To wyrażenie liczbowe.  
  
  **Typy zwracane**  
  
  Zwraca wartość wyrażenia liczbowego.  
  
  **Przykłady**  
  
  W poniższym przykładzie oblicza SINUS określonego kąta.  
  
```  
SELECT SIN(45.175643) AS sin  
```  
  
 W tym miejscu znajduje się zestaw wyników.  
  
```  
[{"sin": 0.929607286611012}]  
```  
  
####  <a name="bk_sqrt"></a> SQRT  
 Zwraca pierwiastek kwadratowy z określoną wartość liczbową.  
  
 **Składnia**  
  
```  
SQRT(<numeric_expression>)  
```  
  
 **Argumenty**  
  
- `numeric_expression`  
  
   To wyrażenie liczbowe.  
  
  **Typy zwracane**  
  
  Zwraca wartość wyrażenia liczbowego.  
  
  **Przykłady**  
  
  Poniższy przykład zwraca pierwiastki kwadratowe liczb od 1 do 3.  
  
```  
SELECT SQRT(1) AS s1, SQRT(2.0) AS s2, SQRT(3) AS s3  
```  
  
 W tym miejscu znajduje się zestaw wyników.  
  
```  
[{s1: 1, s2: 1.4142135623730952, s3: 1.7320508075688772}]  
```  
  
####  <a name="bk_square"></a> KWADRAT  
 Zwraca kwadrat określoną wartość liczbową.  
  
 **Składnia**  
  
```  
SQUARE(<numeric_expression>)  
```  
  
 **Argumenty**  
  
- `numeric_expression`  
  
   To wyrażenie liczbowe.  
  
  **Typy zwracane**  
  
  Zwraca wartość wyrażenia liczbowego.  
  
  **Przykłady**  
  
  Poniższy przykład zwraca kwadratów liczb od 1 do 3.  
  
```  
SELECT SQUARE(1) AS s1, SQUARE(2.0) AS s2, SQUARE(3) AS s3  
```  
  
 W tym miejscu znajduje się zestaw wyników.  
  
```  
[{s1: 1, s2: 4, s3: 9}]  
```  
  
####  <a name="bk_tan"></a> TAN  
 Zwraca tangens kąta określonego w radianach, określone wyrażenie.  
  
 **Składnia**  
  
```  
TAN (<numeric_expression>)  
```  
  
 **Argumenty**  
  
- `numeric_expression`  
  
   To wyrażenie liczbowe.  
  
  **Typy zwracane**  
  
  Zwraca wartość wyrażenia liczbowego.  
  
  **Przykłady**  
  
  W poniższym przykładzie oblicza tangens liczby PI () / 2.  
  
```  
SELECT TAN(PI()/2) AS tan 
```  
  
 W tym miejscu znajduje się zestaw wyników.  
  
```  
[{"tan": 16331239353195370 }]  
```  
  
####  <a name="bk_trunc"></a> TRUNC —  
 Zwraca wartość liczbową przyciętą do najbliższej wartości całkowitej.  
  
 **Składnia**  
  
```  
TRUNC(<numeric_expression>)  
```  
  
 **Argumenty**  
  
- `numeric_expression`  
  
   To wyrażenie liczbowe.  
  
  **Typy zwracane**  
  
  Zwraca wartość wyrażenia liczbowego.  
  
  **Przykłady**  
  
  Poniższy przykład obcina następujących numerów dodatnie i ujemne do najbliższej wartości całkowitej.  
  
```  
SELECT TRUNC(2.4) AS t1, TRUNC(2.6) AS t2, TRUNC(2.5) AS t3, TRUNC(-2.4) AS t4, TRUNC(-2.6) AS t5  
```  
  
 W tym miejscu znajduje się zestaw wyników.  
  
```  
[{t1: 2, t2: 2, t3: 2, t4: -2, t5: -2}]  
```

## <a id="type-checking-functions"></a>Typ funkcji sprawdzania

Funkcje sprawdzania typu pozwalają sprawdzić typ wyrażenia w ramach zapytania SQL. Kontrola typów w funkcji służy do określania typów właściwości elementów na bieżąco, kiedy są one zmienna lub nieznany. W tym miejscu znajduje się tabela obsługiwanych wbudowanych funkcji sprawdzania typów:

Następujące funkcje obsługuje typ sprawdzania względem wartości wejściowych, a każda zwraca wartość logiczną.  
  
||||  
|-|-|-|  
|[IS_ARRAY](#bk_is_array)|[IS_BOOL](#bk_is_bool)|[IS_DEFINED](#bk_is_defined)|  
|[IS_NULL](#bk_is_null)|[IS_NUMBER](#bk_is_number)|[IS_OBJECT](#bk_is_object)|  
|[IS_PRIMITIVE](#bk_is_primitive)|[IS_STRING](#bk_is_string)||  
  
####  <a name="bk_is_array"></a> IS_ARRAY  
 Zwraca wartość logiczną wskazującą, czy Tablica typu z określonego wyrażenia.  
  
 **Składnia**  
  
```  
IS_ARRAY(<expression>)  
```  
  
 **Argumenty**  
  
- `expression`  
  
   Jest dowolnym prawidłowym wyrażeniem.  
  
  **Typy zwracane**  
  
  Zwraca wyrażenie logiczne.  
  
  **Przykłady**  
  
  Poniższy przykład sprawdza obiektów JSON logiczna, liczba, ciąg, wartość null, obiekt, tablica i niezdefiniowane typów, przy użyciu funkcji IS_ARRAY.  
  
```  
SELECT   
 IS_ARRAY(true) AS isArray1,   
 IS_ARRAY(1) AS isArray2,  
 IS_ARRAY("value") AS isArray3,  
 IS_ARRAY(null) AS isArray4,  
 IS_ARRAY({prop: "value"}) AS isArray5,   
 IS_ARRAY([1, 2, 3]) AS isArray6,  
 IS_ARRAY({prop: "value"}.prop2) AS isArray7  
```  
  
 W tym miejscu znajduje się zestaw wyników.  
  
```  
[{"isArray1":false,"isArray2":false,"isArray3":false,"isArray4":false,"isArray5":false,"isArray6":true,"isArray7":false}]
```  
  
####  <a name="bk_is_bool"></a> IS_BOOL  
 Zwraca wartość logiczną wskazującą, jeśli typ określonego wyrażenie jest wartością logiczną.  
  
 **Składnia**  
  
```  
IS_BOOL(<expression>)  
```  
  
 **Argumenty**  
  
- `expression`  
  
   Jest dowolnym prawidłowym wyrażeniem.  
  
  **Typy zwracane**  
  
  Zwraca wyrażenie logiczne.  
  
  **Przykłady**  
  
  Poniższy przykład sprawdza obiektów JSON logiczna, liczba, ciąg, wartość null, obiekt, tablica i niezdefiniowane typów, przy użyciu funkcji IS_BOOL.  
  
```  
SELECT   
    IS_BOOL(true) AS isBool1,   
    IS_BOOL(1) AS isBool2,  
    IS_BOOL("value") AS isBool3,   
    IS_BOOL(null) AS isBool4,  
    IS_BOOL({prop: "value"}) AS isBool5,   
    IS_BOOL([1, 2, 3]) AS isBool6,  
    IS_BOOL({prop: "value"}.prop2) AS isBool7  
```  
  
 W tym miejscu znajduje się zestaw wyników.  
  
```  
[{"isBool1":true,"isBool2":false,"isBool3":false,"isBool4":false,"isBool5":false,"isBool6":false,"isBool7":false}]
```  
  
####  <a name="bk_is_defined"></a> IS_DEFINED  
 Zwraca wartość logiczną wskazującą, do właściwości przypisano wartość.  
  
 **Składnia**  
  
```  
IS_DEFINED(<expression>)  
```  
  
 **Argumenty**  
  
- `expression`  
  
   Jest dowolnym prawidłowym wyrażeniem.  
  
  **Typy zwracane**  
  
  Zwraca wyrażenie logiczne.  
  
  **Przykłady**  
  
  Poniższy przykład sprawdza obecność właściwość w ramach określonego dokumentu JSON. Pierwsza zwraca wartość true, ponieważ "a" jest obecny, ale druga zwraca wartość false, ponieważ nie istnieje "b".  
  
```  
SELECT IS_DEFINED({ "a" : 5 }.a) AS isDefined1, IS_DEFINED({ "a" : 5 }.b) AS isDefined2 
```  
  
 W tym miejscu znajduje się zestaw wyników.  
  
```  
[{"isDefined1":true,"isDefined2":false}]  
```  
  
####  <a name="bk_is_null"></a> IS_NULL  
 Zwraca wartość logiczną wskazującą, jeśli typ określonego wyrażenie ma wartość null.  
  
 **Składnia**  
  
```  
IS_NULL(<expression>)  
```  
  
 **Argumenty**  
  
- `expression`  
  
   Jest dowolnym prawidłowym wyrażeniem.  
  
  **Typy zwracane**  
  
  Zwraca wyrażenie logiczne.  
  
  **Przykłady**  
  
  Poniższy przykład sprawdza obiektów JSON logiczna, liczba, ciąg, wartość null, obiekt, tablica i niezdefiniowane typów, przy użyciu funkcji IS_NULL.  
  
```  
SELECT   
    IS_NULL(true) AS isNull1,   
    IS_NULL(1) AS isNull2,  
    IS_NULL("value") AS isNull3,   
    IS_NULL(null) AS isNull4,  
    IS_NULL({prop: "value"}) AS isNull5,   
    IS_NULL([1, 2, 3]) AS isNull6,  
    IS_NULL({prop: "value"}.prop2) AS isNull7  
```  
  
 W tym miejscu znajduje się zestaw wyników.  
  
```  
[{"isNull1":false,"isNull2":false,"isNull3":false,"isNull4":true,"isNull5":false,"isNull6":false,"isNull7":false}]
```  
  
####  <a name="bk_is_number"></a> IS_NUMBER  
 Zwraca wartość logiczną wskazującą, jeżeli typ podanego wyrażenia jest liczbą.  
  
 **Składnia**  
  
```  
IS_NUMBER(<expression>)  
```  
  
 **Argumenty**  
  
- `expression`  
  
   Jest dowolnym prawidłowym wyrażeniem.  
  
  **Typy zwracane**  
  
  Zwraca wyrażenie logiczne.  
  
  **Przykłady**  
  
  Poniższy przykład sprawdza obiektów JSON logiczna, liczba, ciąg, wartość null, obiekt, tablica i niezdefiniowane typów, przy użyciu funkcji IS_NULL.  
  
```  
SELECT   
    IS_NUMBER(true) AS isNum1,   
    IS_NUMBER(1) AS isNum2,  
    IS_NUMBER("value") AS isNum3,   
    IS_NUMBER(null) AS isNum4,  
    IS_NUMBER({prop: "value"}) AS isNum5,   
    IS_NUMBER([1, 2, 3]) AS isNum6,  
    IS_NUMBER({prop: "value"}.prop2) AS isNum7  
```  
  
 W tym miejscu znajduje się zestaw wyników.  
  
```  
[{"isNum1":false,"isNum2":true,"isNum3":false,"isNum4":false,"isNum5":false,"isNum6":false,"isNum7":false}]  
```  
  
####  <a name="bk_is_object"></a> IS_OBJECT  
 Zwraca wartość logiczną wskazującą, czy obiekt JSON typu z określonego wyrażenia.  
  
 **Składnia**  
  
```  
IS_OBJECT(<expression>)  
```  
  
 **Argumenty**  
  
- `expression`  
  
   Jest dowolnym prawidłowym wyrażeniem.  
  
  **Typy zwracane**  
  
  Zwraca wyrażenie logiczne.  
  
  **Przykłady**  
  
  Poniższy przykład sprawdza obiektów JSON logiczna, liczba, ciąg, wartość null, obiekt, tablica i niezdefiniowane typów, przy użyciu funkcji is_object —.  
  
```  
SELECT   
    IS_OBJECT(true) AS isObj1,   
    IS_OBJECT(1) AS isObj2,  
    IS_OBJECT("value") AS isObj3,   
    IS_OBJECT(null) AS isObj4,  
    IS_OBJECT({prop: "value"}) AS isObj5,   
    IS_OBJECT([1, 2, 3]) AS isObj6,  
    IS_OBJECT({prop: "value"}.prop2) AS isObj7  
```  
  
 W tym miejscu znajduje się zestaw wyników.  
  
```  
[{"isObj1":false,"isObj2":false,"isObj3":false,"isObj4":false,"isObj5":true,"isObj6":false,"isObj7":false}]
```  
  
####  <a name="bk_is_primitive"></a> IS_PRIMITIVE  
 Zwraca wartość logiczną wskazującą, czy podstawowy typu z określonego wyrażenia (string, Boolean, liczbowych lub wartość null).  
  
 **Składnia**  
  
```  
IS_PRIMITIVE(<expression>)  
```  
  
 **Argumenty**  
  
- `expression`  
  
   Jest dowolnym prawidłowym wyrażeniem.  
  
  **Typy zwracane**  
  
  Zwraca wyrażenie logiczne.  
  
  **Przykłady**  
  
  Poniższy przykład sprawdza obiektów JSON logiczna, liczba, ciąg, wartość null, obiekt, tablica i niezdefiniowane typów, przy użyciu funkcji IS_PRIMITIVE.  
  
```  
SELECT   
           IS_PRIMITIVE(true) AS isPrim1,   
           IS_PRIMITIVE(1) AS isPrim2,  
           IS_PRIMITIVE("value") AS isPrim3,   
           IS_PRIMITIVE(null) AS isPrim4,  
           IS_PRIMITIVE({prop: "value"}) AS isPrim5,   
           IS_PRIMITIVE([1, 2, 3]) AS isPrim6,  
           IS_PRIMITIVE({prop: "value"}.prop2) AS isPrim7  
```  
  
 W tym miejscu znajduje się zestaw wyników.  
  
```  
[{"isPrim1": true, "isPrim2": true, "isPrim3": true, "isPrim4": true, "isPrim5": false, "isPrim6": false, "isPrim7": false}]  
```  
  
####  <a name="bk_is_string"></a> IS_STRING  
 Zwraca wartość logiczną wskazującą, czy typ określonego wyrażenia ciągu.  
  
 **Składnia**  
  
```  
IS_STRING(<expression>)  
```  
  
 **Argumenty**  
  
- `expression`  
  
   Jest dowolnym prawidłowym wyrażeniem.  
  
  **Typy zwracane**  
  
  Zwraca wyrażenie logiczne.  
  
  **Przykłady**  
  
  Poniższy przykład sprawdza obiektów JSON logiczna, liczba, ciąg, wartość null, obiekt, tablica i niezdefiniowane typów, przy użyciu funkcji IS_STRING.  
  
```  
SELECT   
       IS_STRING(true) AS isStr1,   
       IS_STRING(1) AS isStr2,  
       IS_STRING("value") AS isStr3,   
       IS_STRING(null) AS isStr4,  
       IS_STRING({prop: "value"}) AS isStr5,   
       IS_STRING([1, 2, 3]) AS isStr6,  
       IS_STRING({prop: "value"}.prop2) AS isStr7  
```  
  
 W tym miejscu znajduje się zestaw wyników.  
  
```  
[{"isStr1":false,"isStr2":false,"isStr3":true,"isStr4":false,"isStr5":false,"isStr6":false,"isStr7":false}] 
```  

## <a id="string-functions"></a>Funkcje ciągów

Następujące funkcje skalarne wykonania operacji na wartość ciągu wejściowego i zwraca ciąg, wartość liczbowa lub Boolean:
  
||||  
|-|-|-|  
|[CONCAT](#bk_concat)|[ZAWIERA](#bk_contains)|[ENDSWITH](#bk_endswith)|  
|[INDEX_OF](#bk_index_of)|[LEFT](#bk_left)|[DŁUGOŚĆ](#bk_length)|  
|[NIŻSZY](#bk_lower)|[PRZYTP](#bk_ltrim)|[ZASTĄP](#bk_replace)|  
|[REPLIKUJ](#bk_replicate)|[REVERSE](#bk_reverse)|[PO PRAWEJ STRONIE](#bk_right)|  
|[RTRIM](#bk_rtrim)|[STARTSWITH](#bk_startswith)|[StringToArray](#bk_stringtoarray)|
|[StringToBoolean](#bk_stringtoboolean)|[StringToNull](#bk_stringtonull)|[StringToNumber](#bk_stringtonumber)|
|[StringToObject](#bk_stringtoobject)|[PODCIĄG](#bk_substring)|[ToString](#bk_tostring)|
|[TRIM](#bk_trim)|[GÓRNY](#bk_upper)||
  
####  <a name="bk_concat"></a> CONCAT  
 Zwraca ciąg, który jest wynikiem połączenia co najmniej dwóch wartości ciągu.  
  
 **Składnia**  
  
```  
CONCAT(<str_expr>, <str_expr> [, <str_expr>])  
```  
  
 **Argumenty**  
  
- `str_expr`  
  
   Jest dowolnym wyrażeniem prawidłowy ciąg.  
  
  **Typy zwracane**  
  
  Zwraca wyrażenie ciągu.  
  
  **Przykłady**  
  
  Poniższy przykład zwraca połączony ciąg z określonymi wartościami.  
  
```  
SELECT CONCAT("abc", "def") AS concat  
```  
  
 W tym miejscu znajduje się zestaw wyników.  
  
```  
[{"concat": "abcdef"}  
```  
  
####  <a name="bk_contains"></a> ZAWIERA  
 Zwraca wartość logiczną wskazującą, czy pierwsze wyrażenie ciągu zawiera drugie.  
  
 **Składnia**  
  
```  
CONTAINS(<str_expr>, <str_expr>)  
```  
  
 **Argumenty**  
  
- `str_expr`  
  
   Jest dowolnym wyrażeniem prawidłowy ciąg.  
  
  **Typy zwracane**  
  
  Zwraca wyrażenie logiczne.  
  
  **Przykłady**  
  
  Poniższy przykład sprawdza, czy "abc" zawiera "ab" i zawiera "d".  
  
```  
SELECT CONTAINS("abc", "ab") AS c1, CONTAINS("abc", "d") AS c2 
```  
  
 W tym miejscu znajduje się zestaw wyników.  
  
```  
[{"c1": true, "c2": false}]  
```  
  
####  <a name="bk_endswith"></a> ENDSWITH  
 Zwraca wartość logiczną wskazującą, czy pierwszy ciąg wyrażenia kończy się na drugi.  
  
 **Składnia**  
  
```  
ENDSWITH(<str_expr>, <str_expr>)  
```  
  
 **Argumenty**  
  
- `str_expr`  
  
   Jest dowolnym wyrażeniem prawidłowy ciąg.  
  
  **Typy zwracane**  
  
  Zwraca wyrażenie logiczne.  
  
  **Przykłady**  
  
  Poniższy przykład zwraca "abc" kończy się ciągiem "b" i "bc".  
  
```  
SELECT ENDSWITH("abc", "b") AS e1, ENDSWITH("abc", "bc") AS e2 
```  
  
 W tym miejscu znajduje się zestaw wyników.  
  
```  
[{"e1": false, "e2": true}]  
```  
  
####  <a name="bk_index_of"></a> INDEX_OF  
 Zwraca pozycję początkową pierwszego wystąpienia drugiego ciągu wyrażenia w ramach pierwszego określonego wyrażenia ciągu lub wartość -1, jeśli ciąg nie zostanie znaleziony.  
  
 **Składnia**  
  
```  
INDEX_OF(<str_expr>, <str_expr>)  
```  
  
 **Argumenty**  
  
- `str_expr`  
  
   Jest dowolnym wyrażeniem prawidłowy ciąg.  
  
  **Typy zwracane**  
  
  Zwraca wartość wyrażenia liczbowego.  
  
  **Przykłady**  
  
  Poniższy przykład zwraca indeks różnych podciągów wewnątrz "abc".  
  
```  
SELECT INDEX_OF("abc", "ab") AS i1, INDEX_OF("abc", "b") AS i2, INDEX_OF("abc", "c") AS i3 
```  
  
 W tym miejscu znajduje się zestaw wyników.  
  
```  
[{"i1": 0, "i2": 1, "i3": -1}]  
```  
  
####  <a name="bk_left"></a> LEFT  
 Zwraca lewą część ciągu z określoną liczbą znaków.  
  
 **Składnia**  
  
```  
LEFT(<str_expr>, <num_expr>)  
```  
  
 **Argumenty**  
  
- `str_expr`  
  
   Jest dowolnym wyrażeniem prawidłowy ciąg.  
  
- `num_expr`  
  
   Jest dowolne prawidłowe wyrażenie liczbowe.  
  
  **Typy zwracane**  
  
  Zwraca wyrażenie ciągu.  
  
  **Przykłady**  
  
  Poniższy przykład zwraca lewej części "abc" dla różnych wartości długości.  
  
```  
SELECT LEFT("abc", 1) AS l1, LEFT("abc", 2) AS l2 
```  
  
 W tym miejscu znajduje się zestaw wyników.  
  
```  
[{"l1": "a", "l2": "ab"}]  
```  
  
####  <a name="bk_length"></a> DŁUGOŚĆ  
 Zwraca liczbę znaków z określonego wyrażenia ciągu.  
  
 **Składnia**  
  
```  
LENGTH(<str_expr>)  
```  
  
 **Argumenty**  
  
- `str_expr`  
  
   Jest dowolnym wyrażeniem prawidłowy ciąg.  
  
  **Typy zwracane**  
  
  Zwraca wyrażenie ciągu.  
  
  **Przykłady**  
  
  Poniższy przykład zwraca długość ciągu.  
  
```  
SELECT LENGTH("abc") AS len 
```  
  
 W tym miejscu znajduje się zestaw wyników.  
  
```  
[{"len": 3}]  
```  
  
####  <a name="bk_lower"></a> NIŻSZY  
 Zwraca wyrażenie ciągu po przekonwertowaniu danych znakowych wielkich liter na małe litery.  
  
 **Składnia**  
  
```  
LOWER(<str_expr>)  
```  
  
 **Argumenty**  
  
- `str_expr`  
  
   Jest dowolnym wyrażeniem prawidłowy ciąg.  
  
  **Typy zwracane**  
  
  Zwraca wyrażenie ciągu.  
  
  **Przykłady**  
  
  Poniższy przykład pokazuje, jak używać NIŻSZYCH w zapytaniu.  
  
```  
SELECT LOWER("Abc") AS lower
```  
  
 W tym miejscu znajduje się zestaw wyników.  
  
```  
[{"lower": "abc"}]  
  
```  
  
####  <a name="bk_ltrim"></a> PRZYTP  
 Zwraca wyrażenie ciągu po usunięciu spacji wiodących.  
  
 **Składnia**  
  
```  
LTRIM(<str_expr>)  
```  
  
 **Argumenty**  
  
- `str_expr`  
  
   Jest dowolnym wyrażeniem prawidłowy ciąg.  
  
  **Typy zwracane**  
  
  Zwraca wyrażenie ciągu.  
  
  **Przykłady**  
  
  Poniższy przykład pokazuje, jak używać LTRIM w zapytaniu.  
  
```  
SELECT LTRIM("  abc") AS l1, LTRIM("abc") AS l2, LTRIM("abc   ") AS l3 
```  
  
 W tym miejscu znajduje się zestaw wyników.  
  
```  
[{"l1": "abc", "l2": "abc", "l3": "abc   "}]  
```  
  
####  <a name="bk_replace"></a> ZASTĄP  
 Zamienia wszystkie wystąpienia określonej wartości ciągu na inną wartość ciągu.  
  
 **Składnia**  
  
```  
REPLACE(<str_expr>, <str_expr>, <str_expr>)  
```  
  
 **Argumenty**  
  
- `str_expr`  
  
   Jest dowolnym wyrażeniem prawidłowy ciąg.  
  
  **Typy zwracane**  
  
  Zwraca wyrażenie ciągu.  
  
  **Przykłady**  
  
  Poniższy przykład przedstawia sposób użycia ZAMIEŃ w zapytaniu.  
  
```  
SELECT REPLACE("This is a Test", "Test", "desk") AS replace 
```  
  
 W tym miejscu znajduje się zestaw wyników.  
  
```  
[{"replace": "This is a desk"}]  
```  
  
####  <a name="bk_replicate"></a> REPLIKACJA  
 Powtarza wartość ciągu określoną liczbę razy.  
  
 **Składnia**  
  
```  
REPLICATE(<str_expr>, <num_expr>)  
```  
  
 **Argumenty**  
  
- `str_expr`  
  
   Jest dowolnym wyrażeniem prawidłowy ciąg.  
  
- `num_expr`  
  
   Jest dowolne prawidłowe wyrażenie liczbowe. W przypadku ujemny lub nieskończona num_expr wynik jest niezdefiniowany.

  > [!NOTE]
  > Maksymalna długość wyniku jest 10 000 znaków, czyli (length(str_expr) * num_expr) < = 10 000.
  
  **Typy zwracane**  
  
  Zwraca wyrażenie ciągu.  
  
  **Przykłady**  
  
  Poniższy przykład przedstawia sposób użycia replikacji w zapytaniu.  
  
```  
SELECT REPLICATE("a", 3) AS replicate  
```  
  
 W tym miejscu znajduje się zestaw wyników.  
  
```  
[{"replicate": "aaa"}]  
```  
  
####  <a name="bk_reverse"></a> REVERSE  
 Zwraca wartość ciągu w odwrotnej kolejności.  
  
 **Składnia**  
  
```  
REVERSE(<str_expr>)  
```  
  
 **Argumenty**  
  
- `str_expr`  
  
   Jest dowolnym wyrażeniem prawidłowy ciąg.  
  
  **Typy zwracane**  
  
  Zwraca wyrażenie ciągu.  
  
  **Przykłady**  
  
  Poniższy przykład przedstawia sposób użycia w zapytaniu w odwrotnej kolejności.  
  
```  
SELECT REVERSE("Abc") AS reverse  
```  
  
 W tym miejscu znajduje się zestaw wyników.  
  
```  
[{"reverse": "cbA"}]  
```  
  
####  <a name="bk_right"></a> PO PRAWEJ STRONIE  
 Zwraca prawą część ciągu z określoną liczbą znaków.  
  
 **Składnia**  
  
```  
RIGHT(<str_expr>, <num_expr>)  
```  
  
 **Argumenty**  
  
- `str_expr`  
  
   Jest dowolnym wyrażeniem prawidłowy ciąg.  
  
- `num_expr`  
  
   Jest dowolne prawidłowe wyrażenie liczbowe.  
  
  **Typy zwracane**  
  
  Zwraca wyrażenie ciągu.  
  
  **Przykłady**  
  
  Poniższy przykład zwraca jego prawej części "abc" dla różnych wartości długości.  
  
```  
SELECT RIGHT("abc", 1) AS r1, RIGHT("abc", 2) AS r2 
```  
  
 W tym miejscu znajduje się zestaw wyników.  
  
```  
[{"r1": "c", "r2": "bc"}]  
```  
  
####  <a name="bk_rtrim"></a> PRZYTK  
 Zwraca wyrażenie ciągu, po usuwa spacje końcowe.  
  
 **Składnia**  
  
```  
RTRIM(<str_expr>)  
```  
  
 **Argumenty**  
  
- `str_expr`  
  
   Jest dowolnym wyrażeniem prawidłowy ciąg.  
  
  **Typy zwracane**  
  
  Zwraca wyrażenie ciągu.  
  
  **Przykłady**  
  
  Poniższy przykład pokazuje, jak używać RTRIM w zapytaniu.  
  
```  
SELECT RTRIM("  abc") AS r1, RTRIM("abc") AS r2, RTRIM("abc   ") AS r3  
```  
  
 W tym miejscu znajduje się zestaw wyników.  
  
```  
[{"r1": "   abc", "r2": "abc", "r3": "abc"}]  
```  
  
####  <a name="bk_startswith"></a> STARTSWITH  
 Zwraca wartość logiczną wskazującą, czy pierwszy ciąg wyrażenia rozpoczyna się od drugiego.  
  
 **Składnia**  
  
```  
STARTSWITH(<str_expr>, <str_expr>)  
```  
  
 **Argumenty**  
  
- `str_expr`  
  
   Jest dowolnym wyrażeniem prawidłowy ciąg.  
  
  **Typy zwracane**  
  
  Zwraca wyrażenie logiczne.  
  
  **Przykłady**  
  
  Poniższy przykład sprawdza, jeśli ciąg "abc" rozpoczyna się znakiem "b" i "a".  
  
```  
SELECT STARTSWITH("abc", "b") AS s1, STARTSWITH("abc", "a") AS s2  
```  
  
 W tym miejscu znajduje się zestaw wyników.  
  
```  
[{"s1": false, "s2": true}]  
```  

  ####  <a name="bk_stringtoarray"></a> StringToArray  
 Zwraca wyrażenie przetłumaczone na tablicę. Jeśli nie można przetłumaczyć wyrażenia, zwraca niezdefiniowane.  
  
 **Składnia**  
  
```  
StringToArray(<expr>)  
```  
  
 **Argumenty**  
  
- `expr`  
  
   Jest dowolne prawidłowe wyrażenie skalarne, mogło zostać ocenione jako wyrażenie tablicy JSON. Należy pamiętać o tym, czy wartości ciągu zagnieżdżonych musi być napisana przy użyciu podwójnych cudzysłowów był prawidłowy. Aby uzyskać więcej informacji na temat formatu JSON, zobacz [json.org](https://json.org/)
  
  **Typy zwracane**  
  
  Zwraca wyrażenie tablicy lub jest niezdefiniowana.  
  
  **Przykłady**  
  
  Poniższy przykład pokazuje, jak StringToArray zachowuje się na różnych urządzeniach. 
  
 Poniżej przedstawiono przykłady prawidłowych danych wejściowych.

```
SELECT 
    StringToArray('[]') AS a1, 
    StringToArray("[1,2,3]") AS a2,
    StringToArray("[\"str\",2,3]") AS a3,
    StringToArray('[["5","6","7"],["8"],["9"]]') AS a4,
    StringToArray('[1,2,3, "[4,5,6]",[7,8]]') AS a5
```

W tym miejscu znajduje się zestaw wyników.

```
[{"a1": [], "a2": [1,2,3], "a3": ["str",2,3], "a4": [["5","6","7"],["8"],["9"]], "a5": [1,2,3,"[4,5,6]",[7,8]]}]
```

Oto przykład nieprawidłowe dane wejściowe. 
   
 Apostrofy w tablicy nie jest prawidłowym plikiem JSON.
Mimo że są one prawidłowe w obrębie zapytania, ich nie można przeanalizować prawidłowe tablic. Ciągi znaków w ciągu tablicy należy albo użyć znaków ucieczki "[\\"\\"]" lub otaczającego oferty muszą być pojedynczego "[" "]".

```
SELECT
    StringToArray("['5','6','7']")
```

W tym miejscu znajduje się zestaw wyników.

```
[{}]
```

Poniżej przedstawiono przykłady nieprawidłowe dane wejściowe.
   
 Będzie można przeanalizować wyrażenia przekazanego jako tablicę JSON; następujące nie oceniają do typu tablicy i dlatego zwraca niezdefiniowane.
   
```
SELECT
    StringToArray("["),
    StringToArray("1"),
    StringToArray(NaN),
    StringToArray(false),
    StringToArray(undefined)
```

W tym miejscu znajduje się zestaw wyników.

```
[{}]
```

####  <a name="bk_stringtoboolean"></a> StringToBoolean  
 Zwraca wyrażenie przetłumaczone na wartość logiczną. Jeśli nie można przetłumaczyć wyrażenia, zwraca niezdefiniowane.  
  
 **Składnia**  
  
```  
StringToBoolean(<expr>)  
```  
  
 **Argumenty**  
  
- `expr`  
  
   Jest dowolne prawidłowe wyrażenie skalarne, mogło zostać ocenione jako wyrażenie logiczne.  
  
  **Typy zwracane**  
  
  Zwraca wartość wyrażenia logicznego lub jest niezdefiniowana.  
  
  **Przykłady**  
  
  Poniższy przykład pokazuje, jak StringToBoolean zachowuje się na różnych urządzeniach. 
 
 Poniżej przedstawiono przykłady prawidłowych danych wejściowych.

Odstęp jest dozwolona tylko przed lub po nim "true"/ "false".

```  
SELECT 
    StringToBoolean("true") AS b1, 
    StringToBoolean("    false") AS b2,
    StringToBoolean("false    ") AS b3
```  
  
 W tym miejscu znajduje się zestaw wyników.  
  
```  
[{"b1": true, "b2": false, "b3": false}]
```  

Poniżej przedstawiono przykłady nieprawidłowe dane wejściowe.

 Wartości logiczne jest uwzględniana wielkość liter i musi być napisana przy użyciu małych liter to "true" i "false".

```  
SELECT 
    StringToBoolean("TRUE"),
    StringToBoolean("False")
```  

W tym miejscu znajduje się zestaw wyników.  
  
```  
[{}]
``` 

Będzie można przeanalizować wyrażenia przekazanego jako wyrażenie logiczne; te dane wejściowe nie oceniają typu Boolean i dlatego zwraca niezdefiniowane.

```  
SELECT 
    StringToBoolean("null"),
    StringToBoolean(undefined),
    StringToBoolean(NaN), 
    StringToBoolean(false), 
    StringToBoolean(true)
```  

W tym miejscu znajduje się zestaw wyników.  
  
```  
[{}]
```  

####  <a name="bk_stringtonull"></a> StringToNull  
 Zwraca wyrażenie przekonwertowana na wartość null. Jeśli nie można przetłumaczyć wyrażenia, zwraca niezdefiniowane.  
  
 **Składnia**  
  
```  
StringToNull(<expr>)  
```  
  
 **Argumenty**  
  
- `expr`  
  
   Jest dowolne prawidłowe wyrażenie skalarne, mogło zostać ocenione jako wyrażenie o wartości null.
  
  **Typy zwracane**  
  
  Zwraca wyrażenie o wartości null lub jest niezdefiniowana.  
  
  **Przykłady**  
  
  Poniższy przykład pokazuje, jak StringToNull zachowuje się na różnych urządzeniach. 

Poniżej przedstawiono przykłady prawidłowych danych wejściowych.

 Odstęp jest dozwolona tylko przed lub po nim wartości "null".

```  
SELECT 
    StringToNull("null") AS n1, 
    StringToNull("  null ") AS n2,
    IS_NULL(StringToNull("null   ")) AS n3
```  
  
 W tym miejscu znajduje się zestaw wyników.  
  
```  
[{"n1": null, "n2": null, "n3": true}]
```  

Poniżej przedstawiono przykłady nieprawidłowe dane wejściowe.

Wartość null jest uwzględniana wielkość liter i musi być napisana przy użyciu wszystkie małe litery, czyli "null".

```  
SELECT    
    StringToNull("NULL"),
    StringToNull("Null")
```  
  
 W tym miejscu znajduje się zestaw wyników.  
  
```  
[{}]
```  

Będzie można przeanalizować wyrażenia przekazanego jako wyrażenie o wartości null; te dane wejściowe nie oceniają do typu o wartości null i dlatego zwraca niezdefiniowane.

```  
SELECT    
    StringToNull("true"), 
    StringToNull(false), 
    StringToNull(undefined),
    StringToNull(NaN) 
```  
  
 W tym miejscu znajduje się zestaw wyników.  
  
```  
[{}]
```  

####  <a name="bk_stringtonumber"></a> StringToNumber  
 Zwraca wyrażenie przetłumaczone na liczbę. Jeśli nie można przetłumaczyć wyrażenia, zwraca niezdefiniowane.  
  
 **Składnia**  
  
```  
StringToNumber(<expr>)  
```  
  
 **Argumenty**  
  
- `expr`  
  
   Jest dowolne prawidłowe wyrażenie skalarne, mogło zostać ocenione jako wyrażenie numer JSON. Liczby w formacie JSON musi być liczbą całkowitą lub zmiennoprzecinkowej. Aby uzyskać więcej informacji na temat formatu JSON, zobacz [json.org](https://json.org/)  
  
  **Typy zwracane**  
  
  Zwraca wyrażenie lub jest niezdefiniowana.  
  
  **Przykłady**  
  
  Poniższy przykład pokazuje, jak StringToNumber zachowuje się na różnych urządzeniach. 

Odstęp jest dozwolona tylko przed lub po nim numer.

```  
SELECT 
    StringToNumber("1.000000") AS num1, 
    StringToNumber("3.14") AS num2,
    StringToNumber("   60   ") AS num3, 
    StringToNumber("-1.79769e+308") AS num4
```  
  
 W tym miejscu znajduje się zestaw wyników.  
  
```  
{{"num1": 1, "num2": 3.14, "num3": 60, "num4": -1.79769e+308}}
```  

W formacie JSON, prawidłowy numer musi być albo być liczbą całkowitą lub zmiennoprzecinkowy numer punktu.

```  
SELECT   
    StringToNumber("0xF")
```  
  
 W tym miejscu znajduje się zestaw wyników.  
  
```  
{{}}
```  

Będzie można przeanalizować wyrażenia przekazanego jako wyrażenie; te dane wejściowe nie oceniają wpisz numer i dlatego zwraca niezdefiniowane. 

```  
SELECT 
    StringToNumber("99     54"),   
    StringToNumber(undefined),
    StringToNumber("false"),
    StringToNumber(false),
    StringToNumber(" "),
    StringToNumber(NaN)
```  
  
 W tym miejscu znajduje się zestaw wyników.  
  
```  
{{}}
```  

####  <a name="bk_stringtoobject"></a> StringToObject  
 Zwraca wyrażenie tłumaczone na obiekt. Jeśli nie można przetłumaczyć wyrażenia, zwraca niezdefiniowane.  
  
 **Składnia**  
  
```  
StringToObject(<expr>)  
```  
  
 **Argumenty**  
  
- `expr`  
  
   Jest dowolne prawidłowe wyrażenie skalarne, mogło zostać ocenione jako wyrażenie obiektu JSON. Należy pamiętać o tym, czy wartości ciągu zagnieżdżonych musi być napisana przy użyciu podwójnych cudzysłowów był prawidłowy. Aby uzyskać więcej informacji na temat formatu JSON, zobacz [json.org](https://json.org/)  
  
  **Typy zwracane**  
  
  Zwraca wyrażenie obiektu lub jest niezdefiniowana.  
  
  **Przykłady**  
  
  Poniższy przykład pokazuje, jak StringToObject zachowuje się na różnych urządzeniach. 
  
 Poniżej przedstawiono przykłady prawidłowych danych wejściowych.

``` 
SELECT 
    StringToObject("{}") AS obj1, 
    StringToObject('{"A":[1,2,3]}') AS obj2,
    StringToObject('{"B":[{"b1":[5,6,7]},{"b2":8},{"b3":9}]}') AS obj3, 
    StringToObject("{\"C\":[{\"c1\":[5,6,7]},{\"c2\":8},{\"c3\":9}]}") AS obj4
``` 

W tym miejscu znajduje się zestaw wyników.

```
[{"obj1": {}, 
  "obj2": {"A": [1,2,3]}, 
  "obj3": {"B":[{"b1":[5,6,7]},{"b2":8},{"b3":9}]},
  "obj4": {"C":[{"c1":[5,6,7]},{"c2":8},{"c3":9}]}}]
```

 Poniżej przedstawiono przykłady nieprawidłowe dane wejściowe.
Mimo że są one prawidłowe w obrębie zapytania, ich nie można przeanalizować do prawidłowych obiektów. Ciągi znaków w ciągu obiektu należy albo użyć znaków ucieczki "{\\"\\":\\" str\\"}" lub otaczającego oferty muszą być pojedynczy "{"":"str"}".

Apostrofy wokół nazwy właściwości nie jest prawidłowym plikiem JSON.

``` 
SELECT 
    StringToObject("{'a':[1,2,3]}")
```

W tym miejscu znajduje się zestaw wyników.

```  
[{}]
```  

Nazwy właściwości bez znaków cudzysłowu otaczające nie są prawidłowym kodem JSON.

``` 
SELECT 
    StringToObject("{a:[1,2,3]}")
```

W tym miejscu znajduje się zestaw wyników.

```  
[{}]
``` 

Poniżej przedstawiono przykłady nieprawidłowe dane wejściowe.

 Będzie można przeanalizować wyrażenia przekazanego jako obiekt JSON; te dane wejściowe nie oceniają do typu obiektu i dlatego zwraca niezdefiniowane.

``` 
SELECT 
    StringToObject("}"),
    StringToObject("{"),
    StringToObject("1"),
    StringToObject(NaN), 
    StringToObject(false), 
    StringToObject(undefined)
``` 
 
 W tym miejscu znajduje się zestaw wyników.

```
[{}]
```

####  <a name="bk_substring"></a> PODCIĄG  
 Zwraca część wyrażenia ciągu, zaczynając od pozycji liczony od zera określony znak i kontynuuje do określonej długości lub do końca ciągu.  
  
 **Składnia**  
  
```  
SUBSTRING(<str_expr>, <num_expr>, <num_expr>)  
```  
  
 **Argumenty**  
  
- `str_expr`  
  
   Jest dowolnym wyrażeniem prawidłowy ciąg.  
  
- `num_expr`  
  
   Jest dowolne prawidłowe wyrażenie liczbowe do oznaczania znakiem początkowym i końcowym.    
  
  **Typy zwracane**  
  
  Zwraca wyrażenie ciągu.  
  
  **Przykłady**  
  
  Poniższy przykład zwraca podciąg "abc", począwszy od stawki 1 i długości 1 znak.  
  
```  
SELECT SUBSTRING("abc", 1, 1) AS substring  
```  
  
 W tym miejscu znajduje się zestaw wyników.  
  
```  
[{"substring": "b"}]  
```  
####  <a name="bk_tostring"></a> ToString  
 Zwraca ciąg reprezentujący wyrażenie skalarne. 
  
 **Składnia**  
  
```  
ToString(<expr>)
```  
  
 **Argumenty**  
  
- `expr`  
  
   Jest dowolne prawidłowe wyrażenie skalarne.  
  
  **Typy zwracane**  
  
  Zwraca wyrażenie ciągu.  
  
  **Przykłady**  
  
  Poniższy przykład pokazuje, jak ToString zachowuje się na różnych urządzeniach.   
  
```  
SELECT 
    ToString(1.0000) AS str1, 
    ToString("Hello World") AS str2, 
    ToString(NaN) AS str3, 
    ToString(Infinity) AS str4,
    ToString(IS_STRING(ToString(undefined))) AS str5, 
    ToString(0.1234) AS str6, 
    ToString(false) AS str7, 
    ToString(undefined) AS str8
```  
  
 W tym miejscu znajduje się zestaw wyników.  
  
```  
[{"str1": "1", "str2": "Hello World", "str3": "NaN", "str4": "Infinity", "str5": "false", "str6": "0.1234", "str7": "false"}]  
```  
 Biorąc pod uwagę następujące dane wejściowe:
```  
{"Products":[{"ProductID":1,"Weight":4,"WeightUnits":"lb"},{"ProductID":2,"Weight":32,"WeightUnits":"kg"},{"ProductID":3,"Weight":400,"WeightUnits":"g"},{"ProductID":4,"Weight":8999,"WeightUnits":"mg"}]}
```    
 Poniższy przykład pokazuje, jak można używać z innymi funkcjami ciąg znaków, takich jak CONCAT ToString.   
 
```  
SELECT 
CONCAT(ToString(p.Weight), p.WeightUnits) 
FROM p in c.Products 
```  

W tym miejscu znajduje się zestaw wyników.  
  
```  
[{"$1":"4lb" },
{"$1":"32kg"},
{"$1":"400g" },
{"$1":"8999mg" }]

```  
Biorąc pod uwagę następujące dane wejściowe.
```
{"id":"08259","description":"Cereals ready-to-eat, KELLOGG, KELLOGG'S CRISPIX","nutrients":[{"id":"305","description":"Caffeine","units":"mg"},{"id":"306","description":"Cholesterol, HDL","nutritionValue":30,"units":"mg"},{"id":"307","description":"Sodium, NA","nutritionValue":612,"units":"mg"},{"id":"308","description":"Protein, ABP","nutritionValue":60,"units":"mg"},{"id":"309","description":"Zinc, ZN","nutritionValue":null,"units":"mg"}]}
```
Poniższy przykład pokazuje, jak ToString można używać z innymi funkcjami ciągu, np. ZAMIANY.   
```
SELECT 
    n.id AS nutrientID,
    REPLACE(ToString(n.nutritionValue), "6", "9") AS nutritionVal
FROM food 
JOIN n IN food.nutrients
```
W tym miejscu znajduje się zestaw wyników.  
 ```
[{"nutrientID":"305"},
{"nutrientID":"306","nutritionVal":"30"},
{"nutrientID":"307","nutritionVal":"912"},
{"nutrientID":"308","nutritionVal":"90"},
{"nutrientID":"309","nutritionVal":"null"}]
``` 
 
####  <a name="bk_trim"></a> TRIM  
 Zwraca wyrażenie ciągu, po usuwa wiodące i końcowe spacje.  
  
 **Składnia**  
  
```  
TRIM(<str_expr>)  
```  
  
 **Argumenty**  
  
- `str_expr`  
  
   Jest dowolnym wyrażeniem prawidłowy ciąg.  
  
  **Typy zwracane**  
  
  Zwraca wyrażenie ciągu.  
  
  **Przykłady**  
  
  Poniższy przykład pokazuje, jak używać TRIM w zapytaniu.  
  
```  
SELECT TRIM("   abc") AS t1, TRIM("   abc   ") AS t2, TRIM("abc   ") AS t3, TRIM("abc") AS t4
```  
  
 W tym miejscu znajduje się zestaw wyników.  
  
```  
[{"t1": "abc", "t2": "abc", "t3": "abc", "t4": "abc"}]  
``` 
####  <a name="bk_upper"></a> GÓRNY  
 Zwraca wyrażenie ciągu po przekonwertowaniu danych znakowych małych liter na wielkie litery.  
  
 **Składnia**  
  
```  
UPPER(<str_expr>)  
```  
  
 **Argumenty**  
  
- `str_expr`  
  
   Jest dowolnym wyrażeniem prawidłowy ciąg.  
  
  **Typy zwracane**  
  
  Zwraca wyrażenie ciągu.  
  
  **Przykłady**  
  
  Poniższy przykład przedstawia sposób użycia w zapytaniu w prawym GÓRNYM  
  
```  
SELECT UPPER("Abc") AS upper  
```  
  
 W tym miejscu znajduje się zestaw wyników.  
  
```  
[{"upper": "ABC"}]  
```

## <a id="array-functions"></a>Funkcje tablicy

Następujące funkcje skalarne wykonania operacji na wartości wejściowej tablicy i zwrócenia numeryczne, wartości tablicy lub atrybut typu wartość logiczna:
  
||||  
|-|-|-|  
|[ARRAY_CONCAT](#bk_array_concat)|[ARRAY_CONTAINS](#bk_array_contains)|[ARRAY_LENGTH](#bk_array_length)|  
|[ARRAY_SLICE](#bk_array_slice)|||  
  
####  <a name="bk_array_concat"></a> ARRAY_CONCAT  
 Zwraca tablicę, która jest wynikiem połączenia co najmniej dwóch wartości tablicy.  
  
 **Składnia**  
  
```  
ARRAY_CONCAT (<arr_expr>, <arr_expr> [, <arr_expr>])  
```  
  
 **Argumenty**  
  
- `arr_expr`  
  
   Jest dowolnym wyrażeniem prawidłową tablicą.  
  
  **Typy zwracane**  
  
  Zwraca wyrażenie tablicy.  
  
  **Przykłady**  
  
  Poniższy przykład sposobu łączenia dwóch tablic.  
  
```  
SELECT ARRAY_CONCAT(["apples", "strawberries"], ["bananas"]) AS arrayConcat 
```  
  
 W tym miejscu znajduje się zestaw wyników.  
  
```  
[{"arrayConcat": ["apples", "strawberries", "bananas"]}]  
```  
  
####  <a name="bk_array_contains"></a> ARRAY_CONTAINS  
Zwraca wartość logiczną wskazującą, czy tablica zawiera określoną wartość. Możesz sprawdzić pod kątem dopasowania częściowego lub pełnego obiektu za pomocą wyrażenia logicznego w ramach polecenia. 

**Składnia**  
  
```  
ARRAY_CONTAINS (<arr_expr>, <expr> [, bool_expr])  
```  
  
 **Argumenty**  
  
- `arr_expr`  
  
   Jest dowolnym wyrażeniem prawidłową tablicą.  
  
- `expr`  
  
   Jest dowolnym prawidłowym wyrażeniem.  

- `bool_expr`  
  
   Jest dowolne wyrażenie logiczne. Jeśli jest ustawiona "true'and, jeśli wartość wyszukiwania jest obiektem, polecenie sprawdza, czy dopasowanie częściowe (obiekt wyszukiwania jest jednym z obiektów podzbiór). Jeśli jest ustawiona na "false", polecenie sprawdza pełne dopasowanie wszystkich obiektów w tablicy. Wartość domyślna, jeśli nie zostanie określony, to false. 
  
  **Typy zwracane**  
  
  Zwraca wartość typu Boolean.  
  
  **Przykłady**  
  
  Poniższy przykład jak sprawdzić, czy członkostwo w tablicy przy użyciu ARRAY_CONTAINS.  
  
```  
SELECT   
           ARRAY_CONTAINS(["apples", "strawberries", "bananas"], "apples") AS b1,  
           ARRAY_CONTAINS(["apples", "strawberries", "bananas"], "mangoes") AS b2  
```  
  
 W tym miejscu znajduje się zestaw wyników.  
  
```  
[{"b1": true, "b2": false}]  
```  

Poniższy przykład jak sprawdzić, czy częściowym JSON w tablicy przy użyciu ARRAY_CONTAINS.  
  
```  
SELECT  
    ARRAY_CONTAINS([{"name": "apples", "fresh": true}, {"name": "strawberries", "fresh": true}], {"name": "apples"}, true) AS b1, 
    ARRAY_CONTAINS([{"name": "apples", "fresh": true}, {"name": "strawberries", "fresh": true}], {"name": "apples"}) AS b2,
    ARRAY_CONTAINS([{"name": "apples", "fresh": true}, {"name": "strawberries", "fresh": true}], {"name": "mangoes"}, true) AS b3 
```  
  
 W tym miejscu znajduje się zestaw wyników.  
  
```  
[{
  "b1": true,
  "b2": false,
  "b3": false
}] 
```  
  
####  <a name="bk_array_length"></a> ARRAY_LENGTH  
 Zwraca liczbę elementów określonego wyrażenia tablicy.  
  
 **Składnia**  
  
```  
ARRAY_LENGTH(<arr_expr>)  
```  
  
 **Argumenty**  
  
- `arr_expr`  
  
   Jest dowolnym wyrażeniem prawidłową tablicą.  
  
  **Typy zwracane**  
  
  Zwraca wartość wyrażenia liczbowego.  
  
  **Przykłady**  
  
  Poniższy przykład sposobu uzyskania długości tablicy przy użyciu ARRAY_LENGTH.  
  
```  
SELECT ARRAY_LENGTH(["apples", "strawberries", "bananas"]) AS len  
```  
  
 W tym miejscu znajduje się zestaw wyników.  
  
```  
[{"len": 3}]  
```  
  
####  <a name="bk_array_slice"></a> ARRAY_SLICE  
 Zwraca część wyrażenia tablicy.
  
 **Składnia**  
  
```  
ARRAY_SLICE (<arr_expr>, <num_expr> [, <num_expr>])  
```  
  
 **Argumenty**  
  
- `arr_expr`  
  
   Jest dowolnym wyrażeniem prawidłową tablicą.  
  
- `num_expr`  
  
   Liczony od zera indeksu liczbowego od którego należy rozpocząć tablicy. Ujemne wartości mogą być używane, aby określić indeks początkowy względem ostatniego elementu odwołań do tablic tj -1 do ostatniego elementu w tablicy.  

- `num_expr`  

   Maksymalna liczba elementów w tablicy wynikowe.    

  **Typy zwracane**  
  
  Zwraca wyrażenie tablicy.  
  
  **Przykłady**  
  
  Poniższy przykład pokazuje, jak uzyskać różne wycinków tablicy przy użyciu ARRAY_SLICE.  
  
```  
SELECT
           ARRAY_SLICE(["apples", "strawberries", "bananas"], 1) AS s1,  
           ARRAY_SLICE(["apples", "strawberries", "bananas"], 1, 1) AS s2,
           ARRAY_SLICE(["apples", "strawberries", "bananas"], -2, 1) AS s3,
           ARRAY_SLICE(["apples", "strawberries", "bananas"], -2, 2) AS s4,
           ARRAY_SLICE(["apples", "strawberries", "bananas"], 1, 0) AS s5,
           ARRAY_SLICE(["apples", "strawberries", "bananas"], 1, 1000) AS s6,
           ARRAY_SLICE(["apples", "strawberries", "bananas"], 1, -100) AS s7      
  
```  
  
 W tym miejscu znajduje się zestaw wyników.  
  
```  
[{  
           "s1": ["strawberries", "bananas"],   
           "s2": ["strawberries"],
           "s3": ["strawberries"],  
           "s4": ["strawberries", "bananas"], 
           "s5": [],
           "s6": ["strawberries", "bananas"],
           "s7": [] 
}]  
```  
## <a id="date-time-functions"></a>Data i godzina — funkcja

Następujące funkcje skalarne umożliwiają uzyskanie bieżąca data i Godzina UTC w dwóch formach; sygnaturę czasową liczbowe, którego wartością jest epoki systemu Unix (w milisekundach) lub jako ciąg, który jest zgodny z formatu ISO 8601. 

|||
|-|-|
|[GETCURRENTDATETIME](#bk_get_current_date_time)|[GETCURRENTTIMESTAMP](#bk_get_current_timestamp)||

####  <a name="bk_get_current_date_time"></a> GETCURRENTDATETIME
 Zwraca bieżące Data i Godzina UTC jako ciąg ISO 8601.
  
 **Składnia**
  
```
GETCURRENTDATETIME ()
```
  
  **Typy zwracane**
  
  Zwraca bieżący UTC daty i godziny ISO 8601 wartość ciągu. 

  To wymaganie jest wyrażone w formacie RRRR-MM-DDThh:mm:ss.sssZ gdzie:
  
  |||
  |-|-|
  |RRRR|Czterocyfrowy rok|
  |MM|dwucyfrowy miesiąc (01 = stycznia, itp.)|
  |DD|dwucyfrowy dzień miesiąca (01 do 31)|
  |T|signifier z początkiem elementów czasu|
  |hh|Godzina dwóch cyfr (od 00 do 23)|
  |mm|dwie minuty cyfry (od 00 do 59)|
  |ss|dwie cyfry sekund (od 00 do 59)|
  |.sss|trzy cyfry dziesiętne ułamków sekund|
  |Z|Oznaczenie UTC (Coordinated Universal Time)||
  
  Aby uzyskać szczegółowe informacje na temat formatu ISO 8601, zobacz [ISO_8601](https://en.wikipedia.org/wiki/ISO_8601)

  **Uwagi**

  GETCURRENTDATETIME to niedeterministyczna funkcja. 
  
  Wynik zwracany jest UTC (Coordinated Universal Time).

  **Przykłady**  
  
  Poniższy przykład pokazuje, jak można pobrać bieżącego czasu daty UTC przy użyciu wbudowanej funkcji GetCurrentDateTime.
  
```  
SELECT GETCURRENTDATETIME() AS currentUtcDateTime
```  
  
 Oto z przykładowego zestawu wyników.
  
```  
[{
  "currentUtcDateTime": "2019-05-03T20:36:17.784Z"
}]  
```  

####  <a name="bk_get_current_timestamp"></a> GETCURRENTTIMESTAMP
 Zwraca liczbę milisekund, które upłynęły od czwartek, 1 stycznia 1970 r. 00:00:00. 
  
 **Składnia**  
  
```  
GETCURRENTTIMESTAMP ()  
```  
  
  **Typy zwracane**  
  
  Zwraca wartość liczbową bieżącą liczbę milisekund, które upłynęły od początku epoki Unix czyli liczbę milisekund, które upłynęły od czwartek, 1 stycznia 1970 r. 00:00:00.

  **Uwagi**

  GETCURRENTTIMESTAMP to niedeterministyczna funkcja.
  
  Wynik zwracany jest UTC (Coordinated Universal Time).

  **Przykłady**  
  
  Poniższy przykład pokazuje, jak uzyskać bieżącą sygnaturę czasową, za pomocą wbudowanych funkcji GetCurrentTimestamp.
  
```  
SELECT GETCURRENTTIMESTAMP() AS currentUtcTimestamp
```  
  
 Oto z przykładowego zestawu wyników.
  
```  
[{
  "currentUtcTimestamp": 1556916469065
}]  
```

## <a id="spatial-functions"></a>Funkcje przestrzenne

Usługa Cosmos DB obsługuje następujące wbudowane funkcje OGC (Open Geospatial Consortium) w celu wykonywania zapytań dotyczących danych geoprzestrzennych. Następujące funkcje skalarne wykonania operacji na podstawie wartości wejściowe obiektu przestrzennego i zwracają wartość liczbowa lub Boolean.  
  
|||||
|-|-|-|-|
|[ST_DISTANCE](#bk_st_distance)|[ST_WITHIN](#bk_st_within)|[ST_INTERSECTS](#bk_st_intersects)|[ST_ISVALID](#bk_st_isvalid)|
|[ST_ISVALIDDETAILED](#bk_st_isvaliddetailed)||||
  
####  <a name="bk_st_distance"></a> ST_DISTANCE  
 Zwraca odległość między dwoma wyrażeniami GeoJSON typu Point, Polygon lub LineString.  
  
 **Składnia**  
  
```  
ST_DISTANCE (<spatial_expr>, <spatial_expr>)  
```  
  
 **Argumenty**  
  
- `spatial_expr`  
  
   Jest dowolne prawidłowe wyrażenie obiektu GeoJSON punkt, wielokąta lub LineString.  
  
  **Typy zwracane**  
  
  Zwraca wartość wyrażenia liczbowego zawierający odległość. To wymaganie jest wyrażone w metrach dla systemu odniesienia domyślne.  
  
  **Przykłady**  
  
  Poniższy przykład pokazuje, jak zwrócić wszystkie dokumenty rodziny, które znajdują się w 30 km określonej lokalizacji za pomocą wbudowanych funkcji ST_DISTANCE. .  
  
```  
SELECT f.id   
FROM Families f   
WHERE ST_DISTANCE(f.location, {'type': 'Point', 'coordinates':[31.9, -4.8]}) < 30000  
```  
  
 W tym miejscu znajduje się zestaw wyników.  
  
```  
[{  
  "id": "WakefieldFamily"  
}]  
```  
  
####  <a name="bk_st_within"></a> ST_WITHIN  
 Zwraca wartość wskazującą, czy obiekt GeoJSON (punkt, wielokąta lub LineString) określona w pierwszym argumencie znajduje się w GeoJSON (punkt wielokąta i LineString) w drugim argumencie wyrażenia logicznego.  
  
 **Składnia**  
  
```  
ST_WITHIN (<spatial_expr>, <spatial_expr>)  
```  
  
 **Argumenty**  
  
- `spatial_expr`  
  
   Jest dowolne prawidłowe wyrażenie obiektu GeoJSON punkt, wielokąta lub LineString.  
 
- `spatial_expr`  
  
   Jest dowolne prawidłowe wyrażenie obiektu GeoJSON punkt, wielokąta lub LineString.  
  
  **Typy zwracane**  
  
  Zwraca wartość typu Boolean.  
  
  **Przykłady**  
  
  Poniższy przykład pokazuje, jak znaleźć wszystkie dokumenty rodziny Wielokąt przy użyciu ST_WITHIN.  
  
```  
SELECT f.id   
FROM Families f   
WHERE ST_WITHIN(f.location, {  
    'type':'Polygon',   
    'coordinates': [[[31.8, -5], [32, -5], [32, -4.7], [31.8, -4.7], [31.8, -5]]]  
})  
```  
  
 W tym miejscu znajduje się zestaw wyników.  
  
```  
[{ "id": "WakefieldFamily" }]  
```  

####  <a name="bk_st_intersects"></a> ST_INTERSECTS  
 Zwraca wartość wskazującą, czy obiekt GeoJSON (punkt, wielokąta lub LineString) określona w pierwszym argumencie przecina GeoJSON (punkt wielokąta i LineString) w drugim argumencie wyrażenia logicznego.  
  
 **Składnia**  
  
```  
ST_INTERSECTS (<spatial_expr>, <spatial_expr>)  
```  
  
 **Argumenty**  
  
- `spatial_expr`  
  
   Jest dowolne prawidłowe wyrażenie obiektu GeoJSON punkt, wielokąta lub LineString.  
 
- `spatial_expr`  
  
   Jest dowolne prawidłowe wyrażenie obiektu GeoJSON punkt, wielokąta lub LineString.  
  
  **Typy zwracane**  
  
  Zwraca wartość typu Boolean.  
  
  **Przykłady**  
  
  Poniższy przykład pokazuje, jak można znaleźć wszystkich obszarów, które przecinają z danym wielokąta.  
  
```  
SELECT a.id
FROM Areas a
WHERE ST_INTERSECTS(a.location, {  
    'type':'Polygon',
    'coordinates': [[[31.8, -5], [32, -5], [32, -4.7], [31.8, -4.7], [31.8, -5]]]  
})  
```  
  
 W tym miejscu znajduje się zestaw wyników.  
  
```  
[{ "id": "IntersectingPolygon" }]  
```  
  
####  <a name="bk_st_isvalid"></a> ST_ISVALID  
 Zwraca wartość logiczną wskazującą, czy określone wyrażenie GeoJSON typu Point, Polygon lub LineString jest nieprawidłowe.  
  
 **Składnia**  
  
```  
ST_ISVALID(<spatial_expr>)  
```  
  
 **Argumenty**
  
- `spatial_expr`  
  
   Jest dowolne prawidłowe wyrażenie GeoJSON punkt, wielokąta lub LineString.  
  
  **Typy zwracane**  
  
  Zwraca wyrażenie logiczne.  
  
  **Przykłady**  
  
  Poniższy przykład pokazuje, jak sprawdzić, czy punkt jest prawidłowy, za pomocą ST_VALID.  
  
  Na przykład ten punkt ma wartość szerokości geograficznej, która nie jest prawidłowy zakres wartości [-90, 90], dlatego zwraca wartość false zapytania.  
  
  W przypadku wielokątów specyfikacją GeoJSON wymaga ostatnią parę współrzędnych podane powinna być taka sama jak pierwsza, aby utworzyć kształt zamknięty. Punkty, w ramach Wielokąt musi być określona w kolejności przeciwnie do ruchu wskazówek zegara. Wielokąt podane w kolejności do ruchu wskazówek zegara reprezentuje odwrotność region znajdujący się w nim.  
  
```  
SELECT ST_ISVALID({ "type": "Point", "coordinates": [31.9, -132.8] }) AS b 
```  
  
 W tym miejscu znajduje się zestaw wyników.  
  
```  
[{ "b": false }]  
```  
  
####  <a name="bk_st_isvaliddetailed"></a> ST_ISVALIDDETAILED  
 Zwraca wartość JSON zawierającą wartość logiczną, jeśli określone wyrażenie GeoJSON typu Point, Polygon lub LineString jest prawidłowe, a jeśli jest nieprawidłowe, dodatkowo zwraca przyczynę jako wartość ciągu.  
  
 **Składnia**  
  
```  
ST_ISVALIDDETAILED(<spatial_expr>)  
```  
  
 **Argumenty**  
  
- `spatial_expr`  
  
   Jest dowolne GeoJSON prawidłowe wyrażenie punktów i wielokątów.  
  
  **Typy zwracane**  
  
  Zwraca wartość JSON, zawierająca wartość logiczną wartość, jeśli określone wyrażenie punktu lub Wielokąt GeoJSON jest prawidłowy, a nieprawidłowy, dodatkowo Przyczyna jako wartość ciągu.  
  
  **Przykłady**  
  
  Poniższy przykład sposobu sprawdzania poprawności (ze szczegółowymi informacjami) przy użyciu ST_ISVALIDDETAILED.  
  
```  
SELECT ST_ISVALIDDETAILED({   
  "type": "Polygon",   
  "coordinates": [[ [ 31.8, -5 ], [ 31.8, -4.7 ], [ 32, -4.7 ], [ 32, -5 ] ]]  
}) AS b 
```  
  
 W tym miejscu znajduje się zestaw wyników.  
  
```  
[{  
  "b": {
    "valid": false,
    "reason": "The Polygon input is not valid because the start and end points of the ring number 1 are not the same. Each ring of a polygon must have the same start and end points."   
  }  
}]  
```  

## <a name="next-steps"></a>Kolejne kroki

- [Wprowadzenie do usługi Azure Cosmos DB](introduction.md)
- [UDFs](sql-query-udfs.md)
- [agregacje](sql-query-aggregates.md)