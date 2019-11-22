---
title: Wstępnie utworzone modele — LUIS
titleSuffix: Azure Cognitive Services
description: Wstępnie utworzone modele zawierają domen, intencji, wypowiedzi i jednostek. Można Rozpocznij tworzenie aplikacji przy użyciu wbudowanych domeny lub później dodać domenę odpowiednie do Twojej aplikacji.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 10/10/2019
ms.author: diberry
ms.openlocfilehash: e0b5b95a7524b60a7c3367035a15a7158fa7908a
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/21/2019
ms.locfileid: "74280841"
---
# <a name="prebuilt-models"></a>Wstępnie utworzone modele

Wstępnie utworzone modele zawierają domen, intencji, wypowiedzi i jednostek. Możesz uruchomić aplikację przy użyciu wstępnie skompilowanego modelu lub później dodać odpowiedni model do aplikacji. 

## <a name="types-of-prebuilt-models"></a>Typy wstępnie utworzone modele

LUIS udostępnia trzy typy wstępnie skompilowanych modeli. Każdy model można dodać do aplikacji w dowolnym momencie. 

|Typ modelu|Zawiera|
|--|--|
|[Domeny](luis-reference-prebuilt-domains.md)|Intencji wypowiedzi, jednostki|
|Intencje|Intencji, wypowiedzi|
|[Obiekty](luis-reference-prebuilt-entities.md)|Tylko jednostki| 

## <a name="prebuilt-domains"></a>Wstępnie utworzone domeny

Language Understanding (LUIS) zapewnia *prebudowane domeny*, które są wstępnie szkolonymi modelami [intencji](luis-how-to-add-intents.md) i [jednostek](luis-concept-entity-types.md) , które współpracują ze sobą w przypadku domen lub wspólnych kategorii aplikacji klienckich. 

Ze wstępnie utworzonych domen są przeszkolony i chcesz dodać do aplikacji usługi LUIS. Intencje i jednostki prekompilowanej domeny są w pełni dostosowywane po dodaniu ich do aplikacji. 

> [!TIP]
> Intencje i podmioty, w domenie wbudowanych najlepiej działają razem. Zaleca się połączyć intencje i podmioty w tej samej domenie, jeśli jest to możliwe.
> Domena wstępnie utworzone narzędzia ma intencji, które można dostosować do użytku w każdej domenie. Na przykład można dodać `Utilities.Repeat` do swojej aplikacji i szkolenie go rozpoznać, niezależnie od działań użytkownika może być ma zostać powtórzony w aplikacji. 

### <a name="changing-the-behavior-of-a-prebuilt-domain-intent"></a>Zmiana zachowania intencji ze wstępnie utworzonych domen

Może się okazać, że wbudowanych domena zawiera cel, który jest podobny do intencji, które chcesz mieć w swojej aplikacji usługi LUIS, ale chcesz, aby zachowywać się inaczej. **Na przykład** prebudowana domena zawiera `MakeReservation` zamiarem do tworzenia rezerwacji w restauracji, ale chcesz, aby Twoja aplikacja korzystała z tego celu do tworzenia rezerwacji hotelowych. W takim przypadku można zmodyfikować zachowanie tego celu przez dodanie przykładu wyrażenia długości do zamiaru dotyczącego tworzenia rezerwacji hotelowych, a następnie ponownego uczenia aplikacji. 

Znajduje się pełna lista ze wstępnie utworzonych domen w [odwołania ze wstępnie utworzonych domen](./luis-reference-prebuilt-domains.md).

## <a name="prebuilt-intents"></a>Wstępnie utworzone intencji

LUIS zapewnia prebudowane intencje i ich wyrażenia długości dla każdej z wstępnie skompilowanych domen. Można dodać intencji bez dodawania całą domenę. Dodanie zamiaru to proces dodawania zamiaru i jego wyrażenia długości do aplikacji. Można modyfikować zarówno nazwę metody konwersji, jak i na liście wypowiedź.  

## <a name="prebuilt-entities"></a>Wstępnie utworzone jednostki

Usługa LUIS zawiera zestaw wstępnie utworzonych jednostek rozpoznawania typowe rodzaje informacji, takich jak daty, godziny, numery, pomiarów i waluty. Obsługa wstępnie utworzone jednostki jest zależna od kultury aplikacją usługi LUIS. Aby uzyskać pełną listę wstępnie utworzone jednostki, które obsługuje usługi LUIS, w tym pomoc od kultury, zobacz [odwołania do wstępnie utworzone jednostki](./luis-reference-prebuilt-entities.md).

Gdy wstępnie utworzone jednostki znajduje się w aplikacji, jej prognozy są uwzględnione w opublikowanej aplikacji. Działanie wstępnie utworzonych jednostek jest wstępnie przeszkolonych i **nie** można modyfikować. 

> [!NOTE]
> **BUILTIN.DateTime** jest przestarzała. Zastępuje się wyrazami [ **builtin.datetimeV2**](luis-reference-prebuilt-datetimev2.md), która umożliwia rozpoznawanie daty i zakresy czasu, a także Rozpoznawanie niejednoznacznych daty i godziny.

## <a name="next-steps"></a>Następne kroki

Dowiedz się, jak [Dodaj ze wstępnie utworzonych jednostek](luis-prebuilt-entities.md) do swojej aplikacji.
