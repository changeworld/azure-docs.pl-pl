---
title: Obsługa języka — interfejs API analizy tekstu
titleSuffix: Azure Cognitive Services
description: 'Lista języków naturalnych obsługiwanych przez interfejs API analizy tekstu. W tym artykule wyjaśniono, które języki są obsługiwane dla każdej operacji: Analiza tonacji, wyodrębnianie kluczowych fraz, wykrywanie języka i rozpoznawanie jednostek.'
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 06/18/2019
ms.author: aahi
ms.openlocfilehash: e14cb56db6e45fe86ecd5e73766c47d29dd23fa1
ms.sourcegitcommit: 909ca340773b7b6db87d3fb60d1978136d2a96b0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/13/2019
ms.locfileid: "70984242"
---
# <a name="language-and-region-support-for-the-text-analytics-api"></a>Obsługa języka i regionu dla interfejs API analizy tekstu

W tym artykule wyjaśniono, które języki są obsługiwane dla każdej operacji: analizy tonacji, wyodrębniania kluczowych fraz, wykrywania języka i nazwanego rozpoznawania jednostek.

## <a name="language-detection"></a>Wykrywanie języka

Interfejs API analizy tekstu może wykryć szeroką gamę języków, wariantów, dialektów i niektórych języków regionalnych/kulturowych.  Wykrywanie języka zwraca "skrypt" języka. Na przykład dla frazy "mam Dog", będzie ona zwracała `en` się `en-US`zamiast. Jedynym szczególnym przypadkiem jest chiński, w którym funkcja wykrywania języka zwróci `zh_CHS` wartość `zh_CHT` lub jeśli może ustalić skrypt, w którym znajduje się tekst. W sytuacjach, gdy nie można zidentyfikować określonego skryptu dla dokumentu w języku chińskim, zostanie on `zh`zwrócony po prostu.

Nie publikujemy dokładnej listy języków dla tej funkcji, ale może wykryć szeroką gamę języków, wariantów, dialektów i niektórych języków regionalnych/kulturowych. 

Jeśli masz zawartość wyrażoną w rzadziej używanym języku, możesz spróbować wykrywanie języka, aby sprawdzić, czy zwróci kod. Odpowiedź dla języków, których nie można wykryć, `unknown`to.

## <a name="sentiment-analysis-key-phrase-extraction-and-named-entity-recognition"></a>Analiza tonacji, wyodrębnianie kluczowych fraz i nazwanego rozpoznawania jednostek

W przypadku analizy tonacji, wyodrębniania kluczowych fraz i rozpoznawania jednostek Lista obsługiwanych języków jest bardziej selektywna, ponieważ analizatory są udoskonalane w celu uwzględnienia reguł lingwistycznych dodatkowych języków. Obsługa pełnego zestawu [typów jednostek](how-tos/text-analytics-how-to-entity-linking.md#supported-types-for-named-entity-recognition) jest obecnie ograniczona do następujących języków: 
* Angielski
* Chiński (uproszczony)
* Francuski
* niemiecki
* Hiszpański

Tylko jednostki i`Organization`nazwane są zwracane dla innych języków. `Location` `Person`

## <a name="language-list-and-status"></a>Lista i stan języka

Obsługa języka jest początkowo wdrażana w wersji zapoznawczej, z stopniowaniem do ogólnie dostępnego stanu (GA), niezależnie od siebie i ogólnej usługi analiza tekstu. Istnieje możliwość, że języki pozostają w wersji zapoznawczej, nawet gdy interfejs API analizy tekstu przejścia są ogólnie dostępne.

| Język    | Kod języka | Opinia | Kluczowe frazy | Rozpoznawanie jednostek nazwanych |   Uwagi  |
|:----------- |:-------------:|:---------:|:-----------:|:-----------:|:-----------:
| Arabski      | `ar`          |           |             | ✔ \*                     | |
| Czeski       | `cs`          |           |             | ✔ \*                     | |
| Chiński (uproszczony) | `zh-CN`| ✔ \***     |             | ✔         |    |
| Duński      | `da`          | ✔ \*     | ✔           | ✔ \*            |     |
| Holenderski       | `nl`          | ✔ \*     | ✔          |  ✔ \*           |     |
| Angielski     | `en`          | ✔        | ✔           |  ✔ \*\*     |      |
| Fiński     | `fi`          | ✔ \*     | ✔           |  ✔ \*           |     |
| Francuski      | `fr`          | ✔        | ✔           |  ✔            |     |
| niemiecki      | `de`          | ✔ \*     | ✔           |  ✔           |     |
| Grecki       | `el`          | ✔ \*     |             |            |     |
| Węgierski   | `hu`          |           |             |  ✔ \*          |     | 
| Włoski     | `it`          | ✔ \*     | ✔           |  ✔ \*           |     |
| Japoński    | `ja`          | ✔ \***         | ✔           |  ✔ \*          |     |
| Koreański      | `ko`          |          | ✔           |  ✔ \*          |     |
| Norweski (bokmål) | `no`  | ✔ \*     |  ✔          | ✔ \*            |     |
| Polski      | `pl`          | ✔ \*     |  ✔          |  ✔ \*           |     |
| Portugalski (Portugalia) | `pt-PT`| ✔        |  ✔          | ✔ \*      |`pt`również zaakceptowane|
| Portugalski (Brazylia)   | `pt-BR`|          |  ✔   |  ✔ \*       |     |
| Rosyjski     | `ru`          | ✔ \*     | ✔           |  ✔ \*           |     |
| Hiszpański     | `es`          | ✔        | ✔           |   ✔ \*\*      |     | 
| Szwedzki     | `sv`          | ✔ \*     | ✔           |   ✔ \*          |     |
| Turecki     | `tr`          | ✔ \*     |             |   ✔ \*          |  |

\*Obsługa języka jest w wersji zapoznawczej

\*\*Dla tego języka są dostępne zarówno [rozpoznawanie jednostek nazwanych](how-tos/text-analytics-how-to-entity-linking.md#named-entity-recognition-ner) , jak i [Konsolidacja jednostek](how-tos/text-analytics-how-to-entity-linking.md#entity-linking) .  

\** * Dostępne w [publicznej wersji Zapoznawczej analiza tonacji v3](https://docs.microsoft.com/en-us/azure/cognitive-services/text-analytics/how-tos/text-analytics-how-to-sentiment-analysis#sentiment-analysis-v3-public-preview)

## <a name="see-also"></a>Zobacz także

[Strona dokumentacji Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/)   
[Strona produktu usług Cognitive Services](https://azure.microsoft.com/services/cognitive-services/)
