---
title: Wszystkie wstępnie skompilowane jednostki — LUIS
titleSuffix: Azure Cognitive Services
description: Ten artykuł zawiera listę wstępnie utworzonych jednostek, które są zawarte w Language Understanding (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 06/25/2019
ms.author: diberry
ms.openlocfilehash: 0d77622e22c297cc29c93a5421da5043e396b929
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/09/2019
ms.locfileid: "68933579"
---
# <a name="entities-per-culture-in-your-luis-model"></a>Jednostki na kulturę w modelu LUIS

Language Understanding (LUIS) oferuje wstępnie utworzonych jednostek. W przypadku wstępnie utworzone jednostki znajduje się w aplikacji, usługi LUIS zawiera odpowiednie prognozowania jednostki w odpowiedzi punktu końcowego. Wszystkie wypowiedzi przykład również są oznaczone etykietami z jednostką. Zachowanie ze wstępnie utworzonych jednostek **nie** można modyfikować. Jeśli nie określono inaczej, ze wstępnie utworzonych jednostek są dostępne we wszystkich regionach aplikacji LUIS (kultury). W poniższej tabeli przedstawiono wstępnie utworzone jednostki, które są obsługiwane w przypadku poszczególnych kultur.

|Kultura|Podhodowli|Uwagi|
|--|--|--|
|Chiński|[nazwy zh-CN](#chinese-entity-support)||
|Holenderski|[NL-NL](#dutch-entity-support)||
|Polski|[EN US (amerykański)](#english-american-entity-support)||
|Francuski|[fr-CA (Kanada)](#french-canadian-entity-support), [fr-FR (Francja)](#french-france-entity-support), ||
|Niemiecki|[de-DE.](#german-entity-support)||
|Włoski|[IT-IT](#italian-entity-support)||
|Japoński|[ja-JP](#japanese-entity-support)||
|Koreański|[ko-KR](#korean-entity-support)||
|Portugalski|[pt-BR (Brazylia)](#portuguese-brazil-entity-support)||
|Hiszpański|[es-ES (Hiszpania)](#spanish-spain-entity-support), [es-MX (Meksyk)](#spanish-mexico-entity-support)||
|Turecki|[Turecki](#turkish-entity-support)|Brak wstępnie skompilowanych jednostek obsługiwanych w języku tureckim|

## <a name="chinese-entity-support"></a>Obsługa chińskich jednostki

Są obsługiwane w następujących elementach:

|Wstępnie utworzone jednostki|```zh-CN``` |
------|:------:|
[Wiek](luis-reference-prebuilt-age.md):<br>rok<br>miesiąc<br>tydzień<br>dzień   |    ✔   |
[Waluty (pieniędzy)](luis-reference-prebuilt-currency.md):<br>Dolar<br>ułamkowe jednostki (np: penny)  |    ✔   |
[DatetimeV2](luis-reference-prebuilt-datetimev2.md):<br>date<br>DateRange<br>time<br>timerange   |    ✔   | 
[Wymiar](luis-reference-prebuilt-dimension.md):<br>wolumin<br>Obszar<br>Waga<br>informacje o (np: bitowy/bajtów)<br>długość (np: miernika)<br>szybkość (np: mil na godzinę)  |    ✔   | 
[Wiadomość e-mail](luis-reference-prebuilt-email.md)   |    ✔   | 
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   | 
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    -   | 
[Liczba](luis-reference-prebuilt-number.md)   |    ✔   |  
[Liczba porządkowa](luis-reference-prebuilt-ordinal.md)   |    ✔   |  
[OrdinalV2](luis-reference-prebuilt-ordinal-v2.md)   |    -   |
[Wartość procentowa](luis-reference-prebuilt-percentage.md)   |    ✔   | 
[PersonName](luis-reference-prebuilt-person.md)   |    ✔   | 
[Numer telefonu](luis-reference-prebuilt-phonenumber.md)   |    ✔   | 
[Temperatura](luis-reference-prebuilt-temperature.md):<br>f<br>kelvin<br>Rankina<br>delisle<br>c   |    ✔   | 
[Adres URL](luis-reference-prebuilt-url.md)   |    ✔   |

## <a name="dutch-entity-support"></a>Obsługa Dutch jednostki

Są obsługiwane w następujących elementach:

|Wstępnie utworzone jednostki|```nl-NL``` |
------|:------:|
[Wiek](luis-reference-prebuilt-age.md):<br>rok<br>miesiąc<br>tydzień<br>dzień   |    ✔   |
[Waluty (pieniędzy)](luis-reference-prebuilt-currency.md):<br>Dolar<br>ułamkowe jednostki (np: penny)  |    ✔   |
[Datę](luis-reference-prebuilt-deprecated.md)   |    -   | 
[Wymiar](luis-reference-prebuilt-dimension.md):<br>wolumin<br>Obszar<br>Waga<br>informacje o (np: bitowy/bajtów)<br>długość (np: miernika)<br>szybkość (np: mil na godzinę)  |    ✔   | 
[Wiadomość e-mail](luis-reference-prebuilt-email.md)   |    ✔   | 
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   | 
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    ✔   | 
[Liczba](luis-reference-prebuilt-number.md)   |    ✔   |  
[Liczba porządkowa](luis-reference-prebuilt-ordinal.md)   |    ✔   |  
[OrdinalV2](luis-reference-prebuilt-ordinal-v2.md)   |    -   |
[Wartość procentowa](luis-reference-prebuilt-percentage.md)   |    ✔   | 
[PersonName](luis-reference-prebuilt-person.md)   |    -   | 
[Numer telefonu](luis-reference-prebuilt-phonenumber.md)   |    ✔   | 
[Temperatura](luis-reference-prebuilt-temperature.md):<br>f<br>kelvin<br>Rankina<br>delisle<br>c   |    ✔   | 
[Adres URL](luis-reference-prebuilt-url.md)   |    ✔   |

## <a name="english-american-entity-support"></a>Angielski (amerykański) jednostki pomocy technicznej

Są obsługiwane w następujących elementach:

|Wstępnie utworzone jednostki|```en-US``` |
------|:------:|
[Wiek](luis-reference-prebuilt-age.md):<br>rok<br>miesiąc<br>tydzień<br>dzień   |    ✔   |
[Waluty (pieniędzy)](luis-reference-prebuilt-currency.md):<br>Dolar<br>ułamkowe jednostki (np: penny)  |    ✔   |
[DatetimeV2](luis-reference-prebuilt-datetimev2.md):<br>date<br>DateRange<br>time<br>timerange   |    ✔   | 
[Wymiar](luis-reference-prebuilt-dimension.md):<br>wolumin<br>Obszar<br>Waga<br>informacje o (np: bitowy/bajtów)<br>długość (np: miernika)<br>szybkość (np: mil na godzinę)  |    ✔   | 
[Wiadomość e-mail](luis-reference-prebuilt-email.md)   |    ✔   | 
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    ✔   | 
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    ✔   | 
[Liczba](luis-reference-prebuilt-number.md)   |    ✔   |  
[Liczba porządkowa](luis-reference-prebuilt-ordinal.md)   |    ✔   |  
[OrdinalV2](luis-reference-prebuilt-ordinal-v2.md)   |    ✔   |
[Wartość procentowa](luis-reference-prebuilt-percentage.md)   |    ✔   | 
[PersonName](luis-reference-prebuilt-person.md)   |    ✔   | 
[Numer telefonu](luis-reference-prebuilt-phonenumber.md)   |    ✔   | 
[Temperatura](luis-reference-prebuilt-temperature.md):<br>f<br>kelvin<br>Rankina<br>delisle<br>c   |    ✔   | 
[Adres URL](luis-reference-prebuilt-url.md)   |    ✔   |

## <a name="french-france-entity-support"></a>Francuski (Francja) jednostki obsługi

Są obsługiwane w następujących elementach:

|Wstępnie utworzone jednostki|```fr-FR``` |
------|:------:|
[Wiek](luis-reference-prebuilt-age.md):<br>rok<br>miesiąc<br>tydzień<br>dzień   |    ✔   |
[Waluty (pieniędzy)](luis-reference-prebuilt-currency.md):<br>Dolar<br>ułamkowe jednostki (np: penny)  |    ✔   |
[DatetimeV2](luis-reference-prebuilt-datetimev2.md):<br>date<br>DateRange<br>time<br>timerange   |    ✔   | 
[Wymiar](luis-reference-prebuilt-dimension.md):<br>wolumin<br>Obszar<br>Waga<br>informacje o (np: bitowy/bajtów)<br>długość (np: miernika)<br>szybkość (np: mil na godzinę)  |    ✔   | 
[Wiadomość e-mail](luis-reference-prebuilt-email.md)   |    ✔   | 
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   | 
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    ✔   | 
[Liczba](luis-reference-prebuilt-number.md)   |    ✔   |  
[Liczba porządkowa](luis-reference-prebuilt-ordinal.md)   |    ✔   |
[OrdinalV2](luis-reference-prebuilt-ordinal-v2.md)   |    -   |  
[Wartość procentowa](luis-reference-prebuilt-percentage.md)   |    ✔   | 
[PersonName](luis-reference-prebuilt-person.md)   |   -   | 
[Numer telefonu](luis-reference-prebuilt-phonenumber.md)   |    ✔   | 
[Temperatura](luis-reference-prebuilt-temperature.md):<br>f<br>kelvin<br>Rankina<br>delisle<br>c   |    ✔   | 
[Adres URL](luis-reference-prebuilt-url.md)   |    ✔   |

## <a name="french-canadian-entity-support"></a>Francuski (kanadyjski) jednostki pomocy technicznej

Są obsługiwane w następujących elementach:

|Wstępnie utworzone jednostki|```fr-CA``` |
------|:------:|
[Wiek](luis-reference-prebuilt-age.md):<br>rok<br>miesiąc<br>tydzień<br>dzień   |    ✔   |
[Waluty (pieniędzy)](luis-reference-prebuilt-currency.md):<br>Dolar<br>ułamkowe jednostki (np: penny)  |    ✔   |
[DatetimeV2](luis-reference-prebuilt-datetimev2.md):<br>date<br>DateRange<br>time<br>timerange   |    ✔   | 
[Wymiar](luis-reference-prebuilt-dimension.md):<br>wolumin<br>Obszar<br>Waga<br>informacje o (np: bitowy/bajtów)<br>długość (np: miernika)<br>szybkość (np: mil na godzinę)  |    ✔   | 
[Wiadomość e-mail](luis-reference-prebuilt-email.md)   |    ✔   | 
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   | 
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    ✔   | 
[Liczba](luis-reference-prebuilt-number.md)   |    ✔   |  
[Liczba porządkowa](luis-reference-prebuilt-ordinal.md)   |    ✔   |  
[OrdinalV2](luis-reference-prebuilt-ordinal-v2.md)   |    -   |
[Wartość procentowa](luis-reference-prebuilt-percentage.md)   |    ✔   | 
[PersonName](luis-reference-prebuilt-person.md)   |    -   | 
[Numer telefonu](luis-reference-prebuilt-phonenumber.md)   |    ✔   | 
[Temperatura](luis-reference-prebuilt-temperature.md):<br>f<br>kelvin<br>Rankina<br>delisle<br>c   |    ✔   | 
[Adres URL](luis-reference-prebuilt-url.md)   |    ✔   |

## <a name="german-entity-support"></a>Obsługa jednostki niemieckiego

Są obsługiwane w następujących elementach:

|Wstępnie utworzone jednostki|```de-DE``` |
------|:------:|
[Wiek](luis-reference-prebuilt-age.md):<br>rok<br>miesiąc<br>tydzień<br>dzień   |    ✔   |
[Waluty (pieniędzy)](luis-reference-prebuilt-currency.md):<br>Dolar<br>ułamkowe jednostki (np: penny)  |    ✔   |
[DatetimeV2](luis-reference-prebuilt-datetimev2.md):<br>date<br>DateRange<br>time<br>timerange   |    ✔   | 
[Wymiar](luis-reference-prebuilt-dimension.md):<br>wolumin<br>Obszar<br>Waga<br>informacje o (np: bitowy/bajtów)<br>długość (np: miernika)<br>szybkość (np: mil na godzinę)  |    ✔   | 
[Wiadomość e-mail](luis-reference-prebuilt-email.md)   |    ✔   | 
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   | 
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    ✔   | 
[Liczba](luis-reference-prebuilt-number.md)   |    ✔   |  
[Liczba porządkowa](luis-reference-prebuilt-ordinal.md)   |    ✔   |
[OrdinalV2](luis-reference-prebuilt-ordinal-v2.md)   |    -   |  
[Wartość procentowa](luis-reference-prebuilt-percentage.md)   |    ✔   | 
[PersonName](luis-reference-prebuilt-person.md)   |    -   | 
[Numer telefonu](luis-reference-prebuilt-phonenumber.md)   |    ✔   | 
[Temperatura](luis-reference-prebuilt-temperature.md):<br>f<br>kelvin<br>Rankina<br>delisle<br>c   |    ✔   | 
[Adres URL](luis-reference-prebuilt-url.md)   |    ✔   |

## <a name="italian-entity-support"></a>Obsługa jednostki włoski

Są obsługiwane w następujących elementach:

|Wstępnie utworzone jednostki|```it-IT``` |
------|:------:|
[Wiek](luis-reference-prebuilt-age.md):<br>rok<br>miesiąc<br>tydzień<br>dzień   |    ✔   |
[Waluty (pieniędzy)](luis-reference-prebuilt-currency.md):<br>Dolar<br>ułamkowe jednostki (np: penny)  |    ✔   |
[Datę](luis-reference-prebuilt-deprecated.md)   |    -   | 
[Wymiar](luis-reference-prebuilt-dimension.md):<br>wolumin<br>Obszar<br>Waga<br>informacje o (np: bitowy/bajtów)<br>długość (np: miernika)<br>szybkość (np: mil na godzinę)  |    ✔   | 
[Wiadomość e-mail](luis-reference-prebuilt-email.md)   |    ✔   | 
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   | 
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    ✔   | 
[Liczba](luis-reference-prebuilt-number.md)   |    ✔   |  
[Liczba porządkowa](luis-reference-prebuilt-ordinal.md)   |    ✔   |  
[OrdinalV2](luis-reference-prebuilt-ordinal-v2.md)   |    -   |
[Wartość procentowa](luis-reference-prebuilt-percentage.md)   |    ✔   | 
[PersonName](luis-reference-prebuilt-person.md)   |    -   | 
[Numer telefonu](luis-reference-prebuilt-phonenumber.md)   |    ✔   | 
[Temperatura](luis-reference-prebuilt-temperature.md):<br>f<br>kelvin<br>Rankina<br>delisle<br>c   |    ✔   | 
[Adres URL](luis-reference-prebuilt-url.md)   |    ✔   |

## <a name="japanese-entity-support"></a>Obsługa jednostki japoński

Są obsługiwane w następujących elementach:

|Wstępnie utworzone jednostki|```ja-JP``` |
------|:------:|
[Wiek](luis-reference-prebuilt-age.md):<br>rok<br>miesiąc<br>tydzień<br>dzień   |    ✔   |
[Waluty (pieniędzy)](luis-reference-prebuilt-currency.md):<br>Dolar<br>ułamkowe jednostki (np: penny)  |    ✔   |
[Datę](luis-reference-prebuilt-deprecated.md)   |    -   | 
[Wymiar](luis-reference-prebuilt-dimension.md):<br>wolumin<br>Obszar<br>Waga<br>informacje o (np: bitowy/bajtów)<br>długość (np: miernika)<br>szybkość (np: mil na godzinę)  |    ✔   | 
[Wiadomość e-mail](luis-reference-prebuilt-email.md)   |    ✔   | 
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   | 
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    ✔   | 
[Liczba](luis-reference-prebuilt-number.md)   |    ✔   |  
[Liczba porządkowa](luis-reference-prebuilt-ordinal.md)   |    ✔   |  
[OrdinalV2](luis-reference-prebuilt-ordinal-v2.md)   |    -   |
[Wartość procentowa](luis-reference-prebuilt-percentage.md)   |    ✔   | 
[PersonName](luis-reference-prebuilt-person.md)   |    -   | 
[Numer telefonu](luis-reference-prebuilt-phonenumber.md)   |    ✔   | 
[Temperatura](luis-reference-prebuilt-temperature.md):<br>f<br>kelvin<br>Rankina<br>delisle<br>c   |    ✔   | 
[Adres URL](luis-reference-prebuilt-url.md)   |    ✔   |

## <a name="korean-entity-support"></a>Obsługa koreańskich jednostek

Są obsługiwane w następujących elementach:

|Wstępnie utworzone jednostki|```ko-KR``` |
------|:------:|
[Wiek](luis-reference-prebuilt-age.md):<br>rok<br>miesiąc<br>tydzień<br>dzień   |    -   |
[Waluty (pieniędzy)](luis-reference-prebuilt-currency.md):<br>Dolar<br>ułamkowe jednostki (np: penny)  |    -   |
[Datę](luis-reference-prebuilt-deprecated.md)   |    -   | 
[Wymiar](luis-reference-prebuilt-dimension.md):<br>wolumin<br>Obszar<br>Waga<br>informacje o (np: bitowy/bajtów)<br>długość (np: miernika)<br>szybkość (np: mil na godzinę)  |    -   | 
[Wiadomość e-mail](luis-reference-prebuilt-email.md)   |    ✔   | 
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   | 
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    ✔   | 
[Liczba](luis-reference-prebuilt-number.md)   |    -   |  
[Liczba porządkowa](luis-reference-prebuilt-ordinal.md)   |    -   |  
[OrdinalV2](luis-reference-prebuilt-ordinal-v2.md)   |    -   |
[Wartość procentowa](luis-reference-prebuilt-percentage.md)   |    -   | 
[PersonName](luis-reference-prebuilt-person.md)   |    -   | 
[Numer telefonu](luis-reference-prebuilt-phonenumber.md)   |    ✔   | 
[Temperatura](luis-reference-prebuilt-temperature.md):<br>f<br>kelvin<br>Rankina<br>delisle<br>c   |    -   | 
[Adres URL](luis-reference-prebuilt-url.md)   |    ✔   |

## <a name="portuguese-brazil-entity-support"></a>Obsługa jednostki portugalski (Brazylia)

Są obsługiwane w następujących elementach:

|Wstępnie utworzone jednostki|```pt-BR``` |
------|:------:|
[Wiek](luis-reference-prebuilt-age.md):<br>rok<br>miesiąc<br>tydzień<br>dzień   |    ✔   |
[Waluty (pieniędzy)](luis-reference-prebuilt-currency.md):<br>Dolar<br>ułamkowe jednostki (np: penny)  |    ✔   |
[DatetimeV2](luis-reference-prebuilt-datetimev2.md):<br>date<br>DateRange<br>time<br>timerange   |    ✔   | 
[Wymiar](luis-reference-prebuilt-dimension.md):<br>wolumin<br>Obszar<br>Waga<br>informacje o (np: bitowy/bajtów)<br>długość (np: miernika)<br>szybkość (np: mil na godzinę)  |    ✔   | 
[Wiadomość e-mail](luis-reference-prebuilt-email.md)   |    ✔   | 
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   | 
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    ✔   | 
[Liczba](luis-reference-prebuilt-number.md)   |    ✔   |  
[Liczba porządkowa](luis-reference-prebuilt-ordinal.md)   |    ✔   |  
[OrdinalV2](luis-reference-prebuilt-ordinal-v2.md)   |    -   |
[Wartość procentowa](luis-reference-prebuilt-percentage.md)   |    ✔   | 
[PersonName](luis-reference-prebuilt-person.md)   |    -   | 
[Numer telefonu](luis-reference-prebuilt-phonenumber.md)   |    ✔   | 
[Temperatura](luis-reference-prebuilt-temperature.md):<br>f<br>kelvin<br>Rankina<br>delisle<br>c   |    ✔   | 
[Adres URL](luis-reference-prebuilt-url.md)   |    ✔   |

## <a name="spanish-spain-entity-support"></a>Obsługa jednostki hiszpański (Hiszpania)

Są obsługiwane w następujących elementach:

|Wstępnie utworzone jednostki|```es-ES``` |
------|:------:|
[Wiek](luis-reference-prebuilt-age.md):<br>rok<br>miesiąc<br>tydzień<br>dzień   |    ✔   |
[Waluty (pieniędzy)](luis-reference-prebuilt-currency.md):<br>Dolar<br>ułamkowe jednostki (np: penny)  |    ✔   |
[DatetimeV2](luis-reference-prebuilt-datetimev2.md):<br>date<br>DateRange<br>time<br>timerange   |    ✔   | 
[Wymiar](luis-reference-prebuilt-dimension.md):<br>wolumin<br>Obszar<br>Waga<br>informacje o (np: bitowy/bajtów)<br>długość (np: miernika)<br>szybkość (np: mil na godzinę)  |    ✔   | 
[Wiadomość e-mail](luis-reference-prebuilt-email.md)   |    ✔   | 
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   | 
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    ✔   | 
[Liczba](luis-reference-prebuilt-number.md)   |    ✔   |  
[Liczba porządkowa](luis-reference-prebuilt-ordinal.md)   |    ✔   |  
[OrdinalV2](luis-reference-prebuilt-ordinal-v2.md)   |    -   |
[Wartość procentowa](luis-reference-prebuilt-percentage.md)   |    ✔   | 
[PersonName](luis-reference-prebuilt-person.md)   |    -   | 
[Numer telefonu](luis-reference-prebuilt-phonenumber.md)   |    ✔   | 
[Temperatura](luis-reference-prebuilt-temperature.md):<br>f<br>kelvin<br>Rankina<br>delisle<br>c   |    ✔   | 
[Adres URL](luis-reference-prebuilt-url.md)   |    ✔   |

## <a name="spanish-mexico-entity-support"></a>Obsługa jednostki hiszpański (Meksyk)

Są obsługiwane w następujących elementach:

|Wstępnie utworzone jednostki|```es-MX``` |
------|:------:|
[Wiek](luis-reference-prebuilt-age.md):<br>rok<br>miesiąc<br>tydzień<br>dzień   |    -   |
[Waluty (pieniędzy)](luis-reference-prebuilt-currency.md):<br>Dolar<br>ułamkowe jednostki (np: penny)  |    -   |
[DatetimeV2](luis-reference-prebuilt-datetimev2.md):<br>date<br>DateRange<br>time<br>timerange   |    -   | 
[Wymiar](luis-reference-prebuilt-dimension.md):<br>wolumin<br>Obszar<br>Waga<br>informacje o (np: bitowy/bajtów)<br>długość (np: miernika)<br>szybkość (np: mil na godzinę)  |    -   | 
[Wiadomość e-mail](luis-reference-prebuilt-email.md)   |    ✔   | 
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   | 
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    ✔   | 
[Liczba](luis-reference-prebuilt-number.md)   |    ✔   |  
[Liczba porządkowa](luis-reference-prebuilt-ordinal.md)   |    -   |  
[OrdinalV2](luis-reference-prebuilt-ordinal-v2.md)   |    -   |
[Wartość procentowa](luis-reference-prebuilt-percentage.md)   |    -   | 
[PersonName](luis-reference-prebuilt-person.md)   |    -   | 
[Numer telefonu](luis-reference-prebuilt-phonenumber.md)   |    ✔   | 
[Temperatura](luis-reference-prebuilt-temperature.md):<br>f<br>kelvin<br>Rankina<br>delisle<br>c   |    -   | 
[Adres URL](luis-reference-prebuilt-url.md)   |    ✔   |

Zobacz uwagi na [przestarzałe ze wstępnie utworzonych jednostek](luis-reference-prebuilt-deprecated.md)

KeyPhrase nie jest dostępny w wszystkich podhodowli z portugalski (Brazylia) — ```pt-BR```.

## <a name="turkish-entity-support"></a>Wsparcie tureckiej jednostki

**Brak wstępnie skompilowanych jednostek obsługiwanych w języku tureckim.** 

<!--

|Prebuilt entity|```tr-tr``` |
------|:------:|
[Age](luis-reference-prebuilt-age.md):<br>year<br>month<br>week<br>day   |    -   |
[Currency (money)](luis-reference-prebuilt-currency.md):<br>dollar<br>fractional unit (ex: penny)  |    -   |
[DatetimeV2](luis-reference-prebuilt-datetimev2.md):<br>date<br>daterange<br>time<br>timerange   |    -   | 
[Dimension](luis-reference-prebuilt-dimension.md):<br>volume<br>area<br>weight<br>information (ex: bit/byte)<br>length (ex: meter)<br>speed (ex: mile per hour)  |    -   | 
[Email](luis-reference-prebuilt-email.md)   |    -   | 
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   | 
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    -   | 
[Number](luis-reference-prebuilt-number.md)   |    -   |  
[Ordinal](luis-reference-prebuilt-ordinal.md)   |    -   |  
[Percentage](luis-reference-prebuilt-percentage.md)   |    -   | 
[PersonName](luis-reference-prebuilt-person.md)   |    -   | 
[Phonenumber](luis-reference-prebuilt-phonenumber.md)   |    -   | 
[Temperature](luis-reference-prebuilt-temperature.md):<br>fahrenheit<br>kelvin<br>rankine<br>delisle<br>celsius   |    -   | 
[URL](luis-reference-prebuilt-url.md)   |    -   |

See notes on [Deprecated prebuilt entities](luis-reference-prebuilt-deprecated.md)


KeyPhrase is not available.
-->

## <a name="contribute-to-prebuilt-entity-cultures"></a>Współtworzenie kultur wstępnie utworzone jednostki
Wstępnie utworzone jednostki są opracowywane w projekcie typu open-source aparatów rozpoznawania tekstu. [Współtworzenie](https://github.com/Microsoft/Recognizers-Text) do projektu. Ten projekt zawiera przykłady waluty dla kultury. 

GeographyV2 i PersonName, nie znajdują się w projekcie aparatów rozpoznawania tekstu. Problemy związane z tych wstępnie utworzonych jednostek, otwórz [żądania pomocy technicznej](../../azure-supportability/how-to-create-azure-support-request.md). 

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się więcej o [numer](luis-reference-prebuilt-number.md), [datetimeV2](luis-reference-prebuilt-datetimev2.md), i [waluty](luis-reference-prebuilt-currency.md) jednostek. 
