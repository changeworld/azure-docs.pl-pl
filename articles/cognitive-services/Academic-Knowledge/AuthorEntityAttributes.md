---
title: Tworzenie atrybutów jednostki w Academic Knowledge API | Dokumentacja firmy Microsoft
description: Dowiedz się, atrybuty, które można użyć z jednostką autora w Academic Knowledge API w usługach kognitywnych.
services: cognitive-services
author: alch-msft
manager: kuansanw
ms.service: cognitive-services
ms.component: academic-knowledge
ms.topic: article
ms.date: 03/23/2017
ms.author: alch
ms.openlocfilehash: afcc3257c49522a4631c4aae0e0c2b88373f9af1
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "35346853"
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
E       |Rozszerzone metadanych (patrz tabela "Rozszerzonych atrybutów Meta")  |Ciąg     |brak  


## <a name="extended-metadata-attributes"></a>Rozszerzone atrybuty metadanych ##

Name (Nazwa)    | Opis               
--------|---------------------------    
LKA. Afn     | Nazwa wyświetlana jego przynależność do skojarzonych z autora  
LKA. AfId        | Identyfikator jednostki jego przynależność do skojarzonych z autora