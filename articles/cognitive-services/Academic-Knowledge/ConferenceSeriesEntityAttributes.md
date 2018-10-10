---
title: Atrybuty obiektu seria konferencji — interfejs Academic Knowledge API
titlesuffix: Azure Cognitive Services
description: Więcej informacji na temat atrybuty, które można użyć z jednostką seria konferencji.
services: cognitive-services
author: alch-msft
manager: cgronlun
ms.service: cognitive-services
ms.component: academic-knowledge
ms.topic: conceptual
ms.date: 03/23/2017
ms.author: alch
ms.openlocfilehash: f9f28afd7005d7a61aa0d2f4dba69ca598034b52
ms.sourcegitcommit: 7824e973908fa2edd37d666026dd7c03dc0bafd0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/10/2018
ms.locfileid: "48900752"
---
# <a name="conference-series-entity"></a>Jednostki seria konferencji

<sub> * Następujące atrybuty są specyficzne dla jednostki seria konferencji. (Ty = "3") </sub>

Name (Nazwa)    |Opis                            |Typ       | Operacje
------- | ------------------------------------- | --------- | ----------------------------
Identyfikator      |Identyfikator jednostki                              |Int64      |Równa się
CN      |Seria konferencji znormalizowane nazwy      |Ciąg     |Równa się
DCN     |Nazwa wyświetlana seria konferencji         |Ciąg     |brak
DW      |Seria konferencji całkowita liczba cytatu         |Int32      |brak  
ECC     |Liczba całkowita cytatu szacowany serii konferencji   |Int32      |brak
F.FId   |Pole identyfikatora jednostki badań skojarzona z serią konferencji |Int64  | Równa się
F.FN    |Pole o nazwie analiza skojarzona z serią konferencji  | Równa się,<br/>StartsWith