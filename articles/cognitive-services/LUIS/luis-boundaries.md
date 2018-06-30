---
title: Język granice opis (LUIS) | Dokumentacja firmy Microsoft
titleSuffix: Azure
description: Ten artykuł zawiera znanych ograniczeń LUIS.
services: cognitive-services
author: v-geberr
manager: kamran.iqbal
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 05/07/2018
ms.author: v-geberr
ms.openlocfilehash: 52bda6a13422ce8f759c40bd454a6b15e92d7a5d
ms.sourcegitcommit: 5a7f13ac706264a45538f6baeb8cf8f30c662f8f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/29/2018
ms.locfileid: "37110300"
---
# <a name="luis-boundaries"></a>Granice LUIS
LUIS ma kilka obszarów granic. Pierwsza to [granic modelu](#model-boundaries), która kontroluje lokalizacji docelowych, jednostki i funkcje w LUIS. Drugi ma [limity przydziału](#key-limits) na podstawie typu klucza. Trzeci obszar granice jest [klawiatury kombinacja](#keyboard-controls) kontrolowania LUIS witryny sieci Web. Czwarty obszar jest [mapowania region world](luis-reference-regions.md) między LUIS tworzenia witryny sieci Web i LUIS [punktu końcowego](luis-glossary.md#endpoint) interfejsów API. 


## <a name="model-boundaries"></a>Granice modelu

|Obszar|Limit|
|--|:--|--|
| [Nazwa aplikacji][luis-get-started-create-app] | * Znak domyślny maksymalny |
| [Testowanie usługi partia zadań][batch-testing]| 10 zestawów danych, zniesławiających 1000 dla zestawu danych|
| **[Złożone](./luis-concept-entity-types.md)|100 z maksymalnie 10 elementów podrzędnych |
| Jawna lista | 50 według aplikacji|
| **[Hierarchiczna](./luis-concept-entity-types.md) |100 z maksymalnie 10 elementów podrzędnych |
| [Opcje][intents]|500 według aplikacji<br>[Na podstawie wysyłania](https://github.com/Microsoft/botbuilder-tools/tree/master/Dispatch) aplikacji ma odpowiednie źródła 500 wysyłania|
| [Lista obiektów](./luis-concept-entity-types.md) | Element nadrzędny: 50, podrzędne: 20 000 elementów. Nazwa kanoniczna jest * domyślna maksymalna liczba znaków. Synonimy ma ma ograniczenia długości. |
| [Wzorce](luis-concept-patterns.md)|500 wzorce dla aplikacji.<br>Maksymalna długość wzorca to 400 znaków.<br>3 encji Pattern.any na wzorzec<br>Maksymalnie 2 zagnieżdżonych teksty opcjonalne we wzorcu|
| [Pattern.any](./luis-concept-entity-types.md)|100 na aplikacji, 3 encji pattern.any na wzorzec |
| [Lista fraz][phrase-list]|10 frazę list, 5000 pozycji na liście|
| [Wbudowane jednostek](./Pre-builtEntities.md) | Brak limitu|
| [Wyrażenie regularne jednostek](./luis-concept-entity-types.md)|20 jednostek<br>Maksymalna liczba znaków to 500. na wzorzec wyrażenia regularnego jednostki|
| [Role](luis-concept-roles.md)|300 role na aplikację. 10 ról na jednostkę|
| **[Proste](./luis-concept-entity-types.md)| 100 jednostek|
| [utterance][utterances] | 500 znaków|
| [Zniesławiających][utterances] | 15 000, według aplikacji|
| [Nazwa wersji][luis-how-to-manage-versions] | 10 znaków ograniczone do alfanumeryczne oraz kropki (.) |

* Znak Domyślna maksymalna wynosi 50 znaków. 

** Łączna liczba jednostek proste, hierarchicznych i złożone nie może przekraczać 100. Całkowita liczba jednostek hierarchiczna, złożonego jednostek jednostki proste i hierarchiczna elementy podrzędne jednostki nie może przekraczać 330. 

## <a name="intent-and-entity-naming"></a>Opcje i nazewnictwa jednostki
W nazwach zamiar i jednostki nie należy używać następujących znaków:

|Znak|Name (Nazwa)|
|--|--|
|`{`|Lewego nawiasu klamrowego|
|`}`|Prawy nawias klamrowy|
|`[`|Nawias otwierający|
|`]`|Prawy nawias|
|`\`|Ukośnik odwrotny|

## <a name="key-limits"></a>Ograniczenia klucza
Tworzenia klucz ma inną limity dla tworzenia i punktu końcowego. Klucz punktu końcowego usługi LUIS jest prawidłowy tylko dla punktu końcowego zapytań.

|Klucz|Tworzenie|Endpoint|Przeznaczenie|
|--|--|--|--|
|Tworzenie początkowe|1 mln miesięcznie, 5/s|1 tysięcy miesięcznie, 5/s|Tworzenie aplikacji LUIS|
|[Subskrypcja] [ pricing] - F0 — warstwa bezpłatna |nieprawidłowy|10 tysięcy miesięcznie, 5/s|Wykonywanie zapytania LUIS punktu końcowego|
|[Subskrypcja] [ pricing] - S0 — warstwa podstawowa|nieprawidłowy|50/sekundę|Wykonywanie zapytania LUIS punktu końcowego|
|[Wskaźniki nastrojów klientów analizy integracji](publishapp.md#enable-sentiment-analysis)|nieprawidłowy|bez dodatkowych opłat|Dodawanie informacji wskaźniki nastrojów klientów, w tym wyodrębniania danych frazy klucza |
|Integracja mowy|nieprawidłowy|$5.50 USD/1 tysięcy żądania punktu końcowego|Konwertuj tekst utterance utterance rozmowy i zwracają wyniki LUIS|

## <a name="keyboard-controls"></a>Klawiatury

|Wejście klawiatury | Opis | 
|--|--|
|Formant + E|Przełącza między tokeny i jednostek na liście zniesławiających|

## <a name="website-sign-in-time-period"></a>Witryny sieci Web, zaloguj się w przedziale czasu

Dotyczy dostępu logowania **60 minut**. Po tym okresie wystąpi błąd. Musisz zalogować się ponownie.

[luis-get-started-create-app]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-get-started-create-app
[batch-testing]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-concept-test#batch-testing
[intents]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-concept-intent
[phrase-list]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-concept-feature
[utterances]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-concept-utterance
[luis-how-to-manage-versions]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-how-to-manage-versions
[pricing]: https://azure.microsoft.com/pricing/details/cognitive-services/language-understanding-intelligent-services/
<!-- TBD: fix this link -->
[speech-to-intent-pricing]: https://azure.microsoft.com/pricing/details/cognitive-services/language-understanding-intelligent-services/
