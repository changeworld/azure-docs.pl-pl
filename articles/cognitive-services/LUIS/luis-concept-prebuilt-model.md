---
title: Wstępnie utworzone modele — LUIS
titleSuffix: Azure Cognitive Services
description: Wbudowane modele zapewniają domeny, intencje, wyrażenia długości i jednostki. Możesz uruchomić aplikację z prezbudowaną domeną lub później dodać odpowiednią domenę do aplikacji.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 07/29/2019
ms.author: diberry
ms.openlocfilehash: b88801ded3dea7c7514ff117361feba3e95444ed
ms.sourcegitcommit: b4665f444dcafccd74415fb6cc3d3b65746a1a31
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/11/2019
ms.locfileid: "72264389"
---
# <a name="prebuilt-domain-intent-and-entity-models"></a>Wstępnie skompilowane domeny, intencje i modele jednostek

Wbudowane modele zapewniają domeny, intencje, wyrażenia długości i jednostki. Możesz uruchomić aplikację z prezbudowaną domeną lub później dodać odpowiednią domenę do aplikacji. 

## <a name="types-of-prebuilt-models"></a>Typy wstępnie skompilowanych modeli

Dostępne są 3 typy wstępnie skompilowanych modeli LUIS. Każdy model można dodać do aplikacji w dowolnym momencie. 

|Typ modelu|Zawiera|
|--|--|
|Domain|Intencje, wyrażenia długości, jednostki|
|Intencje|Intencje, wyrażenia długości|
|Jednostki|Tylko jednostki| 

## <a name="prebuilt-domains"></a>Wstępnie utworzone domeny

Language Understanding (LUIS) zapewnia *prebudowane domeny*, które są wstępnie skompilowanymi zestawami [zamiar](luis-how-to-add-intents.md) i [jednostek](luis-concept-entity-types.md) , które współpracują ze sobą w przypadku domen lub wspólnych kategorii aplikacji klienckich. 

Wstępnie skompilowane domeny są przeszkolone i gotowe do dodania do aplikacji LUIS. Intencje i jednostki we wstępnie skompilowanej domenie są w pełni dostosowywane po dodaniu ich do aplikacji. 

Jeśli zaczniesz Dostosowywanie całej wbudowanej domeny, Usuń intencje i jednostki, których aplikacja nie musi używać. Możesz również dodać do zestawu, które zapewnią, że wbudowana domena już zapewnia. Na przykład w przypadku korzystania z domeny prekompilowanej **zdarzeń** dla aplikacji zdarzeń sportowych można dodać jednostki dla zespołów sportowych. Po rozpoczęciu [udostępniania wyrażenia długości](luis-how-to-add-example-utterances.md) do Luis Uwzględnij warunki specyficzne dla aplikacji. LUIS uczy się rozpoznawać te i elementy, które prekonstruują wbudowane domeny i jednostki do potrzeb Twojej aplikacji. 

> [!TIP]
> Intencje i jednostki we wstępnie skompilowanej domenie działają najlepiej wspólnie. Lepszym rozwiązaniem jest połączenie intencji i jednostek z tej samej domeny, jeśli jest to możliwe.
> Prebudowana domena narzędzi jest zamiarem, który można dostosować do użycia w dowolnej domenie. Można na przykład dodać do aplikacji `Utilities.Repeat` i wyszkolić rozpoznawanie dowolnego działania, które użytkownik może chcieć powtórzyć w aplikacji. 

### <a name="changing-the-behavior-of-a-prebuilt-domain-intent"></a>Zmiana zachowania prekompilowanego zamiaru domeny

Może się okazać, że wbudowana domena zawiera zamiar, która jest podobna do zamiaru, który ma być używany w aplikacji LUIS, ale ma być zachowywać się inaczej. **Na przykład** prebudowana domena udostępnia zamiaru `MakeReservation` do tworzenia rezerwacji w restauracji, ale chcesz, aby Twoja aplikacja korzystała z tego celu do tworzenia rezerwacji hotelowych. W takim przypadku można zmodyfikować zachowanie tego zamiaru, dostarczając wyrażenia długości do LUIS o sposobie rezerwacji hotelowych i etykietowania ich przy użyciu zamiaru `MakeReservation`, dlatego LUIS można ponownie przeszkolić, aby rozpoznać zamiar `MakeReservation` w żądaniu rezerwacji hotelu.

Pełną listę wstępnie skompilowanych domen można znaleźć w [dokumentacji prekompilowanych domen](./luis-reference-prebuilt-domains.md).

## <a name="prebuilt-intents"></a>Prebudowane intencje

LUIS zapewnia prebudowane intencje i ich wyrażenia długości. Intencje mogą być dodawane bez dodawania całej domeny. Dodanie zamiaru to proces dodawania zamiaru i jego wyrażenia długości. Można modyfikować zarówno nazwę zamierzenia, jak i listę wypowiedź.  

## <a name="prebuilt-entities"></a>Wstępnie utworzone jednostki

LUIS zawiera zestaw wstępnie utworzonych jednostek do rozpoznawania wspólnych typów informacji, takich jak daty, godziny, liczby, pomiary i waluta. Wbudowana obsługa jednostek jest różna w zależności od kultury aplikacji LUIS. Aby uzyskać pełną listę wstępnie utworzonych jednostek, które obsługuje LUIS, w tym obsługę według kultury, zobacz [prekompilowane odwołanie do jednostki](./luis-reference-prebuilt-entities.md).

Gdy wbudowana jednostka jest dołączona do aplikacji, jej przewidywania są zawarte w opublikowanej aplikacji. Zachowanie wbudowanych jednostek jest wstępnie nauczone i **nie można** go modyfikować. 

> [!NOTE]
> **element wbudowany. DateTime** jest przestarzały. Jest on zastępowany przez element [**wbudowany. datetimeV2**](luis-reference-prebuilt-datetimev2.md), który zapewnia rozpoznawanie zakresów dat i godzin, a także ulepszone rozpoznawanie niejednoznacznych dat i godzin.

## <a name="next-steps"></a>Następne kroki

Dowiedz się, jak [dodać wstępnie utworzone jednostki](luis-prebuilt-entities.md) do aplikacji.
