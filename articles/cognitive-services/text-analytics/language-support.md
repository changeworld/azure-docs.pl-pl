---
title: Obsługa języków — interfejs API analizy tekstu
titleSuffix: Azure Cognitive Services
description: 'Lista języków naturalnych obsługiwanych przez interfejs API analizy tekstu. W tym artykule opisano, które języki są obsługiwane dla każdej operacji: analiza tonacji, wyodrębnianie kluczowych fraz, wykrywanie języka i rozpoznawanie jednostek.'
services: cognitive-services
author: ashmaka
manager: cgronlun
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 10/01/2018
ms.author: ashmaka
ms.openlocfilehash: ec1858256c942fd50818a214de426fd00c3c2c88
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/29/2019
ms.locfileid: "55216935"
---
# <a name="language-and-region-support-for-the-text-analytics-api"></a>Obsługa języka i regionu dla interfejsu API analizy tekstu

W tym artykule opisano, które języki są obsługiwane dla każdej operacji: analiza tonacji, wyodrębnianie kluczowych fraz oraz wykrywanie języka.

## <a name="language-detection"></a>Wykrywanie języka

Interfejs API analizy tekstu można wykrywać maksymalnie 120 języków. Wykrywanie języka zwraca "skrypt" język. Na przykład frazy "Mam dog" zwróci `en` zamiast `en-US`. Tylko w szczególnych przypadkach jest chińskich, których będzie zwracać funkcja wykrywania języka `zh_CHS` lub `zh_CHT` Jeśli można określić, skrypt danego tekstu, pod warunkiem. W sytuacjach, gdzie określonego skryptu nie można zidentyfikować dla dokumentu chińskim, to zostanie zwrócona po prostu `zh`.

## <a name="sentiment-analysis-key-phrase-extraction-and-entity-recognition"></a>Analiza tonacji, wyodrębnianie kluczowych fraz i rozpoznawanie jednostek

Analiza tonacji, wyodrębnianie kluczowych fraz i rozpoznawanie jednostek listę obsługiwanych języków to tylko niektóre analizatorów jest dostosowany do uwzględnienia językowej reguły dodatkowe języki.

## <a name="language-list-and-status"></a>Lista języków i stan

Obsługa języków początkowo jest udostępniona w wersji zapoznawczej, tłumaczenie jest ogólnie dostępna (GA) stanu, niezależnie od siebie i usługi analizy tekstu, ogólne. Istnieje możliwość dla języków pozostaje w wersji zapoznawczej, nawet w trakcie przejść interfejsu API analizy tekstu jest ogólnie dostępna.

| Język    | Kod języka | Opinia | Kluczowe frazy | Rozpoznawanie jednostek |   Uwagi  |
|:----------- |:-------------:|:---------:|:-----------:|:-----------:|:-----------:
| Duński      | `da`          | ✔ \*     | ✔           |             |     |
| Holenderski       | `nl`          | ✔ \*     | ✔          |             |     |
| Polski     | `en`          | ✔        | ✔           |  ✔ \*   |      |
| Fiński     | `fi`          | ✔ \*     | ✔           |             |     |
| Francuski      | `fr`          | ✔        | ✔           |             |     |
| Niemiecki      | `de`          | ✔ \*     | ✔           |            |     |
| Grecki       | `el`          | ✔ \*     |             |            |     |
| Włoski     | `it`          | ✔ \*     | ✔           |             |     |
| Japoński    | `ja`          |          | ✔           |            |     |
| Koreański      | `ko`          |          | ✔           |            |     |
| Norwegian  (Bokmål) | `no`          | ✔ \*     |  ✔          |             |     |
| Polski      | `pl`          | ✔ \*     |  ✔          |             |     |
| Portugalski (Portugalia) | `pt-PT`| ✔        |  ✔          |       |`pt` również zaakceptowana|
| Portugalski (Brazylia)   | `pt-BR`|          |  ✔   |         |     |
| Rosyjski     | `ru`          | ✔ \*     | ✔           |             |     |
| Hiszpański     | `es`          | ✔        | ✔           |   ✔ \*\*      |     |
| Szwedzki     | `sv`          | ✔ \*     | ✔           |             |     |
| Turecki     | `tr`          | ✔ \*     |             |             |  |

\* Wskazuje obsługę języka w wersji zapoznawczej

\*\* Wyodrębnianie jednostek dla języka hiszpańskiego jest dostępna tylko w [(w wersji 2.1 — wersja zapoznawcza)](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-V2-1-Preview/operations/5ac4251d5b4ccd1554da7634)

## <a name="see-also"></a>Zobacz także

[Cognitive stronę dokumentacji usługi](https://docs.microsoft.com/azure/cognitive-services/)   
[Strona produktu usług Cognitive Services](https://azure.microsoft.com/services/cognitive-services/)
