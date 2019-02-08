---
title: Role dla jednostek
titleSuffix: Azure Cognitive Services
description: Role są podtypy nazwanych, kontekstowych podmiotu używana tylko we wzorcach. Na przykład w wypowiedź `buy a ticket from New York to London`, zarówno w Nowym Jorku, jak i w Londynie są miast, ale każda ma inne znaczenie w zdaniu. Nowy Jork jest miasto źródła i Londyn jest miasta docelowego.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 12/17/2018
ms.author: diberry
ms.openlocfilehash: 958194d49cd403caeaf9dd21dd90a02cab098e45
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/07/2019
ms.locfileid: "55881461"
---
# <a name="entity-roles-in-patterns-are-contextual-subtypes"></a>Role jednostki we wzorcach są podtypy kontekstowych
Role są podtypy nazwanych, kontekstowych podmiotu używana tylko w [wzorców](luis-concept-patterns.md).

Na przykład w wypowiedź `buy a ticket from New York to London`, zarówno w Nowym Jorku, jak i w Londynie są miast, ale każda ma inne znaczenie w zdaniu. Nowy Jork jest miasto źródła i Londyn jest miasta docelowego. 

Role nadaj nazwę różnic:

|Jednostka|Rola|Przeznaczenie|
|--|--|--|
|Lokalizacja|źródło|Jeżeli płaszczyzny pozostawia z|
|Lokalizacja|miejsce docelowe|gdzie wyładowuje płaszczyzna|
|Wstępnie utworzone datetimeV2|na|Data zakończenia|
|Wstępnie utworzone datetimeV2|z|Data rozpoczęcia|

## <a name="how-are-roles-used-in-patterns"></a>Jak role są używane we wzorcach?
W polu wypowiedź szablonu wzorca role są używane w ramach wypowiedź: 

|Wzorzec z rolami jednostki|
|--|
|`buy a ticket from {Location:origin} to {Location:destination}`|


## <a name="role-syntax-in-patterns"></a>Składnia roli we wzorcach
Jednostki i rola są ujęte w nawiasach, `{}`. Jednostka i rola są rozdzielone średnikiem. 


[!INCLUDE [H2 Roles versus hierarchical entities](../../../includes/cognitive-services-luis-hier-roles.md)] 

## <a name="example-role-for-entities"></a>Przykład roli dla jednostek

Rola to kontekstowe nauczony położenie jednostki w ramach wypowiedź. Może to być najbardziej efektywne, gdy wypowiedź ma więcej niż jedną tego typu jednostki. Najprostszym przykład dla dowolnego typu jednostki jest rozróżnienie między do i z lokalizacji. Lokalizacja może być reprezentowany w partii jednostek różnych typów. 

Przypadek użycia przykład transferuje pracownika z jednego działu do innego gdzie każdy dział jest element na liście. Na przykład: 

`Move [PersonName] from [Department:from] to [Department:to]`. 

Prognozowania zwracane zarówno jednostki działu zostaną zwrócone w odpowiedzi JSON, a każda będzie zawierać nazwę roli. 

## <a name="roles-with-prebuilt-entities"></a>Role z wstępnie utworzonych jednostek

Za pomocą ról wstępnie utworzonych jednostek oferowanie znaczenie różnych wystąpień wstępnie utworzone jednostki w ramach wypowiedź. 

### <a name="roles-with-datetimev2"></a>Role z datetimeV2

Wstępnie utworzone jednostki, datetimeV2, wykonuje świetnie zrozumienia szeroką gamę różnych w daty i godziny w wypowiedzi. Można określić daty i zakresy dat inaczej niż wstępnie utworzone jednostki domyślnej interpretacji. 

## <a name="next-steps"></a>Kolejne kroki

* Dowiedz się, jak dodać [ról](luis-how-to-add-entities.md#add-a-role-to-pattern-based-entity)
