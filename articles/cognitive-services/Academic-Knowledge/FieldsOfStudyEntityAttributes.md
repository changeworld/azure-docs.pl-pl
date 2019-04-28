---
title: Pole analiza atrybutów jednostki — interfejs Academic Knowledge API
titlesuffix: Azure Cognitive Services
description: Dowiedz się, atrybuty, które można używać z obiektu pola analiza interfejsu Academic Knowledge API.
services: cognitive-services
author: alch-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: conceptual
ms.date: 03/31/2017
ms.author: alch
ms.openlocfilehash: e9d6badf76efd03c0520a728af7b3e47b25f200a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61339589"
---
# <a name="field-of-study-entity"></a>Pola jednostki badań

<sub> * Następujące atrybuty są specyficzne dla pola jednostki badań. (Ty = '6') </sub>

Name (Nazwa)    |Opis                            |Type       | Operacje
------- | ------------------------------------- | --------- | ----------------------------
Identyfikator      |Identyfikator jednostki                              |Int64      |Równa się
FN      |Pole znormalizowaną nazwę analiza         |String     |Równa się
DFN     |Pole nazwy wyświetlanej analiza            |String     |brak
DW      |Pola Liczba całkowita cytatu analiza    |Int32      |brak  
ECC     |Pola Liczba całkowita szacowany cytatu|Int32      |brak
FL      |Poziom w polach badania hierarchii     |Int32      |Równa się, <br/>IsBetween
FP.FN   |Pole nadrzędne o nazwie analiza             |String     |Równa się
FP.FId  |Pole nadrzędne identyfikatora analiza               |Int64      |Równa się
FC.FN   |Pole podrzędne o nazwie analiza              |String     |Równa się
FC.FId  |Podrzędne pole Identyfikator badania                |Int64      |Równa się
