---
title: Obsługa języków - API wyszukiwania w Internecie Bing
titleSuffix: Azure Cognitive Services
description: Lista języków naturalnych, krajów i regionów, które są obsługiwane przez interfejs API wyszukiwania wiadomości Bing.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: article
ms.date: 05/15/2019
ms.author: aahi
ms.openlocfilehash: e657c4678c76e8ff667c1a3f30409fc157f52d16
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2019
ms.locfileid: "65798244"
---
# <a name="language-and-region-support-for-the-bing-web-search-api"></a>Obsługa języka i regionu API wyszukiwania w Internecie Bing

API wyszukiwania w Internecie Bing obsługuje ponad tuzina trzech krajów lub regionów, z których wiele z więcej niż jednym języku. Określanie kraj lub region z zapytaniem pomaga zawęzić wyniki wyszukiwania oparte na tym zainteresowania krajów lub regionów. Wyniki mogą być dostępne łącza do usługi Bing, a te linki mogą lokalizować Bing środowiska użytkownika zgodnie z określonego kraju/regionu lub języka.

Można określić kraj lub region przy użyciu `cc` parametr zapytania. Kraj lub region jest określony, należy określić co najmniej jeden kod języka z [ `Accept-Language` nagłówka](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#headers). Użyj [tabeli rynków](#markets) listę języków obsługiwanych w poszczególnych segmentach rynku.

Alternatywnie, można określić na rynek `mkt` parametr zapytania i kod z **rynków** tabeli. Określanie na rynek, jednocześnie Określa kraj lub region i język preferowany. Można jawnie ustawić język za pomocą `setLang` parametr zapytania.

## <a name="countriesregions"></a>Kraje/regiony

|Kraj/region|Kod|
|-------|----|
|Argentyna|AR|
|Australia|AU|
|Austria|AT|
|Belgia|BE|
|Brazylia|BR|
|Kanada|Urząd certyfikacji|
|Chile|CL|
|Dania|DK|
|Finlandia|FI|
|Francja|PW|
|Niemcy|DE|
|SRA Hongkong|HK|
|Indie|IN|
|Indonezja|ID|
|Włochy|it|
|Japonia|JP|
|Korea Południowa|KR|
|Malezja|MY|
|Meksyk|MX|
|Holandia|NL|
|Nowa Zelandia|NZ|
|Norwegia|NIE|
|Chiny|CN|
|Polska|PL|
|Portugalia|PT|
|Filipiny|PH|
|Rosja|RU|
|Arabia Saudyjska|SA|
|RPA|ZA|
|Hiszpania|ES|
|Szwecja|SE|
|Szwajcaria|CH|
|Tajwan|TW|
|Turcja|TR|
|Zjednoczone Królestwo|GB|
|Stany Zjednoczone Ameryki|USA|

## <a name="markets"></a>Rynki

|Kraj/region|Język|Rynek kodu|
|-------|--------|-----------|
|Argentyna|Hiszpański|ES AR|
|Australia|Angielski|EN-AU|
|Austria|Niemiecki|de-AT|
|Belgia|Holenderski|nl-BE|
|Belgia|Francuski|fr-BE|
|Brazylia|Portugalski|pt-BR|
|Kanada|Angielski|EN-CA|
|Kanada|Francuski|fr-CA|
|Chile|Hiszpański|ES-CL|
|Dania|Duński|Akcelerator deweloperski w wersji DK|
|Finlandia|Fiński|fi-FI|
|Francja|Francuski|fr-FR|
|Niemcy|Niemiecki|de-DE.|
|SRA Hongkong|Chiński (tradycyjny)|zh-HK|
|Indie|Angielski|EN-IN|
|Indonezja|Angielski|EN-ID|
|Włochy|Włoski|IT-IT|
|Japonia|Japoński|ja-JP|
|Korea Południowa|Koreański|ko-KR|
|Malezja|Angielski|Moje en|
|Meksyk|Hiszpański|es-MX|
|Holandia|Holenderski|NL-NL|
|Nowa Zelandia|Angielski|EN NZ|
|Norwegia|Norweski|no-NO|
|Chiny|Chiński|zh-CN|
|Polska|Polski|pl-PL|
|Portugalia|Portugalski|pt-PT|
|Filipiny|Angielski|en-PH|
|Rosja|Rosyjski|ru-RU|
|Arabia Saudyjska|Arabski|ar-SA|
|RPA|Angielski|en-ZA|
|Hiszpania|Hiszpański|es-ES|
|Szwecja|Szwedzki|sv-SE|
|Szwajcaria|Francuski|FR-CH|
|Szwajcaria|Niemiecki|de-CH|
|Tajwan|Chiński (tradycyjny)|zh-TW|
|Turcja|Turecki|tr-TR|
|Zjednoczone Królestwo|Angielski|en-GB|
|Stany Zjednoczone Ameryki|Angielski|en-US|
|Stany Zjednoczone Ameryki|Hiszpański|es-US|

## <a name="next-steps"></a>Kolejne kroki

* [Dokumentacja interfejsu API wyszukiwania obrazów Bing](//docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference)
