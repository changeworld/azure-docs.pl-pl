---
title: Atrybuty obiektu seria konferencji — interfejs Academic Knowledge API
titlesuffix: Azure Cognitive Services
description: Więcej informacji na temat atrybuty, które można użyć z jednostką seria konferencji.
services: cognitive-services
author: alch-msft
manager: cgronlun
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: conceptual
ms.date: 03/23/2017
ms.author: alch
ms.openlocfilehash: ff71b489cce01d8d6ea29e09905d7d3ac8429e34
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/29/2019
ms.locfileid: "55155609"
---
# <a name="conference-series-entity"></a>Jednostki seria konferencji

<sub> * Następujące atrybuty są specyficzne dla jednostki seria konferencji. (Ty = "3") </sub>

Name    |Opis                            |Type       | Operacje
------- | ------------------------------------- | --------- | ----------------------------
Identyfikator      |Identyfikator jednostki                              |Int64      |Równa się
CN      |Seria konferencji znormalizowane nazwy      |Ciąg     |Równa się
DCN     |Nazwa wyświetlana seria konferencji         |Ciąg     |brak
DW      |Seria konferencji całkowita liczba cytatu         |Int32      |brak  
ECC     |Liczba całkowita cytatu szacowany serii konferencji   |Int32      |brak
F.FId   |Pole identyfikatora jednostki badań skojarzona z serią konferencji |Int64  | Równa się
F.FN    |Pole o nazwie analiza skojarzona z serią konferencji  | Równa się,<br/>StartsWith
