---
title: Role dla jednostek
titleSuffix: Azure Cognitive Services
description: Role są podtypy nazwanych, kontekstowych podmiotu używana tylko we wzorcach. Na przykład w wypowiedź `buy a ticket from New York to London`, zarówno w Nowym Jorku, jak i w Londynie są miast, ale każda ma inne znaczenie w zdaniu. Nowy Jork jest miasto źródła i Londyn jest miasta docelowego.
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: conceptual
ms.date: 09/10/2018
ms.author: diberry
ms.openlocfilehash: dd11c2c7062d09c522c46770e8dba6d6eb0ac1e9
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/08/2018
ms.locfileid: "53105568"
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

## <a name="roles-with-prebuilt-entities"></a>Role z wstępnie utworzonych jednostek

Za pomocą ról wstępnie utworzonych jednostek oferowanie znaczenie różnych wystąpień wstępnie utworzone jednostki w ramach wypowiedź. 

### <a name="roles-with-datetimev2"></a>Role z datetimeV2

Wstępnie utworzone jednostki, datetimeV2, wykonuje świetnie zrozumienia szeroką gamę różnych w daty i godziny w wypowiedzi. Można określić daty i zakresy dat inaczej niż wstępnie utworzone jednostki domyślnej interpretacji. 

## <a name="next-steps"></a>Kolejne kroki

* Dowiedz się, jak dodać [ról](luis-how-to-add-entities.md#add-a-role-to-pattern-based-entity)
