---
title: Obsługiwane języki w interfejs API analizy tekstu
titleSuffix: Azure Cognitive Services
description: Lista jest ogólnie dostępna i języka w wersji zapoznawczej obsługi dla operacji interfejsu API analizy tekstu. Ma zastosowanie do analizy tonacji, wyodrębnianie kluczowych fraz oraz wykrywanie języka.
services: cognitive-services
author: ashmaka
manager: cgronlun
ms.service: cognitive-services
ms.technology: text-analytics
ms.topic: conceptual
ms.date: 09/12/2018
ms.author: ashmaka
ms.openlocfilehash: 984947903a91837910493618bd7abc2cdfe0da71
ms.sourcegitcommit: 616e63d6258f036a2863acd96b73770e35ff54f8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/14/2018
ms.locfileid: "45603247"
---
# <a name="supported-languages-in-the-text-analytics-cognitive-service"></a>Obsługiwane języki w usłudze Cognitive analizy tekstu

W tym artykule opisano, które języki są obsługiwane dla każdej operacji: analiza tonacji, wyodrębnianie kluczowych fraz oraz wykrywanie języka.

## <a name="language-detection"></a>Wykrywanie języka

Interfejs API analizy tekstu można wykrywać maksymalnie 120 języków. Wykrywanie języka zwraca "skrypt" język. Na przykład frazy "Mam dog" zwróci `en` zamiast `en-US`. Tylko w szczególnych przypadkach jest chińskich, których będzie zwracać funkcja wykrywania języka `zh_CHS` lub `zh_CHT` Jeśli można określić, skrypt danego tekstu, pod warunkiem. W sytuacjach, gdzie określonego skryptu nie można zidentyfikować dla dokumentu chińskim, to zostanie zwrócona po prostu `zh`.

## <a name="sentiment-analysis-key-phrase-extraction-and-entity-linking"></a>Analiza tonacji, wyodrębnianie kluczowych fraz i łączenie jednostek

Analiza tonacji, wyodrębnianie kluczowych fraz i łączenie podmiotów, aby uzyskać listę obsługiwanych języków jest bardziej selektywnego analizatorów jest dostosowany do uwzględnienia językowej reguły dodatkowe języki.

## <a name="language-list-and-status"></a>Lista języków i stan

Obsługa języków początkowo jest udostępniona w wersji zapoznawczej, tłumaczenie jest ogólnie dostępna (GA) stanu, niezależnie od siebie i usługi analizy tekstu, ogólne. Istnieje możliwość dla języków pozostaje w wersji zapoznawczej, nawet w trakcie przejść interfejsu API analizy tekstu jest ogólnie dostępna.

| Język    | Kod języka | Opinia | Kluczowe frazy | Łączenie jednostek |   Uwagi  |
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
| Norweski (Bokmal) | `no`          | ✔ \*     |  ✔          |             |     |
| Polski      | `pl`          | ✔ \*     |  ✔          |             |     |
| Portugalski (Portugalia) | `pt-PT`| ✔        |  ✔          |       |`pt` również zaakceptowana|
| Portugalski (Brazylia)   | `pt-BR`|          |  ✔   |         |     |
| Rosyjski     | `ru`          | ✔ \*     | ✔           |             |     |
| Hiszpański     | `es`          | ✔        | ✔           |     |     |
| Szwedzki     | `sv`          | ✔ \*     | ✔           |             |     |
| Turecki     | `tr`          | ✔ \*     |             |             |     |

\* Wskazuje obsługę języka w wersji zapoznawczej

## <a name="see-also"></a>Zobacz także

[Cognitive stronę dokumentacji usługi](https://docs.microsoft.com/azure/cognitive-services/)   
[Strona produktu usług Cognitive Services](https://azure.microsoft.com/services/cognitive-services/)
