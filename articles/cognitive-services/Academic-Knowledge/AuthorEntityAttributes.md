---
title: Tworzenie atrybutów jednostki — interfejs Academic Knowledge API
titlesuffix: Azure Cognitive Services
description: Dowiedz się, atrybuty, które można użyć z jednostką autora w interfejsu Academic Knowledge API.
services: cognitive-services
author: alch-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: conceptual
ms.date: 03/23/2017
ms.author: alch
ms.openlocfilehash: d4b33c06ab023023aadf403cf0ef0b08c2bafc5f
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/07/2019
ms.locfileid: "55878939"
---
# <a name="author-entity"></a>Tworzenie jednostki
<sub> * Następujące atrybuty są specyficzne dla jednostki autora. (Ty = '1') </sub>

Name (Nazwa)    |Opis                            |Type       | Operacje
------- | ------------------------------------- | --------- | ----------------------------
Identyfikator      |Identyfikator jednostki                              |Int64      |Równa się
AuN     |Znormalizowaną nazwę autora                 |String     |Równa się
DAuN    |Nazwa wyświetlana autora                    |String     |brak
DW      |Liczba całkowita cytatu autora            |Int32      |brak  
ECC     |Liczba całkowita cytatu szacowany autora  |Int32      |brak
E       |Rozszerzone metadane (patrz tabela "Atrybutów rozszerzonych metadanych")  |String     |brak  


## <a name="extended-metadata-attributes"></a>Rozszerzone atrybuty metadanych ##

Name (Nazwa)    | Opis               
--------|---------------------------    
LKA.Afn     | Nazwa wyświetlana jego przynależność do skojarzonych z autorem  
LKA.AfId        | Identyfikator jednostki jego przynależność do skojarzonych z autorem
