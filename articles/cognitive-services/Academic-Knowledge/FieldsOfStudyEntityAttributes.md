---
title: Pole analiza atrybutów jednostki — interfejs Academic Knowledge API
titlesuffix: Azure Cognitive Services
description: Dowiedz się, atrybuty, które można używać z obiektu pola analiza interfejsu Academic Knowledge API.
services: cognitive-services
author: alch-msft
manager: cgronlun
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: conceptual
ms.date: 03/31/2017
ms.author: alch
ms.openlocfilehash: 793b35d9c6412c40a87f3f91fcd772476d57584f
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/29/2019
ms.locfileid: "55154571"
---
# <a name="field-of-study-entity"></a>Pola jednostki badań

<sub> * Następujące atrybuty są specyficzne dla pola jednostki badań. (Ty = '6') </sub>

Name (Nazwa)    |Opis                            |Typ       | Operacje
------- | ------------------------------------- | --------- | ----------------------------
Identyfikator      |Identyfikator jednostki                              |Int64      |Równa się
FN      |Pole znormalizowaną nazwę analiza         |Ciąg     |Równa się
DFN     |Pole nazwy wyświetlanej analiza            |Ciąg     |brak
DW      |Pola Liczba całkowita cytatu analiza    |Int32      |brak  
ECC     |Pola Liczba całkowita szacowany cytatu|Int32      |brak
FL      |Poziom w polach badania hierarchii     |Int32      |Równa się, <br/>IsBetween
FP.FN   |Pole nadrzędne o nazwie analiza             |Ciąg     |Równa się
FP.FId  |Pole nadrzędne identyfikatora analiza               |Int64      |Równa się
FC.FN   |Pole podrzędne o nazwie analiza              |Ciąg     |Równa się
FC.FId  |Podrzędne pole Identyfikator badania                |Int64      |Równa się
