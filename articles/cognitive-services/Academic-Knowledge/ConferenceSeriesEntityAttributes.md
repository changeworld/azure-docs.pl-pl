---
title: Atrybuty obiektu seria konferencji — interfejs Academic Knowledge API
titlesuffix: Azure Cognitive Services
description: Więcej informacji na temat atrybuty, które można użyć z jednostką seria konferencji.
services: cognitive-services
author: alch-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: conceptual
ms.date: 03/23/2017
ms.author: alch
ms.openlocfilehash: 38b4aa4c899668a68041f042ce6981ddd8c58219
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61340200"
---
# <a name="conference-series-entity"></a>Jednostki seria konferencji

<sub> * Następujące atrybuty są specyficzne dla jednostki seria konferencji. (Ty = "3") </sub>

Name (Nazwa)    |Opis                            |Type       | Operacje
------- | ------------------------------------- | --------- | ----------------------------
Identyfikator      |Identyfikator jednostki                              |Int64      |Równa się
CN      |Seria konferencji znormalizowane nazwy      |String     |Równa się
DCN     |Nazwa wyświetlana seria konferencji         |String     |brak
DW      |Seria konferencji całkowita liczba cytatu         |Int32      |brak  
ECC     |Liczba całkowita cytatu szacowany serii konferencji   |Int32      |brak
F.FId   |Pole identyfikatora jednostki badań skojarzona z serią konferencji |Int64  | Równa się
F.FN    |Pole o nazwie analiza skojarzona z serią konferencji  | Równa się,<br/>StartsWith
