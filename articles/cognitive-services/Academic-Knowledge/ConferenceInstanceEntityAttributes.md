---
title: Atrybuty jednostki wystąpienia konferencji — interfejs Academic Knowledge API
titlesuffix: Azure Cognitive Services
description: Dowiedz się, atrybuty, które można użyć z jednostką wystąpienie konferencji w interfejsu Academic Knowledge API.
services: cognitive-services
author: alch-msft
manager: cgronlun
ms.service: cognitive-services
ms.component: academic-knowledge
ms.topic: conceptual
ms.date: 03/23/2017
ms.author: alch
ms.openlocfilehash: 6111ad00044943f12b2e098c4fd07ffb40185799
ms.sourcegitcommit: 7824e973908fa2edd37d666026dd7c03dc0bafd0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/10/2018
ms.locfileid: "48902416"
---
# <a name="conference-instance-entity"></a>Jednostki wystąpienie konferencji

<sub> * Następujące atrybuty są specyficzne dla konferencji wystąpienia jednostki. (Ty = "4") </sub>

Name (Nazwa)    |Opis                            |Typ       | Operacje
------- | ------------------------------------- | --------- | ----------------------------
Identyfikator      |Identyfikator jednostki                              |Int64      |Równa się
CIN     |Konferencja znormalizowaną nazwę dla wystąpienia ({ConferenceSeriesNormalizedName} {ConferenceInstanceYear})        |Ciąg     |Równa się
DCN     |Nazwa wyświetlana wystąpienie konferencji ({ConferenceSeriesName}: {ConferenceInstanceYear})       |Ciąg     |brak
CIL     |Lokalizacja wystąpienie konferencji    |Ciąg     |Równa się,<br/>StartsWith
CISD    |Data rozpoczęcia wystąpienie konferencji  |Date       |Równa się,<br/>IsBetween
CIED    |Data zakończenia wystąpienie konferencji    |Date       |Równa się,<br/>IsBetween
CIARD   |Abstrakcyjna rejestracji Data wystąpienie konferencji  |Date       |Równa się,<br/>IsBetween
CISDD   |Przesyłanie Data wystąpienie konferencji     |Date       |Równa się,<br/>IsBetween
CIFVD   |Ostateczna wersja Data wystąpienie konferencji  |Date       |Równa się,<br/>IsBetween
CINDD   |Data powiadomień wystąpienie konferencji   |Date       |Równa się,<br/>IsBetween
CIĄGŁE DOSTARCZANIE. T    |Tytuł zdarzenia wystąpienie konferencji   |Date       |Równa się,<br/>IsBetween
CIĄGŁE DOSTARCZANIE. D    |Data zdarzenia wystąpienie konferencji    |Date       |Równa się,<br/>IsBetween
KOMPUTERY. CN  |Nazwa serii konferencji wystąpienia |Ciąg     |Równa się
KOMPUTERY. Identyfikator CId |Seria konferencji identyfikator wystąpienia |Int64    |Równa się
DW      |Liczba całkowita cytatu wystąpienie konferencji           |Int32      |brak  
ECC     |Liczba całkowita cytatu szacowany wystąpień konferencji |Int32      |brak


## <a name="extended-metadata-attributes"></a>Rozszerzone atrybuty metadanych ##

Name (Nazwa)    | Opis               
--------|---------------------------    
FN      | Pełna nazwa wystąpienie konferencji