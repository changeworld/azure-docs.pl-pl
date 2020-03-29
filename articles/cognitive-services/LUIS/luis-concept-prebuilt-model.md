---
title: Wstępnie utworzone modele - LUIS
titleSuffix: Azure Cognitive Services
description: Wstępnie utworzone modele zapewniają domeny, intencje, wypowiedzi i jednostki. Możesz uruchomić aplikację z wstępnie skompilowana domeną lub dodać odpowiednią domenę do aplikacji później.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "74280841"
---
# <a name="prebuilt-models"></a>Wstępnie utworzone modele

Wstępnie utworzone modele zapewniają domeny, intencje, wypowiedzi i jednostki. Aplikację można uruchomić za pomocą wstępnie utworzonego modelu lub dodać odpowiedni model do aplikacji później. 

## <a name="types-of-prebuilt-models"></a>Rodzaje wstępnie zbudowanych modeli

Usługa LUIS udostępnia trzy typy wstępnie utworzonych modeli. Każdy model można dodać do aplikacji w dowolnym momencie. 

|Typ modelu|Zawiera|
|--|--|
|[Domain](luis-reference-prebuilt-domains.md)|Intencje, wypowiedzi, jednostki|
|Intencje|Intencje, wypowiedzi|
|[Jednostki](luis-reference-prebuilt-entities.md)|Tylko jednostki| 

## <a name="prebuilt-domains"></a>Wstępnie utworzone domeny

Zrozumienie języka (LUIS) udostępnia *wstępnie utworzone domeny,* które są wstępnie [przeszkolonymi modelami intencji](luis-how-to-add-intents.md) i [encjami,](luis-concept-entity-types.md) które współpracują ze sobą dla domen lub typowych kategorii aplikacji klienckich. 

Wstępnie utworzone domeny są przeszkolone i gotowe do dodania do aplikacji usługi LUIS. Intencje i jednostki wstępnie utworzonej domeny są w pełni konfigurowalne po dodaniu ich do aplikacji. 

> [!TIP]
> Intencje i jednostki w wstępnie utworzonej domenie działają najlepiej razem. W miarę możliwości lepiej połączyć intencje i jednostki z tej samej domeny.
> Wstępnie skompilowana domena narzędzia ma intencje, które można dostosować do użycia w dowolnej domenie. Na przykład można `Utilities.Repeat` dodać do aplikacji i trenować go rozpoznać, niezależnie od działań użytkownik może chcieć powtórzyć w aplikacji. 

### <a name="changing-the-behavior-of-a-prebuilt-domain-intent"></a>Zmienianie zachowania wstępnie utworzonej intencji domeny

Może się okazać, że wstępnie skompilowana domena zawiera intencję podobną do intencji, którą chcesz mieć w aplikacji usługi LUIS, ale chcesz, aby zachowywała się inaczej. Na przykład **wstępnie** skompilowana `MakeReservation` domena Miejsca umożliwia dokonanie rezerwacji restauracji, ale chcesz, aby aplikacja używała tego zamiaru do dokonywania rezerwacji hoteli. W takim przypadku można zmodyfikować zachowanie tego zamiaru, dodając wypowiedzi przykład do intencji dotyczące dokonywania rezerwacji hotelu, a następnie przekwalifikować aplikację. 

Pełną listę wstępnie utworzonych domen można znaleźć w [odwołaniu do wstępnie utworzonych domen](./luis-reference-prebuilt-domains.md).

## <a name="prebuilt-intents"></a>Wstępnie utworzone intencje

Usługa LUIS udostępnia wstępnie utworzone intencje i ich wypowiedzi dla każdej z jego wstępnie utworzonych domen. Intencje można dodać bez dodawania całej domeny. Dodawanie intencji jest proces dodawania intencji i jego wypowiedzi do aplikacji. Zarówno nazwa intencji, jak i lista wypowiedź mogą być modyfikowane.  

## <a name="prebuilt-entities"></a>Wstępnie utworzone jednostki

Usługa LUIS zawiera zestaw wstępnie utworzonych jednostek do rozpoznawania typowych typów informacji, takich jak daty, godziny, liczby, pomiary i waluta. Wstępnie skompilowana obsługa jednostek zależy od kultury aplikacji usługi LUIS. Aby uzyskać pełną listę wstępnie utworzonych jednostek obsługiwanych przez usługę LUIS, w tym obsługę według kultury, zobacz [wstępnie utworzone odwołanie do jednostki](./luis-reference-prebuilt-entities.md).

Gdy wstępnie utworzone jednostki jest uwzględniony w aplikacji, jej prognozy są uwzględniane w opublikowanej aplikacji. Zachowanie wstępnie utworzonych jednostek jest wstępnie przeszkolony i **nie można** zmodyfikować. 

> [!NOTE]
> **builtin.datetime** jest przestarzały. Zastępuje go [**builtin.datetimeV2**](luis-reference-prebuilt-datetimev2.md), który zapewnia rozpoznawanie zakresów dat i godzin, a także lepsze rozpoznawanie niejednoznacznych dat i godzin.

## <a name="next-steps"></a>Następne kroki

Dowiedz się, jak [dodać wstępnie utworzone jednostki](luis-prebuilt-entities.md) do aplikacji.
