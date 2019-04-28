---
title: Interfejs Academic Graph atrybutów jednostki - interfejsu Academic Knowledge API
titlesuffix: Azure Cognitive Services
description: Więcej informacji na temat atrybutów jednostki, których można używać z Academic Graph w interfejsu Academic Knowledge API.
services: cognitive-services
author: alch-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: conceptual
ms.date: 03/27/2017
ms.author: alch
ms.openlocfilehash: 485775660ecfdf2291365ab98c9188295ea2cbde
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61340183"
---
# <a name="entity-attributes"></a>Atrybuty obiektu:

Interfejs academic graph składa się z 7 typów jednostek. Wszystkie jednostki mają Identyfikatora jednostki i typ jednostki.

## <a name="common-entity-attributes"></a>Wspólne atrybuty jednostki
Name (Nazwa)    |Opis                |Type       | Operacje
------- | ------------------------- | --------- | ----------------------------
Identyfikator      |Identyfikator jednostki                  |Int64      |Równa się
Ty      |Typ jednostki                |Wyliczenia   |Równa się

## <a name="entity-type-enum"></a>Jednostka typu enum
Name (Nazwa)                                                            |value
----------------------------------------------------------------|-----
[Dokument](PaperEntityAttributes.md)                               |0
[Autor](AuthorEntityAttributes.md)                             |1
[Dziennik](JournalEntityAttributes.md)                           |2
[Seria konferencji](JournalEntityAttributes.md)                 |3
[Wystąpienie konferencji](ConferenceInstanceEntityAttributes.md)    |4
[Przynależność](AffiliationEntityAttributes.md)                   |5
[Zakres badań](FieldsOfStudyEntityAttributes.md)                      |6

