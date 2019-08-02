---
title: Tworzenie atrybutów jednostki — Academic Knowledge API
titlesuffix: Azure Cognitive Services
description: Informacje o atrybutach, których można użyć z jednostką autor w Academic Knowledge API.
services: cognitive-services
author: alch-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: conceptual
ms.date: 03/23/2017
ms.author: alch
ROBOTS: NOINDEX
ms.openlocfilehash: e63e9d3f8f17a2473caf3d31b83e318ddb132b43
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/01/2019
ms.locfileid: "68705090"
---
# <a name="author-entity"></a>Tworzenie jednostki
<sub>* Następujące atrybuty są specyficzne dla podmiotu autora. (Ty = ' 1 ')</sub>

Name (Nazwa)    |Opis                            |Type       | Operacje
------- | ------------------------------------- | --------- | ----------------------------
Id      |Identyfikator jednostki                              |Int64      |Równa się
AuN     |Nazwa zwyczajowa autora                 |String     |Równa się
DAuN    |Nazwa wyświetlana autora                    |String     |brak
CC      |Łączna liczba cytatów dla autora            |Int32      |brak  
ECC     |Całkowita Szacowana Liczba cytatów dla autora  |Int32      |brak
E       |Rozszerzone metadane (zobacz sekcję "rozszerzone atrybuty meta")  |Ciąg     |brak  


## <a name="extended-metadata-attributes"></a>Rozszerzone atrybuty metadanych ##

Name (Nazwa)    | Opis               
--------|---------------------------    
LKA. Afn     | Nazwa wyświetlana przynależności skojarzona z autorem  
LKA.AfId        | Identyfikator jednostki przynależności skojarzonej z autorem
