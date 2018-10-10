---
title: Pole analiza atrybutów jednostki — interfejs Academic Knowledge API
titlesuffix: Azure Cognitive Services
description: Dowiedz się, atrybuty, które można używać z obiektu pola analiza interfejsu Academic Knowledge API.
services: cognitive-services
author: alch-msft
manager: cgronlun
ms.service: cognitive-services
ms.component: academic-knowledge
ms.topic: conceptual
ms.date: 03/31/2017
ms.author: alch
ms.openlocfilehash: 862fd6d506d5f1ca6f7f532f80f53a29200f33db
ms.sourcegitcommit: 7824e973908fa2edd37d666026dd7c03dc0bafd0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/10/2018
ms.locfileid: "48900431"
---
# <a name="field-of-study-entity"></a>Pola jednostki badań

<sub> * Następujące atrybuty są specyficzne dla pola jednostki badań. (Ty = "6") </sub>

Name (Nazwa)    |Opis                            |Typ       | Operacje
------- | ------------------------------------- | --------- | ----------------------------
Identyfikator      |Identyfikator jednostki                              |Int64      |Równa się
FN      |Pole znormalizowaną nazwę analiza         |Ciąg     |Równa się
DFN     |Pole nazwy wyświetlanej analiza            |Ciąg     |brak
DW      |Pola Liczba całkowita cytatu analiza    |Int32      |brak  
ECC     |Pola Liczba całkowita szacowany cytatu|Int32      |brak
FL      |Poziom w polach badania hierarchii     |Int32      |Równa się, <br/>IsBetween
FP. FN   |Pole nadrzędne o nazwie analiza             |Ciąg     |Równa się
FP. FId  |Pole nadrzędne identyfikatora analiza               |Int64      |Równa się
FC. FN   |Pole podrzędne o nazwie analiza              |Ciąg     |Równa się
FC. FId  |Podrzędne pole Identyfikator badania                |Int64      |Równa się