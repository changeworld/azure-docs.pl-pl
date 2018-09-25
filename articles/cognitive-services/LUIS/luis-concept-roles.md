---
title: Zrozumienie, jak role są używane w jednostkach na podstawie wzorca
titleSuffix: Azure Cognitive Services
description: Role są podtypy nazwanych, kontekstowych podmiotu używana tylko we wzorcach. Na przykład Kup wypowiedź biletu z nowego Jorku do Londynu, zarówno w Nowym Jorku, jak i w Londynie są miast, ale każda ma inne znaczenie w zdaniu. Nowy Jork jest miasto źródła i Londyn jest miasta docelowego.
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 09/10/2018
ms.author: diberry
ms.openlocfilehash: 033e5e5e054b0a29961ad60e72b1466b51d1df0c
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/24/2018
ms.locfileid: "47035202"
---
# <a name="entity-roles-in-patterns-are-contextual-subtypes"></a>Role jednostki we wzorcach są podtypy kontekstowych
Role są podtypy nazwanych, kontekstowych podmiotu używana tylko w [wzorców](luis-concept-patterns.md).

Na przykład w wypowiedź `buy a ticket from New York to London`, zarówno w Nowym Jorku, jak i w Londynie są miast, ale każda ma inne znaczenie w zdaniu. Nowy Jork jest miasto źródła i Londyn jest miasta docelowego. 

Role nadaj nazwę różnic:

|Jednostka|Rola|Przeznaczenie|
|--|--|--|
|Lokalizacja|źródło|Jeżeli płaszczyzny pozostawia z|
|Lokalizacja|miejsce docelowe|gdzie wyładowuje płaszczyzna|

## <a name="how-are-roles-used-in-patterns"></a>Jak role są używane we wzorcach?
W polu wypowiedź szablonu wzorca role są używane w ramach wypowiedź: 

|Wzorzec z rolami jednostki|
|--|
|`buy a ticket from {Location:origin} to {Location:destination}`|


## <a name="role-syntax-in-patterns"></a>Składnia roli we wzorcach
Jednostki i rola są ujęte w nawiasach, `{}`. Jednostka i rola są rozdzielone średnikiem. 

## <a name="roles-versus-hierarchical-entities"></a>Role i hierarchiczne jednostek
Jednostki hierarchiczną zapewnia te same informacje kontekstowe jako role, ale tylko wypowiedzi w **intencji**. Podobnie, role zawierają te same informacje kontekstowe jako hierarchiczna jednostki, ale tylko w **wzorców**.

|Learning kontekstowych|Używane w|
|--|--|
|Hierarchiczna jednostek|Intencji|
|role|Wzorce|

## <a name="next-steps"></a>Kolejne kroki

* Dowiedz się, jak dodać [ról](luis-how-to-add-entities.md#add-role-to-pattern-based-entity)
