---
title: Wstępnie utworzone modele
titleSuffix: Language Understanding - Azure Cognitive Services
description: Wstępnie utworzone modele zawierają domen, intencji, wypowiedzi i jednostek. Można Rozpocznij tworzenie aplikacji przy użyciu wbudowanych domeny lub później dodać domenę odpowiednie do Twojej aplikacji.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 01/24/2019
ms.author: diberry
ms.openlocfilehash: 5d2ea9d971eff22ddeed4122c9697ca3096697b0
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60813883"
---
# <a name="prebuilt-domain-intent-and-entity-models"></a>Wstępnie utworzone modele domenę, intencji i jednostki

Wstępnie utworzone modele zawierają domen, intencji, wypowiedzi i jednostek. Można Rozpocznij tworzenie aplikacji przy użyciu wbudowanych domeny lub później dodać domenę odpowiednie do Twojej aplikacji. 

## <a name="types-of-prebuilt-models"></a>Typy wstępnie utworzone modele

Istnieją 3 typy wstępnie utworzone modele, które zapewnia usługi LUIS. Każdy model można dodać do aplikacji w dowolnym momencie. 

|Typ modelu|Zawiera|
|--|--|
|Domain|Intencji wypowiedzi, jednostki|
|Intencje|Intencji, wypowiedzi|
|Jednostki|Tylko jednostki| 

## <a name="prebuilt-domains"></a>Ze wstępnie utworzonych domen

Language Understanding (LUIS) oferuje *ze wstępnie utworzonych domen*, które są wstępnie utworzone zestawy [intencji](luis-how-to-add-intents.md) i [jednostek](luis-concept-entity-types.md) współpracujące ze sobą dla domen lub typowe kategorie aplikacje klienckie. 

Ze wstępnie utworzonych domen są przeszkolony i chcesz dodać do aplikacji usługi LUIS. Intencje i podmioty, w domenie wbudowanych są w pełni konfigurowalne po dodaniu ich do swojej aplikacji. 

W przypadku uruchomienia Dostosowywanie wstępnie całej domeny, Usuń intencje i podmioty, nie trzeba używać twojej aplikacji. Do zestawu, który zapewnia wbudowane domeny, można dodać niektórych opcjami lub jednostki. Na przykład, jeśli używasz **zdarzenia** wbudowanych domeny dla aplikacji sportowych zdarzeń, można dodać jednostki dla zespołów, dyscyplin sportowych. Po uruchomieniu [dostarczanie wypowiedzi](luis-how-to-add-example-utterances.md) do usługi LUIS, zawierają postanowienia, które są specyficzne dla aplikacji. Usługa LUIS uczy się rozpoznawać je i dostosowanie intencje i podmioty do potrzeb swojej aplikacji, w domenie wbudowanych. 

> [!TIP]
> Intencje i podmioty, w domenie wbudowanych najlepiej działają razem. Zaleca się połączyć intencje i podmioty w tej samej domenie, jeśli jest to możliwe.
> Domena wstępnie utworzone narzędzia ma intencji, które można dostosować do użytku w każdej domenie. Na przykład można dodać `Utilities.Repeat` do swojej aplikacji i szkolenie go rozpoznać, niezależnie od działań użytkownika może być ma zostać powtórzony w aplikacji. 

### <a name="changing-the-behavior-of-a-prebuilt-domain-intent"></a>Zmiana zachowania intencji ze wstępnie utworzonych domen

Może się okazać, że wbudowanych domena zawiera cel, który jest podobny do intencji, które chcesz mieć w swojej aplikacji usługi LUIS, ale chcesz, aby zachowywać się inaczej. Na przykład **miejsc** udostępnia wstępnie utworzonych domen `MakeReservation` intencji składania rezerwacji restauracji, ale ma aplikację do używania z tym przeznaczeniem aby rezerwację hoteli. W takiej sytuacji możesz zmodyfikować zachowanie tym przeznaczeniem dostarczając wypowiedzi użytkownikowi usługi LUIS więcej o tworzeniu rezerwacji hotelowych i etykietowania je przy użyciu `MakeReservation` intencji, tak więc LUIS może być retrained rozpoznawanie `MakeReservation` intencji w żądaniu, aby zarezerwować hotelu .

Znajduje się pełna lista ze wstępnie utworzonych domen w [odwołania ze wstępnie utworzonych domen](./luis-reference-prebuilt-domains.md).

## <a name="prebuilt-intents"></a>Wstępnie utworzone intencji

Usługa LUIS udostępnia wstępnie intencje i ich wypowiedzi. Można dodać intencji bez dodawania całą domenę. Dodawanie intencji jest proces dodawania intencji i jego wypowiedzi. Można modyfikować zarówno nazwę metody konwersji, jak i na liście wypowiedź.  

## <a name="prebuilt-entities"></a>Wstępnie utworzonych jednostek

Usługa LUIS zawiera zestaw wstępnie utworzonych jednostek rozpoznawania typowe rodzaje informacji, takich jak daty, godziny, numery, pomiarów i waluty. Obsługa wstępnie utworzone jednostki jest zależna od kultury aplikacją usługi LUIS. Aby uzyskać pełną listę wstępnie utworzone jednostki, które obsługuje usługi LUIS, w tym pomoc od kultury, zobacz [odwołania do wstępnie utworzone jednostki](./luis-reference-prebuilt-entities.md).

Gdy wstępnie utworzone jednostki znajduje się w aplikacji, jej prognozy są uwzględnione w opublikowanej aplikacji. Działanie wstępnie utworzonych jednostek jest wstępnie przeszkolonych i **nie** można modyfikować. Wykonaj następujące kroki, aby zobaczyć, jak działa wstępnie utworzone jednostki:

> [!NOTE]
> **BUILTIN.DateTime** jest przestarzała. Zastępuje się wyrazami [ **builtin.datetimeV2**](luis-reference-prebuilt-datetimev2.md), która umożliwia rozpoznawanie daty i zakresy czasu, a także Rozpoznawanie niejednoznacznych daty i godziny.

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się, jak [Dodaj ze wstępnie utworzonych jednostek](luis-prebuilt-entities.md) do swojej aplikacji.
