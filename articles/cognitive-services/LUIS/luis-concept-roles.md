---
title: Zrozumienie, jak role są używane w jednostki na podstawie wzorca - Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak rola jest używany w jednostki na podstawie wzorca do nadaj nazwę podtypu kontekstowe jednostki.
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.technology: luis
ms.topic: article
ms.date: 06/08/2018
ms.author: v-geberr;
ms.openlocfilehash: ab6100e33fb767528b87c6afde4c5ef275fc7c81
ms.sourcegitcommit: 50f82f7682447245bebb229494591eb822a62038
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/08/2018
ms.locfileid: "35356371"
---
# <a name="entity-roles-in-patterns-are-contextual-subtypes"></a>Role jednostki w wzorce są kontekstowe podtypów
Role są nazwanych, kontekstowe podtypów Jednostka używana tylko w [wzorców](luis-concept-patterns.md).

Na przykład w utterance `buy a ticket from New York to London`, zarówno w Nowym Jorku, jak i w Londynie są miast, ale każda ma inne znaczenie w zdaniu. Nowy Jork jest miasto pochodzenia i Londynie Miasto docelowe. 

Role nadaj nazwę różnic:

|Jednostka|Rola|Przeznaczenie|
|--|--|--|
|Lokalizacja|źródło|Jeżeli płaszczyzny pozostawia z|
|Lokalizacja|Miejsce docelowe|gdzie wyładowuje płaszczyzny|

## <a name="how-are-roles-used-in-patterns"></a>Jak role są używane we wzorcach
W utterance szablonu wzorzec role są używane w ramach utterance: 

```
buy a ticket from {Location:origin} to {Location:destination}
```

## <a name="role-syntax-in-patterns"></a>Składnia roli we wzorcach
Jednostki i rola są ujęte w nawiasy, `{}`. Obiekt i rola są oddzielone dwukropkiem. 

## <a name="roles-versus-hierarchical-entities"></a>Role i hierarchicznych jednostek
Hierarchiczna jednostek zapewniają te same informacje kontekstowe co ról, ale tylko zniesławiających w **intencje**. Podobnie, role zawierają te same informacje kontekstowe hierarchiczna jednostki, ale tylko w **wzorców**.

|Learning kontekstowe|Używane w|
|--|--|
|Hierarchiczna jednostek|Opcje|
|role|Wzorce|

## <a name="next-steps"></a>Kolejne kroki

* Dowiedz się, jak dodać [ról](luis-how-to-add-entities.md#add-role-to-pattern-based-entity)
