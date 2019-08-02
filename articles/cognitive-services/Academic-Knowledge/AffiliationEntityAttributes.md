---
title: Atrybuty jednostki przynależności do Academic Knowledge API
titlesuffix: Azure Cognitive Services
description: Informacje o atrybutach, których można używać z jednostką przynależności w Academic Knowledge API.
services: cognitive-services
author: alch-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: conceptual
ms.date: 03/23/2017
ms.author: alch
ROBOTS: NOINDEX
ms.openlocfilehash: 333875472d9b859196c4d828061301b9d32c4d5a
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/01/2019
ms.locfileid: "68705109"
---
# <a name="affiliation-entity"></a>Jednostka przynależności

<sub>* Następujące atrybuty są specyficzne dla jednostki przynależności. (Ty = "5")</sub>

Name (Nazwa)    |Opis                            |Type       | Operacje
------- | ------------------------------------- | --------- | ----------------------------
Id      |Identyfikator jednostki                              |Int64      |Równa się
AfN     |Nazwa nadana przynależności        |Ciąg     |Równa się
DAfN    |Nazwa wyświetlana przynależności       |Ciąg     |brak
CC      |Łączna liczba cytatów przynależności           |Int32      |brak  
ECC     |Łączna liczba cytatów dla przynależności |Int32      |brak

## <a name="extended-metadata-attributes"></a>Rozszerzone atrybuty metadanych ##

Name (Nazwa)    | Opis               
--------|---------------------------    
PC      |Liczba papieru przynależności
