---
title: Wszystkie wstępnie utworzone jednostki — USŁUGA LUIS
titleSuffix: Azure Cognitive Services
description: Ten artykuł zawiera listy wstępnie utworzonych jednostek, które są zawarte w opisie języka (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 10/03/2019
ms.author: diberry
ms.openlocfilehash: a49452653f8ac4bcc62758d801a235be0929d314
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79219721"
---
# <a name="entities-per-culture-in-your-luis-model"></a>Jednostki na kulturę w modelu usługi LUIS

Zrozumienie języka (LUIS) udostępnia wstępnie utworzone jednostki. Gdy wstępnie utworzone jednostki jest uwzględniony w aplikacji, usługa LUIS zawiera odpowiednie przewidywania jednostki w odpowiedzi punktu końcowego. Wszystkie wypowiedzi przykład są również oznaczone z jednostki. **Nie można** zmodyfikować zachowania wstępnie utworzonych jednostek. O ile nie zaznaczono inaczej, wstępnie utworzone jednostki są dostępne we wszystkich ustawieniach regionalnych aplikacji usługi LUIS (kultury). W poniższej tabeli przedstawiono wstępnie utworzone jednostki, które są obsługiwane dla każdej kultury.

|Culture|Subkultur|Uwagi|
|--|--|--|
|Chiński|[zh-CN](#chinese-entity-support)||
|Niderlandzki|[nl-NL](#dutch-entity-support)||
|Polski|[pl-USA (amerykański)](#english-american-entity-support)||
|Francuski|[fr-CA (Kanada),](#french-canadian-entity-support) [fr-FR (Francja),](#french-france-entity-support) ||
|Niemiecki|[de-DE](#german-entity-support)||
|Włoski|[it-IT](#italian-entity-support)||
|Japoński|[ja-JP](#japanese-entity-support)||
|Koreański|[ko-KR](#korean-entity-support)||
|Portugalski|[pt-BR (Brazylia)](#portuguese-brazil-entity-support)||
|Hiszpański|[es-ES (Hiszpania)](#spanish-spain-entity-support), [es-MX (Meksyk)](#spanish-mexico-entity-support)||
|Turecki|[Turecki](#turkish-entity-support)|Brak wstępnie utworzonych jednostek obsługiwanych w języku tureckim|

## <a name="prediction-endpoint-runtime"></a>Przewidywanie środowiska wykonawczego punktu końcowego

Dostępność wstępnie utworzonej jednostki w określonym języku jest określana przez wersję środowiska wykonawczego punktu końcowego przewidywania.

## <a name="chinese-entity-support"></a>Wsparcie chińskich podmiotów

Obsługiwane są następujące jednostki:

|Wstępnie skompilowana jednostka|```zh-CN``` |
------|:------:|
[Wiek](luis-reference-prebuilt-age.md):<br>rok<br>miesiąc<br>Tygodniu<br>dzień   |    V2, V3   |
[Waluta (pieniądze)](luis-reference-prebuilt-currency.md):<br>Dolar<br>jednostka ułamkowa (np.: grosz)  |    V2, V3   |
[DatetimeV2](luis-reference-prebuilt-datetimev2.md):<br>date<br>rozmieszczenie dat<br>time<br>rozsyłanie czasu   |    V2, V3   |
[Wymiar :](luis-reference-prebuilt-dimension.md)<br>wolumin<br>obszar<br>Waga<br>informacje (np.: bit/bajt)<br>długość (np. metr)<br>prędkość (np.: mila na godzinę)  |    V2, V3   |
[Adres e-mail](luis-reference-prebuilt-email.md)   |    V2, V3   |
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   |
[Keyphrase](luis-reference-prebuilt-keyphrase.md)   |    -   |
[Numer](luis-reference-prebuilt-number.md)   |    V2, V3   |
[Porządkowych](luis-reference-prebuilt-ordinal.md)   |    V2, V3   |
[Okręg wyborczy OrdinalV2](luis-reference-prebuilt-ordinal-v2.md)   |    -   |
[Procentowe](luis-reference-prebuilt-percentage.md)   |    V2, V3   |
[Nazwa osoby](luis-reference-prebuilt-person.md)   |    V2, V3   |
[Phonenumber](luis-reference-prebuilt-phonenumber.md)   |    V2, V3   |
[Temperatura](luis-reference-prebuilt-temperature.md):<br>Fahrenheita<br>Kelvin<br>rankine (rankine)<br>Delisle<br>Celsjusza   |    V2, V3   |
[Adres URL](luis-reference-prebuilt-url.md)   |    V2, V3   |

## <a name="dutch-entity-support"></a>Wsparcie holenderskich podmiotów

Obsługiwane są następujące jednostki:

|Wstępnie skompilowana jednostka|```nl-NL``` |
------|:------:|
[Wiek](luis-reference-prebuilt-age.md):<br>rok<br>miesiąc<br>Tygodniu<br>dzień   |    V2, V3   |
[Waluta (pieniądze)](luis-reference-prebuilt-currency.md):<br>Dolar<br>jednostka ułamkowa (np.: grosz)  |    V2, V3   |
[Datetime](luis-reference-prebuilt-deprecated.md)   |    -   |
[Wymiar :](luis-reference-prebuilt-dimension.md)<br>wolumin<br>obszar<br>Waga<br>informacje (np.: bit/bajt)<br>długość (np. metr)<br>prędkość (np.: mila na godzinę)  |    V2, V3   |
[Adres e-mail](luis-reference-prebuilt-email.md)   |    V2, V3   |
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   |
[Keyphrase](luis-reference-prebuilt-keyphrase.md)   |    V2, V3   |
[Numer](luis-reference-prebuilt-number.md)   |    V2, V3   |
[Porządkowych](luis-reference-prebuilt-ordinal.md)   |    V2, V3   |
[Okręg wyborczy OrdinalV2](luis-reference-prebuilt-ordinal-v2.md)   |    -   |
[Procentowe](luis-reference-prebuilt-percentage.md)   |    V2, V3   |
[Nazwa osoby](luis-reference-prebuilt-person.md)   |    -   |
[Phonenumber](luis-reference-prebuilt-phonenumber.md)   |    V2, V3   |
[Temperatura](luis-reference-prebuilt-temperature.md):<br>Fahrenheita<br>Kelvin<br>rankine (rankine)<br>Delisle<br>Celsjusza   |    V2, V3   |
[Adres URL](luis-reference-prebuilt-url.md)   |    V2, V3   |

## <a name="english-american-entity-support"></a>Wsparcie jednostki angielskiej (amerykańskiej)

Obsługiwane są następujące jednostki:

|Wstępnie skompilowana jednostka|```en-US``` |
------|:------:|
[Wiek](luis-reference-prebuilt-age.md):<br>rok<br>miesiąc<br>Tygodniu<br>dzień   |    V2, V3   |
[Waluta (pieniądze)](luis-reference-prebuilt-currency.md):<br>Dolar<br>jednostka ułamkowa (np.: grosz)  |    V2, V3   |
[DatetimeV2](luis-reference-prebuilt-datetimev2.md):<br>date<br>rozmieszczenie dat<br>time<br>rozsyłanie czasu   |    V2, V3   |
[Wymiar :](luis-reference-prebuilt-dimension.md)<br>wolumin<br>obszar<br>Waga<br>informacje (np.: bit/bajt)<br>długość (np. metr)<br>prędkość (np.: mila na godzinę)  |    V2, V3   |
[Adres e-mail](luis-reference-prebuilt-email.md)   |    V2, V3   |
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    V2, V3   |
[Keyphrase](luis-reference-prebuilt-keyphrase.md)   |    V2, V3   |
[Numer](luis-reference-prebuilt-number.md)   |    V2, V3   |
[Porządkowych](luis-reference-prebuilt-ordinal.md)   |    V2, V3   |
[Okręg wyborczy OrdinalV2](luis-reference-prebuilt-ordinal-v2.md)   |    V2, V3   |
[Procentowe](luis-reference-prebuilt-percentage.md)   |    V2, V3   |
[Nazwa osoby](luis-reference-prebuilt-person.md)   |    V2, V3   |
[Phonenumber](luis-reference-prebuilt-phonenumber.md)   |    V2, V3   |
[Temperatura](luis-reference-prebuilt-temperature.md):<br>Fahrenheita<br>Kelvin<br>rankine (rankine)<br>Delisle<br>Celsjusza   |    V2, V3   |
[Adres URL](luis-reference-prebuilt-url.md)   |    V2, V3   |

## <a name="french-france-entity-support"></a>Wsparcie podmiotów francuskich (Francja)

Obsługiwane są następujące jednostki:

|Wstępnie skompilowana jednostka|```fr-FR``` |
------|:------:|
[Wiek](luis-reference-prebuilt-age.md):<br>rok<br>miesiąc<br>Tygodniu<br>dzień   |    V2, V3   |
[Waluta (pieniądze)](luis-reference-prebuilt-currency.md):<br>Dolar<br>jednostka ułamkowa (np.: grosz)  |    V2, V3   |
[DatetimeV2](luis-reference-prebuilt-datetimev2.md):<br>date<br>rozmieszczenie dat<br>time<br>rozsyłanie czasu   |    V2, V3   |
[Wymiar :](luis-reference-prebuilt-dimension.md)<br>wolumin<br>obszar<br>Waga<br>informacje (np.: bit/bajt)<br>długość (np. metr)<br>prędkość (np.: mila na godzinę)  |    V2, V3   |
[Adres e-mail](luis-reference-prebuilt-email.md)   |    V2, V3   |
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   |
[Keyphrase](luis-reference-prebuilt-keyphrase.md)   |    V2, V3   |
[Numer](luis-reference-prebuilt-number.md)   |    V2, V3   |
[Porządkowych](luis-reference-prebuilt-ordinal.md)   |    V2, V3   |
[Okręg wyborczy OrdinalV2](luis-reference-prebuilt-ordinal-v2.md)   |    -   |
[Procentowe](luis-reference-prebuilt-percentage.md)   |    V2, V3   |
[Nazwa osoby](luis-reference-prebuilt-person.md)   |   -   |
[Phonenumber](luis-reference-prebuilt-phonenumber.md)   |    V2, V3   |
[Temperatura](luis-reference-prebuilt-temperature.md):<br>Fahrenheita<br>Kelvin<br>rankine (rankine)<br>Delisle<br>Celsjusza   |    V2, V3   |
[Adres URL](luis-reference-prebuilt-url.md)   |    V2, V3   |

## <a name="french-canadian-entity-support"></a>Wsparcie dla podmiotów francuskich (kanadyjskich)

Obsługiwane są następujące jednostki:

|Wstępnie skompilowana jednostka|```fr-CA``` |
------|:------:|
[Wiek](luis-reference-prebuilt-age.md):<br>rok<br>miesiąc<br>Tygodniu<br>dzień   |    V2, V3   |
[Waluta (pieniądze)](luis-reference-prebuilt-currency.md):<br>Dolar<br>jednostka ułamkowa (np.: grosz)  |    V2, V3   |
[DatetimeV2](luis-reference-prebuilt-datetimev2.md):<br>date<br>rozmieszczenie dat<br>time<br>rozsyłanie czasu   |    V2, V3   |
[Wymiar :](luis-reference-prebuilt-dimension.md)<br>wolumin<br>obszar<br>Waga<br>informacje (np.: bit/bajt)<br>długość (np. metr)<br>prędkość (np.: mila na godzinę)  |    V2, V3   |
[Adres e-mail](luis-reference-prebuilt-email.md)   |    V2, V3   |
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   |
[Keyphrase](luis-reference-prebuilt-keyphrase.md)   |    V2, V3   |
[Numer](luis-reference-prebuilt-number.md)   |    V2, V3   |
[Porządkowych](luis-reference-prebuilt-ordinal.md)   |    V2, V3   |
[Okręg wyborczy OrdinalV2](luis-reference-prebuilt-ordinal-v2.md)   |    -   |
[Procentowe](luis-reference-prebuilt-percentage.md)   |    V2, V3   |
[Nazwa osoby](luis-reference-prebuilt-person.md)   |    -   |
[Phonenumber](luis-reference-prebuilt-phonenumber.md)   |    V2, V3   |
[Temperatura](luis-reference-prebuilt-temperature.md):<br>Fahrenheita<br>Kelvin<br>rankine (rankine)<br>Delisle<br>Celsjusza   |    V2, V3   |
[Adres URL](luis-reference-prebuilt-url.md)   |    V2, V3   |

## <a name="german-entity-support"></a>Wsparcie dla podmiotów niemieckich

Obsługiwane są następujące jednostki:

|Wstępnie skompilowana jednostka|```de-DE``` |
------|:------:|
[Wiek](luis-reference-prebuilt-age.md):<br>rok<br>miesiąc<br>Tygodniu<br>dzień   |    V2, V3   |
[Waluta (pieniądze)](luis-reference-prebuilt-currency.md):<br>Dolar<br>jednostka ułamkowa (np.: grosz)  |    V2, V3   |
[DatetimeV2](luis-reference-prebuilt-datetimev2.md):<br>date<br>rozmieszczenie dat<br>time<br>rozsyłanie czasu   |    V2, V3   |
[Wymiar :](luis-reference-prebuilt-dimension.md)<br>wolumin<br>obszar<br>Waga<br>informacje (np.: bit/bajt)<br>długość (np. metr)<br>prędkość (np.: mila na godzinę)  |    V2, V3   |
[Adres e-mail](luis-reference-prebuilt-email.md)   |    V2, V3   |
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   |
[Keyphrase](luis-reference-prebuilt-keyphrase.md)   |    V2, V3   |
[Numer](luis-reference-prebuilt-number.md)   |    V2, V3   |
[Porządkowych](luis-reference-prebuilt-ordinal.md)   |    V2, V3   |
[Okręg wyborczy OrdinalV2](luis-reference-prebuilt-ordinal-v2.md)   |    -   |
[Procentowe](luis-reference-prebuilt-percentage.md)   |    V2, V3   |
[Nazwa osoby](luis-reference-prebuilt-person.md)   |    -   |
[Phonenumber](luis-reference-prebuilt-phonenumber.md)   |    V2, V3   |
[Temperatura](luis-reference-prebuilt-temperature.md):<br>Fahrenheita<br>Kelvin<br>rankine (rankine)<br>Delisle<br>Celsjusza   |    V2, V3   |
[Adres URL](luis-reference-prebuilt-url.md)   |    V2, V3   |

## <a name="italian-entity-support"></a>Wsparcie dla podmiotów włoskich

Włoski prebuilt wiek, waluta, wymiar, liczba, _rozdzielczość procentowa_ zmieniona z V2 i V3 podglądu.

Obsługiwane są następujące jednostki:

|Wstępnie skompilowana jednostka|```it-IT``` |
------|:------:|
[Wiek](luis-reference-prebuilt-age.md):<br>rok<br>miesiąc<br>Tygodniu<br>dzień   |    V2, V3   |
[Waluta (pieniądze)](luis-reference-prebuilt-currency.md):<br>Dolar<br>jednostka ułamkowa (np.: grosz)  |    V2, V3   |
[Datetime](luis-reference-prebuilt-deprecated.md)   |    -   |
[Wymiar :](luis-reference-prebuilt-dimension.md)<br>wolumin<br>obszar<br>Waga<br>informacje (np.: bit/bajt)<br>długość (np. metr)<br>prędkość (np.: mila na godzinę)  |    V2, V3   |
[Adres e-mail](luis-reference-prebuilt-email.md)   |    V2, V3   |
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   |
[Keyphrase](luis-reference-prebuilt-keyphrase.md)   |    V2, V3   |
[Numer](luis-reference-prebuilt-number.md)   |    V2, V3   |
[Porządkowych](luis-reference-prebuilt-ordinal.md)   |    V2, V3   |
[Okręg wyborczy OrdinalV2](luis-reference-prebuilt-ordinal-v2.md)   |    -   |
[Procentowe](luis-reference-prebuilt-percentage.md)   |    V2, V3   |
[Nazwa osoby](luis-reference-prebuilt-person.md)   |    -   |
[Phonenumber](luis-reference-prebuilt-phonenumber.md)   |    V2, V3   |
[Temperatura](luis-reference-prebuilt-temperature.md):<br>Fahrenheita<br>Kelvin<br>rankine (rankine)<br>Delisle<br>Celsjusza   |    V2, V3   |
[Adres URL](luis-reference-prebuilt-url.md)   |    V2, V3   |

## <a name="japanese-entity-support"></a>Obsługa japońskich podmiotów

Obsługiwane są następujące jednostki:

|Wstępnie skompilowana jednostka|```ja-JP``` |
------|:------:|
[Wiek](luis-reference-prebuilt-age.md):<br>rok<br>miesiąc<br>Tygodniu<br>dzień   |    V2, -   |
[Waluta (pieniądze)](luis-reference-prebuilt-currency.md):<br>Dolar<br>jednostka ułamkowa (np.: grosz)  |    V2, -   |
[Datetime](luis-reference-prebuilt-deprecated.md)   |    -   |
[Wymiar :](luis-reference-prebuilt-dimension.md)<br>wolumin<br>obszar<br>Waga<br>informacje (np.: bit/bajt)<br>długość (np. metr)<br>prędkość (np.: mila na godzinę)  |    V2, -   |
[Adres e-mail](luis-reference-prebuilt-email.md)   |    V2, V3   |
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   |
[Keyphrase](luis-reference-prebuilt-keyphrase.md)   |    V2, V3   |
[Numer](luis-reference-prebuilt-number.md)   |    V2, -   |
[Porządkowych](luis-reference-prebuilt-ordinal.md)   |    V2, -   |
[Okręg wyborczy OrdinalV2](luis-reference-prebuilt-ordinal-v2.md)   |    -   |
[Procentowe](luis-reference-prebuilt-percentage.md)   |    V2, -   |
[Nazwa osoby](luis-reference-prebuilt-person.md)   |    -   |
[Phonenumber](luis-reference-prebuilt-phonenumber.md)   |    V2, V3   |
[Temperatura](luis-reference-prebuilt-temperature.md):<br>Fahrenheita<br>Kelvin<br>rankine (rankine)<br>Delisle<br>Celsjusza   |    V2, -   |
[Adres URL](luis-reference-prebuilt-url.md)   |    V2, V3   |

## <a name="korean-entity-support"></a>Wsparcie koreańskiego podmiotu

Obsługiwane są następujące jednostki:

|Wstępnie skompilowana jednostka|```ko-KR``` |
------|:------:|
[Wiek](luis-reference-prebuilt-age.md):<br>rok<br>miesiąc<br>Tygodniu<br>dzień   |    -   |
[Waluta (pieniądze)](luis-reference-prebuilt-currency.md):<br>Dolar<br>jednostka ułamkowa (np.: grosz)  |    -   |
[Datetime](luis-reference-prebuilt-deprecated.md)   |    -   |
[Wymiar :](luis-reference-prebuilt-dimension.md)<br>wolumin<br>obszar<br>Waga<br>informacje (np.: bit/bajt)<br>długość (np. metr)<br>prędkość (np.: mila na godzinę)  |    -   |
[Adres e-mail](luis-reference-prebuilt-email.md)   |    V2, V3   |
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   |
[Keyphrase](luis-reference-prebuilt-keyphrase.md)   |    V2, V3   |
[Numer](luis-reference-prebuilt-number.md)   |    -   |
[Porządkowych](luis-reference-prebuilt-ordinal.md)   |    -   |
[Okręg wyborczy OrdinalV2](luis-reference-prebuilt-ordinal-v2.md)   |    -   |
[Procentowe](luis-reference-prebuilt-percentage.md)   |    -   |
[Nazwa osoby](luis-reference-prebuilt-person.md)   |    -   |
[Phonenumber](luis-reference-prebuilt-phonenumber.md)   |    V2, V3   |
[Temperatura](luis-reference-prebuilt-temperature.md):<br>Fahrenheita<br>Kelvin<br>rankine (rankine)<br>Delisle<br>Celsjusza   |    -   |
[Adres URL](luis-reference-prebuilt-url.md)   |    V2, V3   |

## <a name="portuguese-brazil-entity-support"></a>Wsparcie dla podmiotów portugalskich (Brazylia)

Obsługiwane są następujące jednostki:

|Wstępnie skompilowana jednostka|```pt-BR``` |
------|:------:|
[Wiek](luis-reference-prebuilt-age.md):<br>rok<br>miesiąc<br>Tygodniu<br>dzień   |    V2, V3   |
[Waluta (pieniądze)](luis-reference-prebuilt-currency.md):<br>Dolar<br>jednostka ułamkowa (np.: grosz)  |    V2, V3   |
[DatetimeV2](luis-reference-prebuilt-datetimev2.md):<br>date<br>rozmieszczenie dat<br>time<br>rozsyłanie czasu   |    V2, V3   |
[Wymiar :](luis-reference-prebuilt-dimension.md)<br>wolumin<br>obszar<br>Waga<br>informacje (np.: bit/bajt)<br>długość (np. metr)<br>prędkość (np.: mila na godzinę)  |    V2, V3   |
[Adres e-mail](luis-reference-prebuilt-email.md)   |    V2, V3   |
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   |
[Keyphrase](luis-reference-prebuilt-keyphrase.md)   |    V2, V3   |
[Numer](luis-reference-prebuilt-number.md)   |    V2, V3   |
[Porządkowych](luis-reference-prebuilt-ordinal.md)   |    V2, V3   |
[Okręg wyborczy OrdinalV2](luis-reference-prebuilt-ordinal-v2.md)   |    -   |
[Procentowe](luis-reference-prebuilt-percentage.md)   |    V2, V3   |
[Nazwa osoby](luis-reference-prebuilt-person.md)   |    -   |
[Phonenumber](luis-reference-prebuilt-phonenumber.md)   |    V2, V3   |
[Temperatura](luis-reference-prebuilt-temperature.md):<br>Fahrenheita<br>Kelvin<br>rankine (rankine)<br>Delisle<br>Celsjusza   |    V2, V3   |
[Adres URL](luis-reference-prebuilt-url.md)   |    V2, V3   |

## <a name="spanish-spain-entity-support"></a>Wsparcie podmiotów hiszpańskich (Hiszpania)

Obsługiwane są następujące jednostki:

|Wstępnie skompilowana jednostka|```es-ES``` |
------|:------:|
[Wiek](luis-reference-prebuilt-age.md):<br>rok<br>miesiąc<br>Tygodniu<br>dzień   |    V2, V3   |
[Waluta (pieniądze)](luis-reference-prebuilt-currency.md):<br>Dolar<br>jednostka ułamkowa (np.: grosz)  |    V2, V3   |
[DatetimeV2](luis-reference-prebuilt-datetimev2.md):<br>date<br>rozmieszczenie dat<br>time<br>rozsyłanie czasu   |    V2, V3   |
[Wymiar :](luis-reference-prebuilt-dimension.md)<br>wolumin<br>obszar<br>Waga<br>informacje (np.: bit/bajt)<br>długość (np. metr)<br>prędkość (np.: mila na godzinę)  |    V2, V3   |
[Adres e-mail](luis-reference-prebuilt-email.md)   |    V2, V3   |
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   |
[Keyphrase](luis-reference-prebuilt-keyphrase.md)   |    V2, V3   |
[Numer](luis-reference-prebuilt-number.md)   |    V2, V3   |
[Porządkowych](luis-reference-prebuilt-ordinal.md)   |    V2, V3   |
[Okręg wyborczy OrdinalV2](luis-reference-prebuilt-ordinal-v2.md)   |    -   |
[Procentowe](luis-reference-prebuilt-percentage.md)   |    V2, V3   |
[Nazwa osoby](luis-reference-prebuilt-person.md)   |    -   |
[Phonenumber](luis-reference-prebuilt-phonenumber.md)   |    V2, V3   |
[Temperatura](luis-reference-prebuilt-temperature.md):<br>Fahrenheita<br>Kelvin<br>rankine (rankine)<br>Delisle<br>Celsjusza   |    V2, V3   |
[Adres URL](luis-reference-prebuilt-url.md)   |    V2, V3   |

## <a name="spanish-mexico-entity-support"></a>Wsparcie podmiotów hiszpańskich (Meksyk)

Obsługiwane są następujące jednostki:

|Wstępnie skompilowana jednostka|```es-MX``` |
------|:------:|
[Wiek](luis-reference-prebuilt-age.md):<br>rok<br>miesiąc<br>Tygodniu<br>dzień   |    -   |
[Waluta (pieniądze)](luis-reference-prebuilt-currency.md):<br>Dolar<br>jednostka ułamkowa (np.: grosz)  |    -   |
[DatetimeV2](luis-reference-prebuilt-datetimev2.md):<br>date<br>rozmieszczenie dat<br>time<br>rozsyłanie czasu   |    -   |
[Wymiar :](luis-reference-prebuilt-dimension.md)<br>wolumin<br>obszar<br>Waga<br>informacje (np.: bit/bajt)<br>długość (np. metr)<br>prędkość (np.: mila na godzinę)  |    -   |
[Adres e-mail](luis-reference-prebuilt-email.md)   |    V2, V3   |
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   |
[Keyphrase](luis-reference-prebuilt-keyphrase.md)   |    V2, V3   |
[Numer](luis-reference-prebuilt-number.md)   |    V2, V3   |
[Porządkowych](luis-reference-prebuilt-ordinal.md)   |    -   |
[Okręg wyborczy OrdinalV2](luis-reference-prebuilt-ordinal-v2.md)   |    -   |
[Procentowe](luis-reference-prebuilt-percentage.md)   |    -   |
[Nazwa osoby](luis-reference-prebuilt-person.md)   |    -   |
[Phonenumber](luis-reference-prebuilt-phonenumber.md)   |    V2, V3   |
[Temperatura](luis-reference-prebuilt-temperature.md):<br>Fahrenheita<br>Kelvin<br>rankine (rankine)<br>Delisle<br>Celsjusza   |    -   |
[Adres URL](luis-reference-prebuilt-url.md)   |    V2, V3   |

Wyświetlanie notatek dotyczących [przestarzałych wstępnie utworzonych encji](luis-reference-prebuilt-deprecated.md)

KeyPhrase nie jest dostępny we wszystkich subkulturach portugalskiego (Brazylia) - ```pt-BR```.

## <a name="turkish-entity-support"></a>Wsparcie tureckich podmiotów

**Nie ma wstępnie utworzonych jednostek obsługiwanych w języku tureckim.**

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

## <a name="contribute-to-prebuilt-entity-cultures"></a>Współtworzenie wstępnie utworzonych kultur encji
Wstępnie utworzone jednostki są opracowywane w projekcie open source aparaty rozpoznawania tekstu. [Współtworzenie](https://github.com/Microsoft/Recognizers-Text) projektu. Ten projekt zawiera przykłady waluty na kulturę.

GeographyV2 i PersonName nie są uwzględnione w projekcie Aparaty rozpoznawania tekstu. W przypadku problemów z tymi wstępnie utworzonymi jednostkami otwórz [żądanie pomocy technicznej](../../azure-portal/supportability/how-to-create-azure-support-request.md).

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o [liczbie](luis-reference-prebuilt-number.md), [datetimeV2](luis-reference-prebuilt-datetimev2.md)i encjach [walutowych.](luis-reference-prebuilt-currency.md)
