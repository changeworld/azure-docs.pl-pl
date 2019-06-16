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
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "61340200"
---
# <a name="conference-series-entity"></a>Jednostki seria konferencji

<sub> * Następujące atrybuty są specyficzne dla jednostki seria konferencji. (Ty = "3") </sub>

Name (Nazwa)    |Opis                            |Typ       | Operacje
------- | ------------------------------------- | --------- | ----------------------------
Identyfikator      |Identyfikator jednostki                              |Int64      |Równa się
CN      |Seria konferencji znormalizowane nazwy      |String     |Równa się
DCN     |Nazwa wyświetlana seria konferencji         |String     |Brak
CC      |Seria konferencji całkowita liczba cytatu         |Int32      |Brak  
ECC     |Liczba całkowita cytatu szacowany serii konferencji   |Int32      |Brak
F.FId   |Pole identyfikatora jednostki badań skojarzona z serią konferencji |Int64  | Równa się
F.FN    |Pole o nazwie analiza skojarzona z serią konferencji  | Równa się,<br/>StartsWith
