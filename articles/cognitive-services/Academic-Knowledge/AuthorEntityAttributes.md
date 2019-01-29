---
title: Tworzenie atrybutów jednostki — interfejs Academic Knowledge API
titlesuffix: Azure Cognitive Services
description: Dowiedz się, atrybuty, które można użyć z jednostką autora w interfejsu Academic Knowledge API.
services: cognitive-services
author: alch-msft
manager: cgronlun
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: conceptual
ms.date: 03/23/2017
ms.author: alch
ms.openlocfilehash: 48758ac9ec8c993bbdb490229ae20fcce1fb0a49
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/29/2019
ms.locfileid: "55175183"
---
# <a name="author-entity"></a>Tworzenie jednostki
<sub> * Następujące atrybuty są specyficzne dla jednostki autora. (Ty = '1') </sub>

Name    |Opis                            |Type       | Operacje
------- | ------------------------------------- | --------- | ----------------------------
Identyfikator      |Identyfikator jednostki                              |Int64      |Równa się
AuN     |Znormalizowaną nazwę autora                 |Ciąg     |Równa się
DAuN    |Nazwa wyświetlana autora                    |Ciąg     |brak
DW      |Liczba całkowita cytatu autora            |Int32      |brak  
ECC     |Liczba całkowita cytatu szacowany autora  |Int32      |brak
E       |Rozszerzone metadane (patrz tabela "Atrybutów rozszerzonych metadanych")  |Ciąg     |brak  


## <a name="extended-metadata-attributes"></a>Rozszerzone atrybuty metadanych ##

Name    | Opis               
--------|---------------------------    
LKA.Afn     | Nazwa wyświetlana jego przynależność do skojarzonych z autorem  
LKA.AfId        | Identyfikator jednostki jego przynależność do skojarzonych z autorem
