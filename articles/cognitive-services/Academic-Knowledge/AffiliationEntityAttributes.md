---
title: Przynależność do atrybutów jednostki w interfejsu Academic Knowledge API
titlesuffix: Azure Cognitive Services
description: Dowiedz się, atrybuty, które można użyć z jednostką przynależności w interfejsu Academic Knowledge API.
services: cognitive-services
author: alch-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: conceptual
ms.date: 03/23/2017
ms.author: alch
ms.openlocfilehash: 82e6a5b66342e58e62da029d617cbd1d74c28149
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61340523"
---
# <a name="affiliation-entity"></a>Przynależność do jednostki

<sub> * Następujące atrybuty są specyficzne dla przynależność do jednostki. (Ty = '5') </sub>

Name (Nazwa)    |Opis                            |Type       | Operacje
------- | ------------------------------------- | --------- | ----------------------------
Identyfikator      |Identyfikator jednostki                              |Int64      |Równa się
AfN     |Przynależność do znormalizowaną nazwę        |String     |Równa się
DAfN    |Nazwa wyświetlana przynależności       |String     |brak
DW      |Liczba całkowita cytatu przynależności           |Int32      |brak  
ECC     |Liczba całkowita cytatu szacowany przynależność do |Int32      |brak

## <a name="extended-metadata-attributes"></a>Rozszerzone atrybuty metadanych ##

Name (Nazwa)    | Opis               
--------|---------------------------    
PC      |Liczba dokument przynależność do firmy
