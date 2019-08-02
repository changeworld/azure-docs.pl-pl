---
title: Pole atrybutów jednostek badania — Academic Knowledge API
titlesuffix: Azure Cognitive Services
description: Informacje o atrybutach, których można używać z polem jednostki badania w Academic Knowledge API.
services: cognitive-services
author: alch-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: conceptual
ms.date: 03/31/2017
ms.author: alch
ROBOTS: NOINDEX
ms.openlocfilehash: d54f3c72462e6702b09068092b7c18ea50f12048
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/01/2019
ms.locfileid: "68704998"
---
# <a name="field-of-study-entity"></a>Pole jednostki badania

<sub>* Następujące atrybuty są specyficzne dla pola jednostki badania. (Ty = "6")</sub>

Name (Nazwa)    |Opis                            |Type       | Operacje
------- | ------------------------------------- | --------- | ----------------------------
Id      |Identyfikator jednostki                              |Int64      |Równa się
FN      |Pole o znormalizowanej nazwie badania         |String     |Równa się
DFN     |Pole nazwy wyświetlanej badania            |Ciąg     |brak
CC      |Pole łącznej liczby cytatów    |Int32      |brak  
ECC     |Pole łącznej szacowanej liczby cytatów|Int32      |brak
FL      |Poziom w dziedzinach hierarchii badań     |Int32      |Ubiegł <br/>IsBetween
FP.FN   |Pole nadrzędne nazwy badania             |String     |Równa się
FP.FId  |Pole nadrzędne identyfikatora badania               |Int64      |Równa się
FC.FN   |Pole podrzędne nazwy badania              |String     |Równa się
FC.FId  |Pole podrzędne identyfikatora badania                |Int64      |Równa się
