---
title: Obsługa języka — interfejs API wyszukiwania niestandardowego usługi Bing
titleSuffix: Azure Cognitive Services
description: Lista obsługiwanych języków i regionów interfejsu API wyszukiwania niestandardowego Bing.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: conceptual
ms.date: 09/25/2018
ms.author: aahi
ms.openlocfilehash: 85326ae9166f7ea15ec2f45c01755b8f9ef03aff
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "66388559"
---
# <a name="language-and-region-support-for-the-bing-custom-search-api"></a>Obsługa języka i regionu interfejsu API wyszukiwania niestandardowego usługi Bing

Interfejs API wyszukiwania niestandardowego usługi Bing obsługuje ponad trzy tuziny krajów/regionów, wiele z więcej niż jednym językiem.

Mimo że jest to opcjonalne, żądanie należy określić [mkt](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-custom-search-api-v7-reference#mkt) query parametr, który identyfikuje rynku, na którym mają pochodzić wyniki. Aby uzyskać listę opcjonalnych parametrów kwerendy, zobacz [Parametry kwerendy](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-custom-search-api-v7-reference#query-parameters)

Można określić kraj/region `cc` przy użyciu parametru kwerendy. Jeśli określisz kraj/region, należy również określić jeden `Accept-Language` lub więcej kodów języków za pomocą nagłówka. Obsługiwane języki różnią się w zależności od kraju/regionu; dla każdego kraju/regionu w tabeli **Rynki.**

Nagłówek `Accept-Language` i `setLang` parametr zapytania wzajemnie się wykluczają — nie należy określać obu tych opcji. Aby uzyskać szczegółowe informacje, zobacz [Akceptowanie języka](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-custom-search-api-v7-reference#acceptlanguage).

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
|Hongkong, SAR|Chiński tradycyjny|zh-HK|
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
