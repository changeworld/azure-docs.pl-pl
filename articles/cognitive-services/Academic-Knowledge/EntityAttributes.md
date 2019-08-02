---
title: Parametry jednostki grafu akademickiego — Academic Knowledge API
titlesuffix: Azure Cognitive Services
description: Dowiedz się więcej na temat atrybutów jednostki, których można użyć z wykresem akademickim w Academic Knowledge API.
services: cognitive-services
author: alch-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: conceptual
ms.date: 03/27/2017
ms.author: alch
ROBOTS: NOINDEX
ms.openlocfilehash: 596ce35a0d744691edd75d6071d4758691e0031b
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/01/2019
ms.locfileid: "68705030"
---
# <a name="entity-attributes"></a>Atrybuty jednostki

Wykres akademicki składa się z 7 typów jednostek. Wszystkie jednostki będą mieć identyfikator jednostki i typ jednostki.

## <a name="common-entity-attributes"></a>Typowe atrybuty jednostki
Name (Nazwa)    |Opis                |Type       | Operacje
------- | ------------------------- | --------- | ----------------------------
Id      |Identyfikator jednostki                  |Int64      |Równa się
Br      |Typ jednostki                |Wyliczenia   |Równa się

## <a name="entity-type-enum"></a>Wyliczenie typu jednostki
Name (Nazwa)                                                            |value
----------------------------------------------------------------|-----
[Dokument](PaperEntityAttributes.md)                               |0
[Autor](AuthorEntityAttributes.md)                             |1
[Dziennik](JournalEntityAttributes.md)                           |2
[Seria konferencyjna](JournalEntityAttributes.md)                 |3
[Wystąpienie konferencji](ConferenceInstanceEntityAttributes.md)    |4
[Przynależność](AffiliationEntityAttributes.md)                   |5
[Pole analizy](FieldsOfStudyEntityAttributes.md)                      |6

