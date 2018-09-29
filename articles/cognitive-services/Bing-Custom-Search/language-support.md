---
title: Obsługa języków — interfejs API wyszukiwania niestandardowego Bing
titleSuffix: Azure Cognitive Services
description: Lista obsługiwanych językach i regionach interfejsu API wyszukiwania niestandardowego Bing.
services: cognitive-services
author: mikedodaro
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-custom-search
ms.topic: article
ms.date: 09/25/2018
ms.author: v-gedod
ms.openlocfilehash: 73aa67ec4939aca28a28e1733a39c8517c2c38c5
ms.sourcegitcommit: 7c4fd6fe267f79e760dc9aa8b432caa03d34615d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/28/2018
ms.locfileid: "47435560"
---
# <a name="language-and-region-support-for-the-bing-custom-search-api"></a>Obsługa języka i regionu dla interfejsu API wyszukiwania niestandardowego Bing

Interfejs API wyszukiwania niestandardowego Bing obsługuje więcej niż trzech tuzina kraje/regiony, wiele z więcej niż jednym języku.

Chociaż jest to opcjonalne, należy określić żądanie [mkt](https://docs.microsoft.com/rest/api/cognitiveservices/bing-custom-search-api-v7-reference#mkt) parametr zapytania, który identyfikuje na rynku, którego wyniki pochodzą. Aby uzyskać listę parametrów opcjonalnych zapytania, zobacz [parametry zapytania](https://docs.microsoft.com/rest/api/cognitiveservices/bing-custom-search-api-v7-reference#query-parameters)

Można określić kraj/region, w którym używana jest `cc` parametr zapytania. Jeśli określisz kraju/regionu, należy także określić co najmniej jeden kod języka przy użyciu `Accept-Language` nagłówka. Obsługiwane języki zależą od kraju/regionu podane są dla każdego kraju/regionu w **rynków** tabeli.

`Accept-Language` Nagłówka i `setLang` parametr zapytania wykluczają się wzajemnie — nie określać jednocześnie. Aby uzyskać więcej informacji, zobacz [Accept-Language](https://docs.microsoft.com/rest/api/cognitiveservices/bing-custom-search-api-v7-reference#acceptlanguage).

## <a name="countries"></a>Kraje

|Kraj/region|Kod|
|-------|----|
|Argentyna|AR|
|Australia|AU|
|Austria|AT|
|Belgia|MOŻNA|
|Brazylia|BRAZYLIA|
|Kanada|Urząd certyfikacji|
|Chile|CL|
|Dania|DK|
|Finlandia|FI|
|Francja|PW|
|Niemcy|DE|
|Hongkong|HK|
|Indie|INDIE|
|Indonezja|ID|
|Włochy|IT|
|Japonia|JP|
|Korea|KR|
|Malezja|MOJE|
|Meksyk|MX|
|Holandia|HOLANDIA|
|Nowa Zelandia|NZ|
|Norwegia|NIE|
|Chiny|CN|
|Polska|PL|
|Portugalia|(CZAS PACYFICZNY)|
|Filipiny|PH|
|Rosja|JEDNOSTKA ŻĄDANIA|
|Arabia Saudyjska|AMERYKA POŁUDNIOWA|
|Republika Południowej Afryki|ZA|
|Hiszpania|ES|
|Szwecja|SE|
|Szwajcaria|CH|
|Tajwan|TW|
|Turcja|TR|
|Wielka Brytania|GB|
|Stany Zjednoczone|USA|


## <a name="markets"></a>Rynki

|Kraj/region|Język|Rynek kodu|
|-------|--------|-----------|
|Argentyna|Hiszpański|ES AR|
|Australia|Polski|EN-AU|
|Austria|Niemiecki|de-AT|
|Belgia|Holenderski|Holandia — być|
|Belgia|Francuski|FR — być|
|Brazylia|Portugalski|pt-BR|
|Kanada|Polski|EN-CA|
|Kanada|Francuski|fr-CA|
|Chile|Hiszpański|ES-CL|
|Dania|Duński|Akcelerator deweloperski w wersji DK|
|Finlandia|Fiński|fi-FI|
|Francja|Francuski|fr-FR|
|Niemcy|Niemiecki|de-DE.|
|Hongkong|Chiński (tradycyjny)|zh-HK|
|Indie|Polski|EN-IN|
|Indonezja|Polski|EN-ID|
|Włochy|Włoski|IT-IT|
|Japonia|Japoński|ja-JP|
|Korea|Koreański|ko-KR|
|Malezja|Polski|Moje en|
|Meksyk|Hiszpański|es-MX|
|Holandia|Holenderski|NL-NL|
|Nowa Zelandia|Polski|EN NZ|
|Norwegia|Norweski|no-NO|
|Chiny|Chiński|zh-CN|
|Polska|Polski|pl-PL|
|Portugalia|Portugalski|pt-PT|
|Filipiny|Polski|EN PH|
|Rosja|Rosyjski|ru-RU|
|Arabia Saudyjska|Arabski|ar-SA|
|Republika Południowej Afryki|Polski|EN ZA|
|Hiszpania|Hiszpański|es-ES|
|Szwecja|Szwedzki|sv-SE|
|Szwajcaria|Francuski|FR-CH|
|Szwajcaria|Niemiecki|de-CH|
|Tajwan|Chiński (tradycyjny)|zh-TW|
|Turcja|Turecki|tr-TR|
|Wielka Brytania|Polski|en-GB|
|Stany Zjednoczone|Polski|pl-PL|
|Stany Zjednoczone|Hiszpański|es-US|
