---
title: Wbudowane LUIS jednostek odwołanie - Azure | Dokumentacja firmy Microsoft
titleSuffix: Azure
description: Ten artykuł zawiera listę wbudowane jednostek, które znajdują się w opis języka (LUIS).
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 06/20/2018
ms.author: v-geberr
ms.openlocfilehash: 7ce50e4c0be605e1700a2c18533cb087384f524c
ms.sourcegitcommit: 65b399eb756acde21e4da85862d92d98bf9eba86
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/22/2018
ms.locfileid: "36316888"
---
# <a name="entities-per-culture"></a>Jednostki dla kultury

Opis języka (LUIS) zawiera wbudowane jednostek. Wbudowane jednostki jest dołączony do aplikacji, LUIS zawiera odpowiednie prognozowania jednostki w odpowiedzi punktu końcowego. Wszystkie zniesławiających przykładzie są również etykietami z jednostką. Zachowanie jednostek wbudowane **nie** można modyfikować. Jeśli nie podano inaczej, wbudowane jednostki są dostępne we wszystkich regionach aplikacji LUIS (kultur). W poniższej tabeli przedstawiono wbudowane jednostek, które są obsługiwane w przypadku każdego kultury.

Wbudowane jednostki   |   Angielski (Stany Zjednoczone)<br>```En-us```   |   Francuski (Francja)<br>```fr-FR```   |   Włoski (Włochy)<br>```it-IT```   |   Hiszpański (Hiszpania)<br>```es-ES```   |   Chiński<br>```zh-CN```   |   Niemiecki<br>```de-DE```   |   Portugalski (Brazylia)<br>```pt-BR```   |   Japoński (Japonia)<br>```ja-JP```   |   Koreański (Korea)<br>```ko-kr```   | Francuski (Kanada)<br>```fr-CA```   |   Hiszpański (Meksyk)<br>```es-MX```   |   Holenderski (Holandia)<br>```nl-NL```   |
------|:------:|------|------|------|------|------|------|------|------|------|------|------|
[Wiek](luis-reference-prebuilt-age.md):<br>rok<br>miesiąc<br>tydzień<br>dzień   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |   -   |   -   |   -   |   -   |
[Currency](luis-reference-prebuilt-currency.md):<br>dolara ($)<br>Jednostka ułamkowych (przykład: wartość)  |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |   -   |   -   |   -   |   -   |
[DatetimeV2](luis-reference-prebuilt-datetimev2.md):<br>data<br>DateRange<br>time<br>timerange   |    ✔   |   ✔   |   -   |   ✔   |    ✔   |   -   |   ✔   |   -   |   -   |   -   |   -   |   -   |
[Wymiar](luis-reference-prebuilt-dimension.md):<br>wolumin<br>obszar<br>Waga<br>informacje (przykład: bit/bajtów)<br>długość (przykład: miernika)<br>szybkość (przykład: mil na godzinę)  |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |   -   |   -   |   -   |   -   |
[Wiadomość e-mail](luis-reference-prebuilt-email.md)   |    ✔   |   -   |   -   |   -   |   -   |   -   |   -   |   -   |   -   |   -   |   -   |   -   |
[Numer](luis-reference-prebuilt-number.md)   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |   -   |   -   |   -   |   -   |
[Liczba porządkowa](luis-reference-prebuilt-ordinal.md)   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |   -   |   -   |   -   |   -   |
[Wartość procentowa](luis-reference-prebuilt-percentage.md)   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |   -   |   -   |   -   |   -   |
[Numer telefonu](luis-reference-prebuilt-phonenumber.md)   |    ✔   |   -   |   -   |   -   |   -   |   -   |   -   |   -   |   -   |   -   |   -   |   -   |
[Temperatury](luis-reference-prebuilt-temperature.md):<br>f<br>kelvin —<br>Rankina<br>delisle<br>c   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |   -   |   -   |   -   |   -   |
[Adres URL](luis-reference-prebuilt-url.md)   |    ✔   |   -   |   -   |   -   |   -   |   -   |   -   |   -   |   -   |   -   |   -   |   -   |

Zobacz uwagi na [przestarzałe wbudowane jednostek](luis-reference-prebuilt-deprecated.md)

## <a name="examples-of-prebuilt-entities-in-en-us-culture"></a>Przykładami jednostek wbudowane w en-us kultury
W poniższej tabeli wymieniono wbudowane jednostki z przykładowych danych i wartości zwracanych.

Wbudowane jednostki   |   Przykład utterance   |   JSON
------|------|------|
 ```builtin.age```   |   ```100 year old```   |```{ "type": "builtin.age", "entity": "100 year old" }```|  
 ```builtin.age```   |   ```19 years old```   |```{ "type": "builtin.age", "entity": "19 years old" }```|
 ```builtin.currency```     |   ```1000.00 US dollars```   |```{ "type": "builtin.currency", "entity": "1000.00 us dollars" }```
 ```builtin.currency```     |   ```$ 67.5 B```   |```{ "type": "builtin.currency", "entity": "$ 67.5" }```|
 ```builtin.datetimeV2``` | Zobacz [builtin.datetimeV2](luis-reference-prebuilt-datetimev2.md) | Zobacz [builtin.datetimeV2](luis-reference-prebuilt-datetimev2.md) |
 ```builtin.dimension```     |   ```2 miles```   |```{ "type": "builtin.dimension", "entity": "2 miles" }```|
 ```builtin.dimension```     |  ```650 square kilometers```   |```{ "type": "builtin.dimension", "entity": "650 square kilometers" }```|
 ```builtin.email```     |  ```patti.owens@microsoft.com```   |```{ "type": "builtin.email", "entity": "patti.owens@microsoft.com" }```|
 ```builtin.number```     |  ```ten```   |``` { "type": "builtin.number", "entity": "ten" } ```|
 ```builtin.number```     |   ```3.1415```   |```  { "type": "builtin.number", "entity": "3 . 1415" }``` |
 ```builtin.ordinal```     |   ```first```   |```{ "type": "builtin.ordinal", "entity": "first" }``` |
 ```builtin.ordinal```     |   ```10th```   | ```{ "type": "builtin.ordinal", "entity": "10th" }``` |  
 ```builtin.percentage```   |   ```The stock price increase by 7 $ this year```   |```{ "type": "builtin.percentage", "entity": "7 %" }```|
 ```builtin.phonenumber```   |   ```my mobile is 00 44 161 1234567```   |```{ "type": "builtin.phonenumber", "entity": "00 44 161 1234567" }```|
 ```builtin.temperature```     |   ```10 degrees celsius```   | ```{ "type": "builtin.temperature", "entity": "10 degrees celcius" }```|   
 ```builtin.temperature```     |   ```78 F```   |```{ "type": "builtin.temperature", "entity": "78 f" }```|
 ```builtin.url```     |   ```http://www.luis.ai is a great cognitive service```   |```{ "type": "builtin.url", "entity": "http://www.luis.ai" }```|


## <a name="contribute-to-prebuilt-entity-cultures"></a>Przyczyniają się do kultury wbudowane jednostki
Wbudowane jednostki są tworzone w projekcie open source aparatów rozpoznawania tekstu. Sprawdź [współtworzenia](https://github.com/Microsoft/Recognizers-Text) do projektu. Ten projekt zawiera przykłady waluty na kulturę. 

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się więcej o [numer](luis-reference-prebuilt-number.md), [datetimeV2](luis-reference-prebuilt-datetimev2.md), i [waluty](luis-reference-prebuilt-currency.md) jednostek. 