---
title: Obsługa języka — interfejs API wyszukiwania w sieci Web Bing
titleSuffix: Azure Cognitive Services
description: Lista języków naturalnych, krajów i regionów, które są obsługiwane przez interfejs API wyszukiwania wiadomości Bing.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: conceptual
ms.date: 05/15/2019
ms.author: aahi
ms.openlocfilehash: 9425de6e75a9a46d71ff85ce49b0650c8e7a9a16
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/09/2019
ms.locfileid: "68882689"
---
# <a name="language-and-region-support-for-the-bing-web-search-api"></a>Obsługa języka i regionu dla interfejs API wyszukiwania w sieci Web Bing

Interfejs API wyszukiwania w sieci Web Bing obsługuje ponad trzy dziesiątki krajów lub regionów, wiele z więcej niż jednym językiem. Określenie kraju lub regionu z zapytaniem ułatwia dokładne wyniki wyszukiwania w zależności od danego kraju lub regionu zainteresowania. Wyniki mogą zawierać linki do usługi Bing, a te linki mogą lokalizować środowisko użytkownika Bing w zależności od określonego kraju/regionu lub języka.

Możesz określić kraj lub region przy użyciu `cc` parametru zapytania. W przypadku określenia kraju lub regionu należy określić jeden lub więcej kodów języka z [ `Accept-Language` nagłówkiem](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#headers). Skorzystaj z [tabeli rynków](#markets) , aby zapoznać się z listą języków obsługiwanych na poszczególnych rynkach.

Alternatywnie można określić rynek z `mkt` parametrem zapytania i kodem z tabeli **rynków** . Określenie rynku jednocześnie określa kraj lub region i preferowany język. Można jawnie ustawić język przy użyciu `setLang` parametru zapytania.

## <a name="countriesregions"></a>Kraje/regiony

|Country/region|Kod|
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
|Indonezja|id|
|Włochy|it|
|Japonia|JP|
|Korea Południowa|KR|
|Malezja|MY|
|Meksyk|MX|
|Holandia|NL|
|Nowa Zelandia|NZ|
|Norwegia|NO|
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
|Stany Zjednoczone|USA|

## <a name="markets"></a>Wprowadza

|Country/region|Język|Kod rynkowy|
|-------|--------|-----------|
|Argentyna|Hiszpański|ES-AR|
|Australia|Angielski|EN-AU|
|Austria|niemiecki|de-AT|
|Belgia|Holenderski|nl-BE|
|Belgia|Francuski|fr-BE|
|Brazylia|Portugalski|pt-BR|
|Kanada|Angielski|EN-CA|
|Kanada|Francuski|fr-CA|
|Chile|Hiszpański|ES — CL|
|Dania|Duński|Akcelerator deweloperski w wersji DK|
|Finlandia|Fiński|fi-FI|
|Francja|Francuski|fr-FR|
|Niemcy|niemiecki|de-DE.|
|SRA Hongkong|Chiński tradycyjny|zh-HK|
|Indie|Angielski|EN-IN|
|Indonezja|Angielski|pl-ID|
|Włochy|Włoski|IT-IT|
|Japonia|Japoński|ja-JP|
|Korea Południowa|Koreański|ko-KR|
|Malezja|Angielski|pl — MY|
|Meksyk|Hiszpański|es-MX|
|Holandia|Holenderski|NL-NL|
|Nowa Zelandia|Angielski|EN NZ|
|Norwegia|Norweski|nie — nie|
|Chiny|Chiński|zh-CN|
|Polska|Polski|pl-PL|
|Portugalia|Portugalski|pt-PT|
|Filipiny|Angielski|EN-PH|
|Rosja|Rosyjski|ru-RU|
|Arabia Saudyjska|Arabski|ar-SA|
|RPA|Angielski|pl-za|
|Hiszpania|Hiszpański|es-ES|
|Szwecja|Szwedzki|sv-SE|
|Szwajcaria|Francuski|FR-CH|
|Szwajcaria|niemiecki|de-CH|
|Tajwan|Chiński tradycyjny|zh-TW|
|Turcja|Turecki|tr-TR|
|Zjednoczone Królestwo|Angielski|en-GB|
|Stany Zjednoczone|Angielski|en-US|
|Stany Zjednoczone|Hiszpański|es-US|

## <a name="next-steps"></a>Następne kroki

* [Dokumentacja interfejsu API wyszukiwania obrazów Bing](//docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference)
