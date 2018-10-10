---
title: Tworzenie atrybutów jednostki — interfejs Academic Knowledge API
titlesuffix: Azure Cognitive Services
description: Dowiedz się, atrybuty, które można użyć z jednostką autora w interfejsu Academic Knowledge API.
services: cognitive-services
author: alch-msft
manager: cgronlun
ms.service: cognitive-services
ms.component: academic-knowledge
ms.topic: conceptual
ms.date: 03/23/2017
ms.author: alch
ms.openlocfilehash: 62e6da6d558a0494fb83115b1e307081099399d4
ms.sourcegitcommit: 7824e973908fa2edd37d666026dd7c03dc0bafd0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/10/2018
ms.locfileid: "48900693"
---
# <a name="author-entity"></a>Tworzenie jednostki
<sub> * Następujące atrybuty są specyficzne dla jednostki autora. (Ty = "1") </sub>

Name (Nazwa)    |Opis                            |Typ       | Operacje
------- | ------------------------------------- | --------- | ----------------------------
Identyfikator      |Identyfikator jednostki                              |Int64      |Równa się
AuN     |Znormalizowaną nazwę autora                 |Ciąg     |Równa się
DAuN    |Nazwa wyświetlana autora                    |Ciąg     |brak
DW      |Liczba całkowita cytatu autora            |Int32      |brak  
ECC     |Liczba całkowita cytatu szacowany autora  |Int32      |brak
E       |Rozszerzone metadane (patrz tabela "Atrybutów rozszerzonych metadanych")  |Ciąg     |brak  


## <a name="extended-metadata-attributes"></a>Rozszerzone atrybuty metadanych ##

Name (Nazwa)    | Opis               
--------|---------------------------    
LKA. Afn     | Nazwa wyświetlana jego przynależność do skojarzonych z autorem  
LKA. AfId        | Identyfikator jednostki jego przynależność do skojarzonych z autorem