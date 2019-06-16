---
title: Atrybuty jednostki wystąpienia konferencji — interfejs Academic Knowledge API
titlesuffix: Azure Cognitive Services
description: Dowiedz się, atrybuty, które można użyć z jednostką wystąpienie konferencji w interfejsu Academic Knowledge API.
services: cognitive-services
author: alch-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: conceptual
ms.date: 03/23/2017
ms.author: alch
ms.openlocfilehash: 183a307159adb5dfdb248eb0cf4862462a626db6
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60498750"
---
# <a name="conference-instance-entity"></a>Jednostki wystąpienie konferencji

<sub> * Następujące atrybuty są specyficzne dla konferencji wystąpienia jednostki. (Ty = "4") </sub>

Name (Nazwa)    |Opis                            |Typ       | Operacje
------- | ------------------------------------- | --------- | ----------------------------
Identyfikator      |Identyfikator jednostki                              |Int64      |Równa się
CIN     |Konferencja znormalizowaną nazwę dla wystąpienia ({ConferenceSeriesNormalizedName} {ConferenceInstanceYear})        |String     |Równa się
DCN     |Nazwa wyświetlana wystąpienie konferencji ({ConferenceSeriesName}: {ConferenceInstanceYear})       |String     |Brak
CIL     |Lokalizacja wystąpienie konferencji    |String     |Równa się,<br/>StartsWith
CISD    |Data rozpoczęcia wystąpienie konferencji  |Date       |Równa się,<br/>IsBetween
CIED    |Data zakończenia wystąpienie konferencji    |Date       |Równa się,<br/>IsBetween
CIARD   |Abstrakcyjna rejestracji Data wystąpienie konferencji  |Date       |Równa się,<br/>IsBetween
CISDD   |Przesyłanie Data wystąpienie konferencji     |Date       |Równa się,<br/>IsBetween
CIFVD   |Ostateczna wersja Data wystąpienie konferencji  |Date       |Równa się,<br/>IsBetween
CINDD   |Data powiadomień wystąpienie konferencji   |Date       |Równa się,<br/>IsBetween
CD.T    |Tytuł zdarzenia wystąpienie konferencji   |Date       |Równa się,<br/>IsBetween
CD.D    |Data zdarzenia wystąpienie konferencji    |Date       |Równa się,<br/>IsBetween
PCS.CN  |Nazwa serii konferencji wystąpienia |String     |Równa się
PCS.CId |Seria konferencji identyfikator wystąpienia |Int64    |Równa się
CC      |Liczba całkowita cytatu wystąpienie konferencji           |Int32      |Brak  
ECC     |Liczba całkowita cytatu szacowany wystąpień konferencji |Int32      |Brak


## <a name="extended-metadata-attributes"></a>Rozszerzone atrybuty metadanych ##

Name (Nazwa)    | Opis               
--------|---------------------------    
FN      | Pełna nazwa wystąpienie konferencji
