---
title: Przynależność do atrybutów jednostki w interfejsu Academic Knowledge API
titlesuffix: Azure Cognitive Services
description: Dowiedz się, atrybuty, które można użyć z jednostką przynależności w interfejsu Academic Knowledge API.
services: cognitive-services
author: alch-msft
manager: cgronlun
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: conceptual
ms.date: 03/23/2017
ms.author: alch
ms.openlocfilehash: 344b26b16f74cd44982e3c93fa69295792daa9a0
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/29/2019
ms.locfileid: "55190647"
---
# <a name="affiliation-entity"></a>Przynależność do jednostki

<sub> * Następujące atrybuty są specyficzne dla przynależność do jednostki. (Ty = '5') </sub>

Name    |Opis                            |Type       | Operacje
------- | ------------------------------------- | --------- | ----------------------------
Identyfikator      |Identyfikator jednostki                              |Int64      |Równa się
AfN     |Przynależność do znormalizowaną nazwę        |Ciąg     |Równa się
DAfN    |Nazwa wyświetlana przynależności       |Ciąg     |brak
DW      |Liczba całkowita cytatu przynależności           |Int32      |brak  
ECC     |Liczba całkowita cytatu szacowany przynależność do |Int32      |brak

## <a name="extended-metadata-attributes"></a>Rozszerzone atrybuty metadanych ##

Name    | Opis               
--------|---------------------------    
PC      |Liczba dokument przynależność do firmy
