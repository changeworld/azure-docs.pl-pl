---
title: Atrybuty obiektu pola analizę w Academic Knowledge API | Dokumentacja firmy Microsoft
description: Dowiedz się, atrybuty, które można użyć z jednostką pola analizę w Academic Knowledge API w usługach kognitywnych.
services: cognitive-services
author: alch-msft
manager: kuansanw
ms.service: cognitive-services
ms.component: academic-knowledge
ms.topic: article
ms.date: 03/31/2017
ms.author: alch
ms.openlocfilehash: a8176370f5b2f63b7741f7e892ed5a8c775f19c1
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "35346857"
---
# <a name="field-of-study-entity"></a>Pole jednostki badań

<sub> * Następujące atrybuty są specyficzne dla pola badania jednostki. (Ty = "6") </sub>

Name (Nazwa)    |Opis                            |Typ       | Operacje
------- | ------------------------------------- | --------- | ----------------------------
Identyfikator      |Identyfikator jednostki                              |Int64      |Równa się
FN      |Pole znormalizowaną nazwę — analiza         |Ciąg     |Równa się
DFN     |Pole Nazwa wyświetlana badań            |Ciąg     |brak
DW      |Pole Liczba całkowita cytatu badań    |Int32      |brak  
ECC     |Pole Liczba całkowita cytatu szacowany|Int32      |brak
FL      |W polach badania hierarchii na poziomie     |Int32      |Równy, <br/>IsBetween
FP. FN   |Pola nadrzędnego o nazwie badań             |Ciąg     |Równa się
FP. FId  |Pola nadrzędnego identyfikatora — analiza               |Int64      |Równa się
FC. FN   |Podrzędne pole nazwy badań              |Ciąg     |Równa się
FC. FId  |Podrzędne pole Identyfikator badania                |Int64      |Równa się