---
title: Obsługa języka — interfejs API wyszukiwania w sieci Web usługi Bing
titleSuffix: Azure Cognitive Services
description: Lista języków naturalnych, krajów i regionów obsługiwanych przez interfejs API wyszukiwania wiadomości Bing.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: conceptual
ms.date: 05/15/2019
ms.author: aahi
ms.openlocfilehash: 9425de6e75a9a46d71ff85ce49b0650c8e7a9a16
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "68882689"
---
# <a name="language-and-region-support-for-the-bing-web-search-api"></a>Obsługa języka i regionu interfejsu API wyszukiwania w sieci Web usługi Bing

Interfejs API wyszukiwania w sieci Web usługi Bing obsługuje ponad trzy tuziny krajów lub regionów, wiele z więcej niż jednym językiem. Określenie kraju lub regionu za pomocą kwerendy pomaga uściślać wyniki wyszukiwania na podstawie tego kraju lub regionów zainteresowań. Wyniki mogą zawierać łącza do usługi Bing, a te łącza mogą zlokalizować środowisko użytkownika usługi Bing zgodnie z określonym krajem/regionem lub językiem.

Za pomocą parametru kwerendy `cc` można określić kraj lub region. Po określeniu kraju lub regionu należy określić jeden lub więcej kodów języków za pomocą [ `Accept-Language` nagłówka](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#headers). Użyj [tabeli Rynki,](#markets) aby wyświetlić listę języków obsługiwanych na każdym rynku.

Alternatywnie można określić rynek `mkt` za pomocą parametru zapytania i kodu z tabeli **Rynki.** Określenie rynku jednocześnie określa kraj lub region oraz preferowany język. Można jawnie ustawić język `setLang` z parametrem kwerendy.

## <a name="countriesregions"></a>Kraje/regiony

|Kraj/region|Code|
|-------|----|
|Argentyna|AR|
|Australia|AU|
|Austria|AT|
|Belgia|BE|
|Brazylia|BR|
|Kanada|CA|
|Chile|CL|
|Dania|DK|
|Finlandia|FI|
|Francja|PW|
|Niemcy|DE|
|SRA Hongkong|HK|
|Indie|IN|
|Indonezja|ID|
|Włochy|IT|
|Japonia|JP|
|Korea|KR|
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
|Republika Południowej Afryki|ZA|
|Hiszpania|ES|
|Szwecja|SE|
|Szwajcaria|CH|
|Tajwan|TW|
|Turcja|TR|
|Wielka Brytania|GB|
|Stany Zjednoczone|USA|

## <a name="markets"></a>Rynków

|Kraj/region|Język|Kodeks rynku|
|-------|--------|-----------|
|Argentyna|Hiszpański|es-AR|
|Australia|Polski|pl-UA|
|Austria|Niemiecki|de-AT|
|Belgia|Niderlandzki|nl-BE|
|Belgia|Francuski|fr-BE|
|Brazylia|Portugalski|pt-BR|
|Kanada|Polski|pl-CA|
|Kanada|Francuski|fr-CA|
|Chile|Hiszpański|es-CL|
|Dania|duński|da-DK|
|Finlandia|fiński|fi-FI|
|Francja|Francuski|fr-FR|
|Niemcy|Niemiecki|de-DE|
|SRA Hongkong|Chiński tradycyjny|zh-HK|
|Indie|Polski|pl-IN|
|Indonezja|Polski|en-ID|
|Włochy|Włoski|it-IT|
|Japonia|Japoński|ja-JP|
|Korea|Koreański|ko-KR|
|Malezja|Polski|pl-MY|
|Meksyk|Hiszpański|es-MX|
|Holandia|Niderlandzki|nl-NL|
|Nowa Zelandia|Polski|pl-NZ|
|Norwegia|Norweski|no-NO|
|Chiny|Chiński|zh-CN|
|Polska|Polski|pl-PL|
|Portugalia|Portugalski|pt-PT|
|Filipiny|Polski|pl-PH|
|Rosja|Rosyjski|ru-RU|
|Arabia Saudyjska|Arabski|ar-SA|
|Republika Południowej Afryki|Polski|pl-ZA|
|Hiszpania|Hiszpański|es-ES|
|Szwecja|szwedzki|sv-SE|
|Szwajcaria|Francuski|fr-CH|
|Szwajcaria|Niemiecki|de-CH|
|Tajwan|Chiński tradycyjny|zh-TW|
|Turcja|Turecki|tr-TR|
|Wielka Brytania|Polski|en-GB|
|Stany Zjednoczone|Polski|pl-PL|
|Stany Zjednoczone|Hiszpański|es-USA|

## <a name="next-steps"></a>Następne kroki

* [Dokumentacja interfejsu API wyszukiwania obrazów Bing](//docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference)
