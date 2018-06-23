---
title: Atrybuty obiektu przynależności w Academic Knowledge API | Dokumentacja firmy Microsoft
description: Dowiedz się, atrybuty, które można użyć z jednostką przynależności w Academic Knowledge API w usługach kognitywnych.
services: cognitive-services
author: alch-msft
manager: kuansanw
ms.service: cognitive-services
ms.component: academic-knowledge
ms.topic: article
ms.date: 03/23/2017
ms.author: alch
ms.openlocfilehash: a0ec67cb811ca207b3d038028491da2516028f0b
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "35346852"
---
# <a name="affiliation-entity"></a>Przynależność do jednostki

<sub> * Następujące atrybuty są specyficzne dla przynależności do jednostki. (Ty = "5") </sub>

Name (Nazwa)    |Opis                            |Typ       | Operacje
------- | ------------------------------------- | --------- | ----------------------------
Identyfikator      |Identyfikator jednostki                              |Int64      |Równa się
AfN     |Przynależność do znormalizowaną nazwę        |Ciąg     |Równa się
DAfN    |Przynależność do nazwy wyświetlanej       |Ciąg     |brak
DW      |Liczba całkowita cytatu przynależności           |Int32      |brak  
ECC     |Liczba całkowita cytatu szacowany przynależności |Int32      |brak

## <a name="extended-metadata-attributes"></a>Rozszerzone atrybuty metadanych ##

Name (Nazwa)    | Opis               
--------|---------------------------    
PC      |Liczba papieru przynależność do firmy