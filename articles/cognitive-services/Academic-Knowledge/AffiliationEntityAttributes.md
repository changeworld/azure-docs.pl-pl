---
title: Przynależność do atrybutów jednostki w interfejsu Academic Knowledge API
titlesuffix: Azure Cognitive Services
description: Dowiedz się, atrybuty, które można użyć z jednostką przynależności w interfejsu Academic Knowledge API.
services: cognitive-services
author: alch-msft
manager: cgronlun
ms.service: cognitive-services
ms.component: academic-knowledge
ms.topic: conceptual
ms.date: 03/23/2017
ms.author: alch
ms.openlocfilehash: 177fe9da8bbe821a69eae02d89a225e5d4009331
ms.sourcegitcommit: 7824e973908fa2edd37d666026dd7c03dc0bafd0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/10/2018
ms.locfileid: "48900482"
---
# <a name="affiliation-entity"></a>Przynależność do jednostki

<sub> * Następujące atrybuty są specyficzne dla przynależność do jednostki. (Ty = "5") </sub>

Name (Nazwa)    |Opis                            |Typ       | Operacje
------- | ------------------------------------- | --------- | ----------------------------
Identyfikator      |Identyfikator jednostki                              |Int64      |Równa się
AfN     |Przynależność do znormalizowaną nazwę        |Ciąg     |Równa się
DAfN    |Nazwa wyświetlana przynależności       |Ciąg     |brak
DW      |Liczba całkowita cytatu przynależności           |Int32      |brak  
ECC     |Liczba całkowita cytatu szacowany przynależność do |Int32      |brak

## <a name="extended-metadata-attributes"></a>Rozszerzone atrybuty metadanych ##

Name (Nazwa)    | Opis               
--------|---------------------------    
PC      |Liczba dokument przynależność do firmy