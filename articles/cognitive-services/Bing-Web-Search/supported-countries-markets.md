---
title: Kraje, regiony i języki obsługiwane przez API wyszukiwania w Internecie Bing
titleSuffix: Azure Cognitive Services
description: Dowiedz się, które kraje/regiony i języki są obsługiwane przez API wyszukiwania w Internecie Bing.
services: cognitive-services
author: v-jerkin
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-web-search
ms.topic: article
ms.date: 8/16/2018
ms.author: erhopf
ms.openlocfilehash: b954814d1c26e8afd24e0bb13e74c493f0fd2c55
ms.sourcegitcommit: f10653b10c2ad745f446b54a31664b7d9f9253fe
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/18/2018
ms.locfileid: "46125123"
---
# <a name="countries-regions-and-languages-supported-by-the-bing-web-search-api"></a>Kraje, regiony i języki obsługiwane przez API wyszukiwania w Internecie Bing

API wyszukiwania w Internecie Bing obsługuje ponad tuzina trzech krajów lub regionów, z których wiele z więcej niż jednym języku. Określanie kraj lub region z zapytaniem pomaga zawęzić wyniki wyszukiwania oparte na tym zainteresowania krajów lub regionów. Wyniki mogą być dostępne łącza do usługi Bing, a te linki mogą lokalizować Bing środowiska użytkownika zgodnie z określonego kraju/regionu lub języka.

Można określić kraj lub region przy użyciu `cc` parametr zapytania. Kraj lub region jest określony, należy określić co najmniej jeden kod języka z [ `Accept-Language` nagłówka](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#headers). Użyj [tabeli rynków](#Markets) listę języków obsługiwanych w poszczególnych segmentach rynku.

Alternatywnie, można określić na rynek `mkt` parametr zapytania i kod z **rynków** tabeli. Określanie na rynek, jednocześnie Określa kraj lub region i język preferowany. Można jawnie ustawić język za pomocą `setLang` parametr zapytania.

## <a name="countriesregions"></a>Kraje/regiony

|Country/region|Kod|
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
|Zjednoczone Królestwo|GB|
|Stany Zjednoczone|USA|

## <a name="markets"></a>Rynki

|Country/region|Język|Rynek kodu|
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
|Zjednoczone Królestwo|Polski|en-GB|
|Stany Zjednoczone|Polski|pl-PL|
|Stany Zjednoczone|Hiszpański|es-US|
