---
title: Obsługa języków — interfejs API wyszukiwania niestandardowego Bing
titleSuffix: Azure Cognitive Services
description: Lista obsługiwanych językach i regionach interfejsu API wyszukiwania niestandardowego Bing.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: conceptual
ms.date: 09/25/2018
ms.author: aahi
ms.openlocfilehash: 56870a63f42c10b48cc2d8f0ae2995862be46d8f
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2019
ms.locfileid: "65790239"
---
# <a name="language-and-region-support-for-the-bing-custom-search-api"></a>Obsługa języka i regionu dla interfejsu API wyszukiwania niestandardowego Bing

Interfejs API wyszukiwania niestandardowego Bing obsługuje więcej niż trzech tuzina kraje/regiony, wiele z więcej niż jednym języku.

Chociaż jest to opcjonalne, należy określić żądanie [mkt](https://docs.microsoft.com/rest/api/cognitiveservices/bing-custom-search-api-v7-reference#mkt) parametr zapytania, który identyfikuje na rynku, którego wyniki pochodzą. Aby uzyskać listę parametrów opcjonalnych zapytania, zobacz [parametry zapytania](https://docs.microsoft.com/rest/api/cognitiveservices/bing-custom-search-api-v7-reference#query-parameters)

Można określić kraj/region, w którym używana jest `cc` parametr zapytania. Jeśli określisz kraju/regionu, należy także określić co najmniej jeden kod języka przy użyciu `Accept-Language` nagłówka. Obsługiwane języki zależą od kraju/regionu podane są dla każdego kraju/regionu w **rynków** tabeli.

`Accept-Language` Nagłówka i `setLang` parametr zapytania wykluczają się wzajemnie — nie określać jednocześnie. Aby uzyskać więcej informacji, zobacz [Accept-Language](https://docs.microsoft.com/rest/api/cognitiveservices/bing-custom-search-api-v7-reference#acceptlanguage).

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
