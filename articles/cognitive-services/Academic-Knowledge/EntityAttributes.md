---
title: Academic atrybuty obiektu wykresu Academic Knowledge interfejsu API | Dokumentacja firmy Microsoft
description: Więcej informacji na temat atrybuty jednostki, które można używać z Academic wykresu w Academic Knowledge API.
services: cognitive-services
author: alch-msft
manager: kuansanw
ms.service: cognitive-services
ms.component: academic-knowledge
ms.topic: article
ms.date: 03/27/2017
ms.author: alch
ms.openlocfilehash: f771969c3c6f05e5d2c99b1fbf593ae039ccb12c
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "35346849"
---
# <a name="entity-attributes"></a>Atrybuty obiektu

Wykres academic składa się z 7 typów jednostek. Wszystkie obiekty będą mieć identyfikator jednostki i typu jednostki.

## <a name="common-entity-attributes"></a>Atrybuty wspólne jednostki
Name (Nazwa)    |Opis                |Typ       | Operacje
------- | ------------------------- | --------- | ----------------------------
Identyfikator      |Identyfikator jednostki                  |Int64      |Równa się
Ty      |Typ jednostki                |wyliczenia   |Równa się

## <a name="entity-type-enum"></a>Jednostka typu wyliczenia
Name (Nazwa)                                                            |wartość
----------------------------------------------------------------|-----
[Dokument](PaperEntityAttributes.md)                               |0
[Autor](AuthorEntityAttributes.md)                             |1
[Dziennik](JournalEntityAttributes.md)                           |2
[Konferencja serii](JournalEntityAttributes.md)                 |3
[Wystąpienie konferencji](ConferenceInstanceEntityAttributes.md)    |4
[Przynależność](AffiliationEntityAttributes.md)                   |5
[Pole — analiza](FieldsOfStudyEntityAttributes.md)                      |6

