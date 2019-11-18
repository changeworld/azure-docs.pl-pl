---
title: Pole atrybutów jednostek badania — Academic Knowledge API
titlesuffix: Azure Cognitive Services
description: Informacje o atrybutach, których można używać z polem jednostki badania w Academic Knowledge API.
services: cognitive-services
author: darrine
manager: kuansanw
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: conceptual
ms.date: 11/14/2019
ms.author: darrine
ROBOTS: NOINDEX
ms.openlocfilehash: b6bb5e0fd822541a0a4c10ef5f275d46664f8e10
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/17/2019
ms.locfileid: "74146476"
---
# <a name="field-of-study-entity"></a>Pole jednostki badania

> [!NOTE]
> Następujące atrybuty są specyficzne dla jednostki badania. (Ty = "6")

Nazwa | Opis | Typ | Operacje
--- | --- | --- | ---
CC      |Pole łącznej liczby cytatów    |Int32      |Brak  
DFN     |Pole nazwy wyświetlanej badania            |Ciąg     |Brak
ECC     |Pole łącznej szacowanej liczby cytatów|Int32      |Brak
FL      |Poziom w dziedzinach hierarchii badań     |Int32      |Equals, isBetween
FN      |Pole o znormalizowanej nazwie badania         |Ciąg     |Równa się
FC.FId  |Pole podrzędne identyfikatora badania                |Int64      |Równa się
FC.FN   |Pole podrzędne nazwy badania              |Ciąg     |Równa się
FP.FId  |Pole nadrzędne identyfikatora badania               |Int64      |Równa się
FP.FN   |Pole nadrzędne nazwy badania             |Ciąg     |Równa się
Identyfikator      |Identyfikator jednostki                              |Int64      |Równa się
PC    | Pole łącznej liczby publikacji | Int32 | Brak
