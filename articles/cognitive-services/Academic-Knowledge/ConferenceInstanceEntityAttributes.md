---
title: Atrybuty jednostki wystąpienia konferencji — interfejs Academic Knowledge API
titlesuffix: Azure Cognitive Services
description: Dowiedz się, atrybuty, które można użyć z jednostką wystąpienie konferencji w interfejsu Academic Knowledge API.
services: cognitive-services
author: alch-msft
manager: cgronlun
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: conceptual
ms.date: 03/23/2017
ms.author: alch
ms.openlocfilehash: db025f377a3fab2f788252db0c8e3555837a6de8
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/29/2019
ms.locfileid: "55196110"
---
# <a name="conference-instance-entity"></a>Jednostki wystąpienie konferencji

<sub> * Następujące atrybuty są specyficzne dla konferencji wystąpienia jednostki. (Ty = "4") </sub>

Name    |Opis                            |Type       | Operacje
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
CD.T    |Tytuł zdarzenia wystąpienie konferencji   |Date       |Równa się,<br/>IsBetween
CD.D    |Data zdarzenia wystąpienie konferencji    |Date       |Równa się,<br/>IsBetween
PCS.CN  |Nazwa serii konferencji wystąpienia |Ciąg     |Równa się
PCS.CId |Seria konferencji identyfikator wystąpienia |Int64    |Równa się
DW      |Liczba całkowita cytatu wystąpienie konferencji           |Int32      |brak  
ECC     |Liczba całkowita cytatu szacowany wystąpień konferencji |Int32      |brak


## <a name="extended-metadata-attributes"></a>Rozszerzone atrybuty metadanych ##

Name    | Opis               
--------|---------------------------    
FN      | Pełna nazwa wystąpienie konferencji
