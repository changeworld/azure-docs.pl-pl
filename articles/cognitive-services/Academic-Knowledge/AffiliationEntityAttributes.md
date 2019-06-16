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
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "61340523"
---
# <a name="affiliation-entity"></a>Przynależność do jednostki

<sub> * Następujące atrybuty są specyficzne dla przynależność do jednostki. (Ty = '5') </sub>

Name (Nazwa)    |Opis                            |Typ       | Operacje
------- | ------------------------------------- | --------- | ----------------------------
Identyfikator      |Identyfikator jednostki                              |Int64      |Równa się
AfN     |Przynależność do znormalizowaną nazwę        |String     |Równa się
DAfN    |Nazwa wyświetlana przynależności       |String     |Brak
CC      |Liczba całkowita cytatu przynależności           |Int32      |Brak  
ECC     |Liczba całkowita cytatu szacowany przynależność do |Int32      |Brak

## <a name="extended-metadata-attributes"></a>Rozszerzone atrybuty metadanych ##

Name (Nazwa)    | Opis               
--------|---------------------------    
PC      |Liczba dokument przynależność do firmy
