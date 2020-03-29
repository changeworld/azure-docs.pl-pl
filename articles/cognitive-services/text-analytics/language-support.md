---
title: Obsługa języków — interfejs API analizy tekstu
titleSuffix: Azure Cognitive Services
description: 'Lista języków naturalnych obsługiwanych przez interfejs API analizy tekstu. W tym artykule wyjaśniono, które języki są obsługiwane dla każdej operacji: analiza tonacji, wyodrębnianie fraz kluczowych, wykrywanie języka i rozpoznawanie jednostek.'
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 12/18/2019
ms.author: aahi
ms.openlocfilehash: c5a413a4fe8d9ac9b7aac59ca78cedc6d5a7a313
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79219281"
---
# <a name="language-and-region-support-for-the-text-analytics-api"></a>Obsługa języka i regionu interfejsu API analizy tekstu

W tym artykule wyjaśniono, które języki są obsługiwane dla każdej operacji: analiza tonacji, wyodrębnianie fraz kluczowych, wykrywanie języka i rozpoznawanie nazwanych jednostek.

## <a name="language-detection"></a>Wykrywanie języka

Interfejs API analizy tekstu może wykrywać szeroką gamę języków, wariantów, dialektów i niektórych języków regionalnych/kulturowych.  Wykrywanie języka zwraca "skrypt" języka. Na przykład, dla wyrażenia "mam psa" `en` wróci zamiast `en-US`. Jedynym szczególnym przypadkiem jest chiński, gdzie funkcja `zh_CHS` `zh_CHT` wykrywania języka powróci lub jeśli może określić skrypt, biorąc pod uwagę dostarczony tekst. W sytuacjach, gdy nie można zidentyfikować konkretnego skryptu dla `zh`chińskiego dokumentu, zwróci on po prostu .

Nie publikujemy dokładnej listy języków tej funkcji, ale może wykryć szeroką gamę języków, wariantów, dialektów i niektórych języków regionalnych/kulturowych. 

Jeśli masz zawartość wyrażoną w rzadziej używanym języku, możesz spróbować wykrywania języka, aby sprawdzić, czy zwraca kod. Odpowiedź dla języków, których nie `unknown`można wykryć, to .

## <a name="sentiment-analysis-key-phrase-extraction-and-named-entity-recognition"></a>Analiza tonacji, wyodrębnianie fraz kluczowych i rozpoznawanie nazwanych encji

W przypadku analizy tonacji, wyodrębniania fraz kluczowych i rozpoznawania jednostek lista obsługiwanych języków jest bardziej selektywna, ponieważ analizatory są udoskonalane w celu dostosowania do reguł językowych dodatkowych języków. W wersji 2 nazwane rozpoznawanie [encji](how-tos/text-analytics-how-to-entity-linking.md#named-entity-recognition-versions-and-features) obsługa pełnego zestawu typów encji jest obecnie ograniczona do następujących języków: 
* Polski
* Chiński uproszczony
* Francuski
* Niemiecki
* Hiszpański

Tylko `Person`encje , `Location` i `Organization` nazwane są zwracane dla innych języków.

## <a name="language-list-and-status"></a>Lista języków i stan

Obsługa języka jest początkowo wdrażana w wersji zapoznawczej, uzyskując ogólny stan dostępności (GA), niezależnie od siebie i ogólnie usługi analizy tekstu. Języki mogą pozostać w wersji zapoznawczej, nawet jeśli interfejs API analizy tekstu przejdzie do ogólnie dostępnych.

> [!NOTE]
> Aby uzyskać szczegółową obsługę języka publicznego podglądu rozpoznawania nazwanych jednostek(NER) w wersji 3, zobacz [Nazwane typy encji](named-entity-types.md).

| Język              | Kod języka | Opinia | Kluczowe zwroty | Rozpoznawanie jednostek znaku | Łączenie jednostek |       Uwagi        |
|:----------------------|:-------------:|:---------:|:-----------:|:------------------------:|:--------------:|:------------------:|
| Arabski                |     `ar`      |           |             |           ✔\*           |                |                    |
| Czeski                 |     `cs`      |           |             |           ✔\*           |                |                    |
| Chiński uproszczony    |   `zh-hans`   |  ✔\*\*   |             |            ✔             |                | `zh`również akceptowane                   |
| Chiński tradycyjny   |   `zh-hant`   |  ✔\*\*   |             |                          |                |                    |
| duński                |     `da`      |   ✔\*    |      ✔      |           ✔\*           |                |                    |
| Niderlandzki                 |     `nl`      |   ✔\**   |      ✔      |           ✔\*           |                |                    |
| Polski               |     `en`      |   ✔\**   |      ✔      |          ✔\*\*          |     ✔\**      |                    |
| fiński               |     `fi`      |   ✔\*    |      ✔      |           ✔\*           |                |                    |
| Francuski                |     `fr`      |   ✔\**   |      ✔      |            ✔             |                |                    |
| Niemiecki                |     `de`      |   ✔\**   |      ✔      |            ✔             |                |                    |
| grecki                 |     `el`      |   ✔\*    |             |                          |                |                    |
| węgierski             |     `hu`      |           |             |           ✔\*           |                |                    |
| Włoski               |     `it`      |   ✔\**   |      ✔      |           ✔\*           |                |                    |
| Japoński              |     `ja`      |   ✔\**   |      ✔      |           ✔\*           |                |                    |
| Koreański                |     `ko`      |   ✔\*\*  |      ✔      |           ✔\*           |                |                    |
| Norweski (Bokmål)   |     `no`      |   ✔\*    |      ✔      |           ✔\*           |                | `nb`również akceptowane                   |
| Polski                |     `pl`      |   ✔\*    |      ✔      |           ✔\*           |                |                    |
| Portugalski (Portugalia) |    `pt-PT`    |   ✔\**    |      ✔      |           ✔\*           |                | `pt`również akceptowane |
| portugalski (Brazylia)   |    `pt-BR`    |           |      ✔      |           ✔\*           |                |                    |
| Rosyjski               |     `ru`      |   ✔\*    |      ✔      |           ✔\*           |                |                    |
| Hiszpański               |     `es`      |   ✔\**    |      ✔      |           ✔\*           |     ✔\**      |                    |
| szwedzki               |     `sv`      |   ✔\*    |      ✔      |           ✔\*           |                |                    |
| Turecki               |     `tr`      |   ✔\*    |             |           ✔\*           |                |                    |

\*Obsługa języka jest w wersji zapoznawczej

\** Dostępne również w [analizach tonacji w wersji 3](https://docs.microsoft.com/azure/cognitive-services/text-analytics/how-tos/text-analytics-how-to-sentiment-analysis#sentiment-analysis-versions-and-features) i/lub publicznej wersji zapoznawczej [rozpoznawania nazwanych jednostek w wersji 3.](how-tos/text-analytics-how-to-entity-linking.md#named-entity-recognition-versions-and-features)

## <a name="see-also"></a>Zobacz też

[Strona Dokumentacja usług Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/)   
[Strona produktu usług Cognitive Services](https://azure.microsoft.com/services/cognitive-services/)
