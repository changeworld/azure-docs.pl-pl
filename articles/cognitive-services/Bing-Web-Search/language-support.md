---
title: Obsługa języków - API wyszukiwania w Internecie Bing
titleSuffix: Azure Cognitive Services
description: Lista języków naturalnych, krajów i regionów, które są obsługiwane przez interfejs API wyszukiwania wiadomości Bing.
services: cognitive-services
author: aahill
manager: cgronlun
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: article
ms.date: 09/25/2018
ms.author: aahi
ms.openlocfilehash: dbe9fe5b8e9cd31ca20d88b7db89a9d35e1fe8de
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/29/2019
ms.locfileid: "55161142"
---
# <a name="language-and-region-support-for-the-bing-web-search-api"></a>Obsługa języka i regionu API wyszukiwania w Internecie Bing

API wyszukiwania w Internecie Bing obsługuje ponad tuzina trzech krajów lub regionów, z których wiele z więcej niż jednym języku. Określanie kraj lub region z zapytaniem pomaga zawęzić wyniki wyszukiwania oparte na tym zainteresowania krajów lub regionów. Wyniki mogą być dostępne łącza do usługi Bing, a te linki mogą lokalizować Bing środowiska użytkownika zgodnie z określonego kraju/regionu lub języka.

Można określić kraj lub region przy użyciu `cc` parametr zapytania. Kraj lub region jest określony, należy określić co najmniej jeden kod języka z [ `Accept-Language` nagłówka](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#headers). Użyj [tabeli rynków](#Markets) listę języków obsługiwanych w poszczególnych segmentach rynku.

Alternatywnie, można określić na rynek `mkt` parametr zapytania i kod z **rynków** tabeli. Określanie na rynek, jednocześnie Określa kraj lub region i język preferowany. Można jawnie ustawić język za pomocą `setLang` parametr zapytania.

## <a name="countriesregions"></a>Kraje/regiony

|Kraj/region|Kod|
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

## <a name="markets"></a>Rynki

|Kraj/region|Język|Rynek kodu|
|-------|--------|-----------|
|Argentyna|Hiszpański|ES AR|
|Australia|Polski|EN-AU|
|Austria|Niemiecki|de-AT|
|Belgia|Holenderski|nl-BE|
|Belgia|Francuski|fr-BE|
|Brazylia|Portugalski|pt-BR|
|Kanada|Polski|EN-CA|
|Kanada|Francuski|fr-CA|
|Chile|Hiszpański|ES-CL|
|Dania|Duński|Akcelerator deweloperski w wersji DK|
|Finlandia|Fiński|fi-FI|
|Francja|Francuski|fr-FR|
|Niemcy|Niemiecki|de-DE.|
|SRA Hongkong|Chiński (tradycyjny)|zh-HK|
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
|Filipiny|Polski|en-PH|
|Rosja|Rosyjski|ru-RU|
|Arabia Saudyjska|Arabski|ar-SA|
|Republika Południowej Afryki|Polski|en-ZA|
|Hiszpania|Hiszpański|es-ES|
|Szwecja|Szwedzki|sv-SE|
|Szwajcaria|Francuski|FR-CH|
|Szwajcaria|Niemiecki|de-CH|
|Tajwan|Chiński (tradycyjny)|zh-TW|
|Turcja|Turecki|tr-TR|
|Wielka Brytania|Polski|en-GB|
|Stany Zjednoczone|Polski|en-US|
|Stany Zjednoczone|Hiszpański|es-US|
