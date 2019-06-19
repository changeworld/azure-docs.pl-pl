---
title: Obsługa języków — interfejs API analizy tekstu
titleSuffix: Azure Cognitive Services
description: 'Lista języków naturalnych obsługiwanych przez interfejs API analizy tekstu. W tym artykule opisano, które języki są obsługiwane dla każdej operacji: analiza tonacji, wyodrębnianie kluczowych fraz, wykrywanie języka i rozpoznawanie jednostek.'
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 06/18/2019
ms.author: aahi
ms.openlocfilehash: 704a1193eb47f9346900c6c8a003122c30c8ab44
ms.sourcegitcommit: b7a44709a0f82974578126f25abee27399f0887f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/18/2019
ms.locfileid: "67203972"
---
# <a name="language-and-region-support-for-the-text-analytics-api"></a>Obsługa języka i regionu dla interfejsu API analizy tekstu

W tym artykule opisano, które języki są obsługiwane dla każdej operacji: analiza tonacji, wyodrębnianie kluczowych fraz, wykrywanie języka i rozpoznawanie jednostek znaku.

## <a name="language-detection"></a>Wykrywanie języka

Interfejs API analizy tekstu można wykrywać szerokiej gamy języków, warianty, dialekty i w niektórych językach regionalne/kultury.  Wykrywanie języka zwraca "skrypt" język. Na przykład frazy "Mam dog" zwróci `en` zamiast `en-US`. Tylko w szczególnych przypadkach jest chińskich, których będzie zwracać funkcja wykrywania języka `zh_CHS` lub `zh_CHT` Jeśli można określić, skrypt danego tekstu, pod warunkiem. W sytuacjach, gdzie określonego skryptu nie można zidentyfikować dla dokumentu chińskim, to zostanie zwrócona po prostu `zh`.

Nie publikujemy dokładna lista języków dla tej funkcji, ale może wykryć, szerokiej gamy języków, warianty, dialekty i w niektórych językach regionalne/kultury. 

Jeśli masz zawartość wyrażona w języku rzadziej używane, możesz spróbować wykrywanie języka, aby zobaczyć, jeśli zwracany jest kod. Odpowiedź dla języków, których nie można wykryć jest `unknown`.

## <a name="sentiment-analysis-key-phrase-extraction-and-named-entity-recognition"></a>Analiza tonacji, wyodrębnianie kluczowych fraz i rozpoznawanie jednostek znaku

Analiza tonacji, wyodrębnianie kluczowych fraz i rozpoznawanie jednostek listę obsługiwanych języków to tylko niektóre analizatorów jest dostosowany do uwzględnienia językowej reguły dodatkowe języki. Obsługa pełnego zestawu [typów jednostek](how-tos/text-analytics-how-to-entity-linking.md#supported-types-for-named-entity-recognition) wynosi obecnie następujące języki: 
* Polski
* Chiński uproszczony
* Francuski
* Niemiecki
* Hiszpański

Tylko `Person`, `Location` i `Organization` nazwane jednostek są zwracane w przypadku pozostałych języków.

## <a name="language-list-and-status"></a>Lista języków i stan

Obsługa języków początkowo jest udostępniona w wersji zapoznawczej, tłumaczenie jest ogólnie dostępna (GA) stanu, niezależnie od siebie i usługi analizy tekstu, ogólne. Istnieje możliwość dla języków pozostaje w wersji zapoznawczej, nawet w trakcie przejść interfejsu API analizy tekstu jest ogólnie dostępna.

| Język    | Kod języka | Opinia | Kluczowe frazy | Rozpoznawanie jednostek nazwanych |   Uwagi  |
|:----------- |:-------------:|:---------:|:-----------:|:-----------:|:-----------:
| Arabski      | `ar`          |           |             | ✔ \*                     | |
| Czeski       | `cs`          |           |             | ✔ \*                     | |
| Chiński uproszczony | `zh-CN`|           |             | ✔ \*        |    |
| Duński      | `da`          | ✔ \*     | ✔           | ✔ \*            |     |
| Holenderski       | `nl`          | ✔ \*     | ✔          |  ✔ \*           |     |
| Polski     | `en`          | ✔        | ✔           |  ✔ \*\*     |      |
| Fiński     | `fi`          | ✔ \*     | ✔           |  ✔ \*           |     |
| Francuski      | `fr`          | ✔        | ✔           |  ✔ \*           |     |
| Niemiecki      | `de`          | ✔ \*     | ✔           |  ✔ \*          |     |
| Grecki       | `el`          | ✔ \*     |             |            |     |
| Węgierski   | `hu`          |           |             |  ✔ \*          |     | 
| Włoski     | `it`          | ✔ \*     | ✔           |  ✔ \*           |     |
| Japoński    | `ja`          |          | ✔           |  ✔ \*          |     |
| Koreański      | `ko`          |          | ✔           |  ✔ \*          |     |
| Norwegian  (Bokmål) | `no`  | ✔ \*     |  ✔          | ✔ \*            |     |
| Polski      | `pl`          | ✔ \*     |  ✔          |  ✔ \*           |     |
| Portugalski (Portugalia) | `pt-PT`| ✔        |  ✔          | ✔ \*      |`pt` również zaakceptowana|
| Portugalski (Brazylia)   | `pt-BR`|          |  ✔   |  ✔ \*       |     |
| Rosyjski     | `ru`          | ✔ \*     | ✔           |  ✔ \*           |     |
| Hiszpański     | `es`          | ✔        |            |   ✔ \*\*      |     | 
| Szwedzki     | `sv`          | ✔ \*     | ✔           |   ✔ \*          |     |
| Turecki     | `tr`          | ✔ \*     |             |   ✔ \*          |  |

\* Obsługa języka jest w wersji zapoznawczej

\*\* [O nazwie rozpoznawanie jednostek](how-tos/text-analytics-how-to-entity-linking.md#named-entity-recognition-ner) i [łączenie podmiotów](how-tos/text-analytics-how-to-entity-linking.md#entity-linking) są dostępne dla tego języka.    

## <a name="see-also"></a>Zobacz także

[Cognitive stronę dokumentacji usługi](https://docs.microsoft.com/azure/cognitive-services/)   
[Strona produktu usług Cognitive Services](https://azure.microsoft.com/services/cognitive-services/)
