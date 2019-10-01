---
title: Wszystkie wstępnie skompilowane jednostki — LUIS
titleSuffix: Azure Cognitive Services
description: Ten artykuł zawiera listy wstępnie utworzonych jednostek, które znajdują się w Language Understanding (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 09/27/2019
ms.author: diberry
ms.openlocfilehash: 66cdedb402392340d38ab9a72f6f19012c359664
ms.sourcegitcommit: 5f0f1accf4b03629fcb5a371d9355a99d54c5a7e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/30/2019
ms.locfileid: "71677622"
---
# <a name="entities-per-culture-in-your-luis-model"></a>Jednostki na kulturę w modelu LUIS

Language Understanding (LUIS) udostępnia wstępnie utworzone jednostki. Gdy wbudowana jednostka jest dołączona do aplikacji, LUIS obejmuje odpowiednie przewidywanie jednostek w odpowiedzi punktu końcowego. Wszystkie przykładowe wyrażenia długości są również oznaczone jednostką. **Nie** można zmodyfikować zachowania wstępnie skompilowanych jednostek. O ile nie wskazano inaczej, wstępnie skompilowane jednostki są dostępne we wszystkich ustawieniach regionalnych aplikacji LUIS (kultury). W poniższej tabeli przedstawiono wstępnie skompilowane jednostki, które są obsługiwane przez każdą kulturę.

|Kultura|Podkultury|Uwagi|
|--|--|--|
|Chiński|[zh-CN](#chinese-entity-support)||
|Holenderski|[NL-NL](#dutch-entity-support)||
|Polski|[EN-US (American)](#english-american-entity-support)||
|Francuski|[fr — CA (Kanada)](#french-canadian-entity-support), [fr-fr (Francja)](#french-france-entity-support), ||
|Niemiecki|[de-DE](#german-entity-support)||
|Włoski|[IT](#italian-entity-support)||
|Japoński|[ja-JP](#japanese-entity-support)||
|Koreański|[Ko — KR](#korean-entity-support)||
|Portugalski|[pt-BR (Brazylia)](#portuguese-brazil-entity-support)||
|Hiszpański|[ES-es (Hiszpania)](#spanish-spain-entity-support), [es-MX (Meksyk)](#spanish-mexico-entity-support)||
|Turecki|[Turecki](#turkish-entity-support)|Brak wstępnie skompilowanych jednostek obsługiwanych w języku tureckim|

## <a name="chinese-entity-support"></a>Obsługa chińskich jednostek

Obsługiwane są następujące jednostki:

|Wstępnie utworzona jednostka|```zh-CN``` |
------|:------:|
[Wiek](luis-reference-prebuilt-age.md):<br>rocznie<br>Bieżącym<br>Ciągu<br>Dzień   |    ✔   |
[Waluta (pieniądze)](luis-reference-prebuilt-currency.md):<br>dolarów<br>Jednostka ułamkowa (np. groszki)  |    ✔   |
[DatetimeV2](luis-reference-prebuilt-datetimev2.md):<br>date<br>daterange<br>time<br>timerange   |    ✔   | 
[Wymiar](luis-reference-prebuilt-dimension.md):<br>dysku<br>Stref<br>Waga<br>informacje (np. bit/Byte)<br>Długość (np. miernik)<br>szybkość (np. droga na godzinę)  |    ✔   | 
[Wiadomość e-mail](luis-reference-prebuilt-email.md)   |    ✔   | 
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   | 
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    -   | 
[Liczba](luis-reference-prebuilt-number.md)   |    ✔   |  
[Liczbą](luis-reference-prebuilt-ordinal.md)   |    ✔   |  
[OrdinalV2](luis-reference-prebuilt-ordinal-v2.md)   |    -   |
[Wartości](luis-reference-prebuilt-percentage.md)   |    ✔   | 
[Nazwisko osoby](luis-reference-prebuilt-person.md)   |    ✔   | 
[PhoneNumber](luis-reference-prebuilt-phonenumber.md)   |    ✔   | 
[Temperatura](luis-reference-prebuilt-temperature.md):<br>Stop<br>Kelvin<br>rankine<br>delisle<br>Celsjusza   |    ✔   | 
[Adres URL](luis-reference-prebuilt-url.md)   |    ✔   |

## <a name="dutch-entity-support"></a>Obsługa jednostek Holenderskich

Obsługiwane są następujące jednostki:

|Wstępnie utworzona jednostka|```nl-NL``` |
------|:------:|
[Wiek](luis-reference-prebuilt-age.md):<br>rocznie<br>Bieżącym<br>Ciągu<br>Dzień   |    ✔   |
[Waluta (pieniądze)](luis-reference-prebuilt-currency.md):<br>dolarów<br>Jednostka ułamkowa (np. groszki)  |    ✔   |
[Datę](luis-reference-prebuilt-deprecated.md)   |    -   | 
[Wymiar](luis-reference-prebuilt-dimension.md):<br>dysku<br>Stref<br>Waga<br>informacje (np. bit/Byte)<br>Długość (np. miernik)<br>szybkość (np. droga na godzinę)  |    ✔   | 
[Wiadomość e-mail](luis-reference-prebuilt-email.md)   |    ✔   | 
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   | 
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    ✔   | 
[Liczba](luis-reference-prebuilt-number.md)   |    ✔   |  
[Liczbą](luis-reference-prebuilt-ordinal.md)   |    ✔   |  
[OrdinalV2](luis-reference-prebuilt-ordinal-v2.md)   |    -   |
[Wartości](luis-reference-prebuilt-percentage.md)   |    ✔   | 
[Nazwisko osoby](luis-reference-prebuilt-person.md)   |    -   | 
[PhoneNumber](luis-reference-prebuilt-phonenumber.md)   |    ✔   | 
[Temperatura](luis-reference-prebuilt-temperature.md):<br>Stop<br>Kelvin<br>rankine<br>delisle<br>Celsjusza   |    ✔   | 
[Adres URL](luis-reference-prebuilt-url.md)   |    ✔   |

## <a name="english-american-entity-support"></a>Obsługa jednostek w języku angielskim (American)

Obsługiwane są następujące jednostki:

|Wstępnie utworzona jednostka|```en-US``` |
------|:------:|
[Wiek](luis-reference-prebuilt-age.md):<br>rocznie<br>Bieżącym<br>Ciągu<br>Dzień   |    ✔   |
[Waluta (pieniądze)](luis-reference-prebuilt-currency.md):<br>dolarów<br>Jednostka ułamkowa (np. groszki)  |    ✔   |
[DatetimeV2](luis-reference-prebuilt-datetimev2.md):<br>date<br>daterange<br>time<br>timerange   |    ✔   | 
[Wymiar](luis-reference-prebuilt-dimension.md):<br>dysku<br>Stref<br>Waga<br>informacje (np. bit/Byte)<br>Długość (np. miernik)<br>szybkość (np. droga na godzinę)  |    ✔   | 
[Wiadomość e-mail](luis-reference-prebuilt-email.md)   |    ✔   | 
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    ✔   | 
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    ✔   | 
[Liczba](luis-reference-prebuilt-number.md)   |    ✔   |  
[Liczbą](luis-reference-prebuilt-ordinal.md)   |    ✔   |  
[OrdinalV2](luis-reference-prebuilt-ordinal-v2.md)   |    ✔   |
[Wartości](luis-reference-prebuilt-percentage.md)   |    ✔   | 
[Nazwisko osoby](luis-reference-prebuilt-person.md)   |    ✔   | 
[PhoneNumber](luis-reference-prebuilt-phonenumber.md)   |    ✔   | 
[Temperatura](luis-reference-prebuilt-temperature.md):<br>Stop<br>Kelvin<br>rankine<br>delisle<br>Celsjusza   |    ✔   | 
[Adres URL](luis-reference-prebuilt-url.md)   |    ✔   |

## <a name="french-france-entity-support"></a>Obsługa jednostek francuskich (Francja)

Obsługiwane są następujące jednostki:

|Wstępnie utworzona jednostka|```fr-FR``` |
------|:------:|
[Wiek](luis-reference-prebuilt-age.md):<br>rocznie<br>Bieżącym<br>Ciągu<br>Dzień   |    ✔   |
[Waluta (pieniądze)](luis-reference-prebuilt-currency.md):<br>dolarów<br>Jednostka ułamkowa (np. groszki)  |    ✔   |
[DatetimeV2](luis-reference-prebuilt-datetimev2.md):<br>date<br>daterange<br>time<br>timerange   |    ✔   | 
[Wymiar](luis-reference-prebuilt-dimension.md):<br>dysku<br>Stref<br>Waga<br>informacje (np. bit/Byte)<br>Długość (np. miernik)<br>szybkość (np. droga na godzinę)  |    ✔   | 
[Wiadomość e-mail](luis-reference-prebuilt-email.md)   |    ✔   | 
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   | 
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    ✔   | 
[Liczba](luis-reference-prebuilt-number.md)   |    ✔   |  
[Liczbą](luis-reference-prebuilt-ordinal.md)   |    ✔   |
[OrdinalV2](luis-reference-prebuilt-ordinal-v2.md)   |    -   |  
[Wartości](luis-reference-prebuilt-percentage.md)   |    ✔   | 
[Nazwisko osoby](luis-reference-prebuilt-person.md)   |   -   | 
[PhoneNumber](luis-reference-prebuilt-phonenumber.md)   |    ✔   | 
[Temperatura](luis-reference-prebuilt-temperature.md):<br>Stop<br>Kelvin<br>rankine<br>delisle<br>Celsjusza   |    ✔   | 
[Adres URL](luis-reference-prebuilt-url.md)   |    ✔   |

## <a name="french-canadian-entity-support"></a>Obsługa jednostek francuskich (kanadyjskich)

Obsługiwane są następujące jednostki:

|Wstępnie utworzona jednostka|```fr-CA``` |
------|:------:|
[Wiek](luis-reference-prebuilt-age.md):<br>rocznie<br>Bieżącym<br>Ciągu<br>Dzień   |    ✔   |
[Waluta (pieniądze)](luis-reference-prebuilt-currency.md):<br>dolarów<br>Jednostka ułamkowa (np. groszki)  |    ✔   |
[DatetimeV2](luis-reference-prebuilt-datetimev2.md):<br>date<br>daterange<br>time<br>timerange   |    ✔   | 
[Wymiar](luis-reference-prebuilt-dimension.md):<br>dysku<br>Stref<br>Waga<br>informacje (np. bit/Byte)<br>Długość (np. miernik)<br>szybkość (np. droga na godzinę)  |    ✔   | 
[Wiadomość e-mail](luis-reference-prebuilt-email.md)   |    ✔   | 
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   | 
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    ✔   | 
[Liczba](luis-reference-prebuilt-number.md)   |    ✔   |  
[Liczbą](luis-reference-prebuilt-ordinal.md)   |    ✔   |  
[OrdinalV2](luis-reference-prebuilt-ordinal-v2.md)   |    -   |
[Wartości](luis-reference-prebuilt-percentage.md)   |    ✔   | 
[Nazwisko osoby](luis-reference-prebuilt-person.md)   |    -   | 
[PhoneNumber](luis-reference-prebuilt-phonenumber.md)   |    ✔   | 
[Temperatura](luis-reference-prebuilt-temperature.md):<br>Stop<br>Kelvin<br>rankine<br>delisle<br>Celsjusza   |    ✔   | 
[Adres URL](luis-reference-prebuilt-url.md)   |    ✔   |

## <a name="german-entity-support"></a>Obsługa jednostek niemieckich

Obsługiwane są następujące jednostki:

|Wstępnie utworzona jednostka|```de-DE``` |
------|:------:|
[Wiek](luis-reference-prebuilt-age.md):<br>rocznie<br>Bieżącym<br>Ciągu<br>Dzień   |    ✔   |
[Waluta (pieniądze)](luis-reference-prebuilt-currency.md):<br>dolarów<br>Jednostka ułamkowa (np. groszki)  |    ✔   |
[DatetimeV2](luis-reference-prebuilt-datetimev2.md):<br>date<br>daterange<br>time<br>timerange   |    ✔   | 
[Wymiar](luis-reference-prebuilt-dimension.md):<br>dysku<br>Stref<br>Waga<br>informacje (np. bit/Byte)<br>Długość (np. miernik)<br>szybkość (np. droga na godzinę)  |    ✔   | 
[Wiadomość e-mail](luis-reference-prebuilt-email.md)   |    ✔   | 
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   | 
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    ✔   | 
[Liczba](luis-reference-prebuilt-number.md)   |    ✔   |  
[Liczbą](luis-reference-prebuilt-ordinal.md)   |    ✔   |
[OrdinalV2](luis-reference-prebuilt-ordinal-v2.md)   |    -   |  
[Wartości](luis-reference-prebuilt-percentage.md)   |    ✔   | 
[Nazwisko osoby](luis-reference-prebuilt-person.md)   |    -   | 
[PhoneNumber](luis-reference-prebuilt-phonenumber.md)   |    ✔   | 
[Temperatura](luis-reference-prebuilt-temperature.md):<br>Stop<br>Kelvin<br>rankine<br>delisle<br>Celsjusza   |    ✔   | 
[Adres URL](luis-reference-prebuilt-url.md)   |    ✔   |

## <a name="italian-entity-support"></a>Obsługa jednostek włoskich

Obsługiwane są następujące jednostki:

|Wstępnie utworzona jednostka|```it-IT``` |
------|:------:|
[Wiek](luis-reference-prebuilt-age.md):<br>rocznie<br>Bieżącym<br>Ciągu<br>Dzień   |    ✔   |
[Waluta (pieniądze)](luis-reference-prebuilt-currency.md):<br>dolarów<br>Jednostka ułamkowa (np. groszki)  |    ✔   |
[Datę](luis-reference-prebuilt-deprecated.md)   |    -   | 
[Wymiar](luis-reference-prebuilt-dimension.md):<br>dysku<br>Stref<br>Waga<br>informacje (np. bit/Byte)<br>Długość (np. miernik)<br>szybkość (np. droga na godzinę)  |    ✔   | 
[Wiadomość e-mail](luis-reference-prebuilt-email.md)   |    ✔   | 
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   | 
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    ✔   | 
[Liczba](luis-reference-prebuilt-number.md)   |    ✔   |  
[Liczbą](luis-reference-prebuilt-ordinal.md)   |    ✔   |  
[OrdinalV2](luis-reference-prebuilt-ordinal-v2.md)   |    -   |
[Wartości](luis-reference-prebuilt-percentage.md)   |    ✔   | 
[Nazwisko osoby](luis-reference-prebuilt-person.md)   |    -   | 
[PhoneNumber](luis-reference-prebuilt-phonenumber.md)   |    ✔   | 
[Temperatura](luis-reference-prebuilt-temperature.md):<br>Stop<br>Kelvin<br>rankine<br>delisle<br>Celsjusza   |    ✔   | 
[Adres URL](luis-reference-prebuilt-url.md)   |    ✔   |

## <a name="japanese-entity-support"></a>Obsługa jednostek japońskich

Obsługiwane są następujące jednostki:

|Wstępnie utworzona jednostka|```ja-JP``` |
------|:------:|
[Wiek](luis-reference-prebuilt-age.md):<br>rocznie<br>Bieżącym<br>Ciągu<br>Dzień   |    ✔   |
[Waluta (pieniądze)](luis-reference-prebuilt-currency.md):<br>dolarów<br>Jednostka ułamkowa (np. groszki)  |    ✔   |
[Datę](luis-reference-prebuilt-deprecated.md)   |    -   | 
[Wymiar](luis-reference-prebuilt-dimension.md):<br>dysku<br>Stref<br>Waga<br>informacje (np. bit/Byte)<br>Długość (np. miernik)<br>szybkość (np. droga na godzinę)  |    ✔   | 
[Wiadomość e-mail](luis-reference-prebuilt-email.md)   |    ✔   | 
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   | 
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    ✔   | 
[Liczba](luis-reference-prebuilt-number.md)   |    ✔   |  
[Liczbą](luis-reference-prebuilt-ordinal.md)   |    ✔   |  
[OrdinalV2](luis-reference-prebuilt-ordinal-v2.md)   |    -   |
[Wartości](luis-reference-prebuilt-percentage.md)   |    ✔   | 
[Nazwisko osoby](luis-reference-prebuilt-person.md)   |    -   | 
[PhoneNumber](luis-reference-prebuilt-phonenumber.md)   |    ✔   | 
[Temperatura](luis-reference-prebuilt-temperature.md):<br>Stop<br>Kelvin<br>rankine<br>delisle<br>Celsjusza   |    ✔   | 
[Adres URL](luis-reference-prebuilt-url.md)   |    ✔   |

## <a name="korean-entity-support"></a>Obsługa jednostek w języku koreańskim

Obsługiwane są następujące jednostki:

|Wstępnie utworzona jednostka|```ko-KR``` |
------|:------:|
[Wiek](luis-reference-prebuilt-age.md):<br>rocznie<br>Bieżącym<br>Ciągu<br>Dzień   |    -   |
[Waluta (pieniądze)](luis-reference-prebuilt-currency.md):<br>dolarów<br>Jednostka ułamkowa (np. groszki)  |    -   |
[Datę](luis-reference-prebuilt-deprecated.md)   |    -   | 
[Wymiar](luis-reference-prebuilt-dimension.md):<br>dysku<br>Stref<br>Waga<br>informacje (np. bit/Byte)<br>Długość (np. miernik)<br>szybkość (np. droga na godzinę)  |    -   | 
[Wiadomość e-mail](luis-reference-prebuilt-email.md)   |    ✔   | 
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   | 
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    ✔   | 
[Liczba](luis-reference-prebuilt-number.md)   |    -   |  
[Liczbą](luis-reference-prebuilt-ordinal.md)   |    -   |  
[OrdinalV2](luis-reference-prebuilt-ordinal-v2.md)   |    -   |
[Wartości](luis-reference-prebuilt-percentage.md)   |    -   | 
[Nazwisko osoby](luis-reference-prebuilt-person.md)   |    -   | 
[PhoneNumber](luis-reference-prebuilt-phonenumber.md)   |    ✔   | 
[Temperatura](luis-reference-prebuilt-temperature.md):<br>Stop<br>Kelvin<br>rankine<br>delisle<br>Celsjusza   |    -   | 
[Adres URL](luis-reference-prebuilt-url.md)   |    ✔   |

## <a name="portuguese-brazil-entity-support"></a>Obsługa jednostek w języku portugalskim (Brazylia)

Obsługiwane są następujące jednostki:

|Wstępnie utworzona jednostka|```pt-BR``` |
------|:------:|
[Wiek](luis-reference-prebuilt-age.md):<br>rocznie<br>Bieżącym<br>Ciągu<br>Dzień   |    ✔   |
[Waluta (pieniądze)](luis-reference-prebuilt-currency.md):<br>dolarów<br>Jednostka ułamkowa (np. groszki)  |    ✔   |
[DatetimeV2](luis-reference-prebuilt-datetimev2.md):<br>date<br>daterange<br>time<br>timerange   |    ✔   | 
[Wymiar](luis-reference-prebuilt-dimension.md):<br>dysku<br>Stref<br>Waga<br>informacje (np. bit/Byte)<br>Długość (np. miernik)<br>szybkość (np. droga na godzinę)  |    ✔   | 
[Wiadomość e-mail](luis-reference-prebuilt-email.md)   |    ✔   | 
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   | 
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    ✔   | 
[Liczba](luis-reference-prebuilt-number.md)   |    ✔   |  
[Liczbą](luis-reference-prebuilt-ordinal.md)   |    ✔   |  
[OrdinalV2](luis-reference-prebuilt-ordinal-v2.md)   |    -   |
[Wartości](luis-reference-prebuilt-percentage.md)   |    ✔   | 
[Nazwisko osoby](luis-reference-prebuilt-person.md)   |    -   | 
[PhoneNumber](luis-reference-prebuilt-phonenumber.md)   |    ✔   | 
[Temperatura](luis-reference-prebuilt-temperature.md):<br>Stop<br>Kelvin<br>rankine<br>delisle<br>Celsjusza   |    ✔   | 
[Adres URL](luis-reference-prebuilt-url.md)   |    ✔   |

## <a name="spanish-spain-entity-support"></a>Obsługa jednostki hiszpańskiej (Hiszpania)

Obsługiwane są następujące jednostki:

|Wstępnie utworzona jednostka|```es-ES``` |
------|:------:|
[Wiek](luis-reference-prebuilt-age.md):<br>rocznie<br>Bieżącym<br>Ciągu<br>Dzień   |    ✔   |
[Waluta (pieniądze)](luis-reference-prebuilt-currency.md):<br>dolarów<br>Jednostka ułamkowa (np. groszki)  |    ✔   |
[DatetimeV2](luis-reference-prebuilt-datetimev2.md):<br>date<br>daterange<br>time<br>timerange   |    ✔   | 
[Wymiar](luis-reference-prebuilt-dimension.md):<br>dysku<br>Stref<br>Waga<br>informacje (np. bit/Byte)<br>Długość (np. miernik)<br>szybkość (np. droga na godzinę)  |    ✔   | 
[Wiadomość e-mail](luis-reference-prebuilt-email.md)   |    ✔   | 
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   | 
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    ✔   | 
[Liczba](luis-reference-prebuilt-number.md)   |    ✔   |  
[Liczbą](luis-reference-prebuilt-ordinal.md)   |    ✔   |  
[OrdinalV2](luis-reference-prebuilt-ordinal-v2.md)   |    -   |
[Wartości](luis-reference-prebuilt-percentage.md)   |    ✔   | 
[Nazwisko osoby](luis-reference-prebuilt-person.md)   |    -   | 
[PhoneNumber](luis-reference-prebuilt-phonenumber.md)   |    ✔   | 
[Temperatura](luis-reference-prebuilt-temperature.md):<br>Stop<br>Kelvin<br>rankine<br>delisle<br>Celsjusza   |    ✔   | 
[Adres URL](luis-reference-prebuilt-url.md)   |    ✔   |

## <a name="spanish-mexico-entity-support"></a>Obsługa jednostki hiszpańskiej (Meksyk)

Obsługiwane są następujące jednostki:

|Wstępnie utworzona jednostka|```es-MX``` |
------|:------:|
[Wiek](luis-reference-prebuilt-age.md):<br>rocznie<br>Bieżącym<br>Ciągu<br>Dzień   |    -   |
[Waluta (pieniądze)](luis-reference-prebuilt-currency.md):<br>dolarów<br>Jednostka ułamkowa (np. groszki)  |    -   |
[DatetimeV2](luis-reference-prebuilt-datetimev2.md):<br>date<br>daterange<br>time<br>timerange   |    -   | 
[Wymiar](luis-reference-prebuilt-dimension.md):<br>dysku<br>Stref<br>Waga<br>informacje (np. bit/Byte)<br>Długość (np. miernik)<br>szybkość (np. droga na godzinę)  |    -   | 
[Wiadomość e-mail](luis-reference-prebuilt-email.md)   |    ✔   | 
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   | 
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    ✔   | 
[Liczba](luis-reference-prebuilt-number.md)   |    ✔   |  
[Liczbą](luis-reference-prebuilt-ordinal.md)   |    -   |  
[OrdinalV2](luis-reference-prebuilt-ordinal-v2.md)   |    -   |
[Wartości](luis-reference-prebuilt-percentage.md)   |    -   | 
[Nazwisko osoby](luis-reference-prebuilt-person.md)   |    -   | 
[PhoneNumber](luis-reference-prebuilt-phonenumber.md)   |    ✔   | 
[Temperatura](luis-reference-prebuilt-temperature.md):<br>Stop<br>Kelvin<br>rankine<br>delisle<br>Celsjusza   |    -   | 
[Adres URL](luis-reference-prebuilt-url.md)   |    ✔   |

Zobacz uwagi dotyczące [przestarzałych wstępnie skompilowanych jednostek](luis-reference-prebuilt-deprecated.md)

KeyPhrase nie jest dostępna we wszystkich podkulturach portugalskich (Brazylia) — ```pt-BR```.

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

## <a name="contribute-to-prebuilt-entity-cultures"></a>Współtworzenie do prekompilowanych kultur jednostek
Wstępnie skompilowane jednostki są opracowywane w projekcie rozpoznawania tekstu Open-Source. [Współtworzenie](https://github.com/Microsoft/Recognizers-Text) projektu. Ten projekt zawiera przykłady walut na kulturę. 

GeographyV2 i PersonName nie są uwzględnione w projekcie rozpoznawania tekstu. W przypadku problemów z tymi wstępnie skompilowanymi jednostkami Otwórz [żądanie pomocy technicznej](../../azure-supportability/how-to-create-azure-support-request.md). 

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej na temat [liczby](luis-reference-prebuilt-number.md), [datetimeV2](luis-reference-prebuilt-datetimev2.md)i jednostek [walutowych](luis-reference-prebuilt-currency.md) . 
