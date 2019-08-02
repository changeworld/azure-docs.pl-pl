---
title: Atrybuty jednostki serii konferencyjnej — Academic Knowledge API
titlesuffix: Azure Cognitive Services
description: Dowiedz się więcej na temat atrybutów, których można używać z jednostką z serii konferencji.
services: cognitive-services
author: alch-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: conceptual
ms.date: 03/23/2017
ms.author: alch
ROBOTS: NOINDEX
ms.openlocfilehash: feed324202f6a75ceb7e9089875b899c51cd8ae6
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/01/2019
ms.locfileid: "68705044"
---
# <a name="conference-series-entity"></a>Jednostka serii konferencji

<sub>* Następujące atrybuty są specyficzne dla jednostki szeregów konferencji. (Ty = ' 3 ')</sub>

Name (Nazwa)    |Opis                            |Type       | Operacje
------- | ------------------------------------- | --------- | ----------------------------
Id      |Identyfikator jednostki                              |Int64      |Równa się
CN      |Nazwa znormalizowana serii konferencji      |String     |Równa się
DCN     |Nazwa wyświetlana serii konferencji         |Ciąg     |brak
CC      |Łączna liczba cytatów serii konferencji         |Int32      |brak  
ECC     |Łączna liczba cytatów dla serii konferencji   |Int32      |brak
F.FId   |Pole identyfikatora jednostki badania skojarzone z serią konferencji |Int64  | Równa się
F.FN    |Pole nazwy badania skojarzone z serią konferencji  | Ubiegł<br/>StartsWith
