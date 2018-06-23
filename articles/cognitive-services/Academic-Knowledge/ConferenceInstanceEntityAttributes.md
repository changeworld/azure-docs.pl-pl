---
title: Atrybuty obiektu wystąpienia konferencji w Academic Knowledge API | Dokumentacja firmy Microsoft
description: Dowiedz się, atrybuty, które można używać z konferencji wystąpienia jednostki w Academic Knowledge API w usługach kognitywnych.
services: cognitive-services
author: alch-msft
manager: kuansanw
ms.service: cognitive-services
ms.component: academic-knowledge
ms.topic: article
ms.date: 03/23/2017
ms.author: alch
ms.openlocfilehash: ef2bca4346a4666905f3dfb7bd448720f3b0ef8b
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "35346881"
---
# <a name="conference-instance-entity"></a>Konferencja wystąpienia jednostki

<sub> * Następujące atrybuty są specyficzne dla konferencji wystąpienia jednostki. (Ty = "4") </sub>

Name (Nazwa)    |Opis                            |Typ       | Operacje
------- | ------------------------------------- | --------- | ----------------------------
Identyfikator      |Identyfikator jednostki                              |Int64      |Równa się
CIN     |Znormalizowaną nazwę wystąpienia konferencji ({ConferenceSeriesNormalizedName} {ConferenceInstanceYear})        |Ciąg     |Równa się
DCN     |Nazwa wyświetlana wystąpienia konferencji ({ConferenceSeriesName}: {ConferenceInstanceYear})       |Ciąg     |brak
CIL     |Lokalizacja wystąpienia konferencji    |Ciąg     |Równy,<br/>StartsWith
CISD    |Data rozpoczęcia wystąpienia konferencji  |Date       |Równy,<br/>IsBetween
CIED    |Data zakończenia wystąpienia konferencji    |Date       |Równy,<br/>IsBetween
CIARD   |Abstract rejestracji termin wystąpienia konferencji  |Date       |Równy,<br/>IsBetween
CISDD   |Przesłanie termin wystąpienia konferencji     |Date       |Równy,<br/>IsBetween
CIFVD   |Wersja ostateczna data wystąpienia konferencji  |Date       |Równy,<br/>IsBetween
CINDD   |Data wystąpienia konferencji powiadomień   |Date       |Równy,<br/>IsBetween
DYSK CD. T    |Tytuł konferencji wystąpienia zdarzenia   |Date       |Równy,<br/>IsBetween
DYSK CD. D    |Data konferencji wystąpienia zdarzenia    |Date       |Równy,<br/>IsBetween
KOMPUTERY. NAZWA POSPOLITA  |Nazwa serii konferencji wystąpienia |Ciąg     |Równa się
KOMPUTERY. CId |Identyfikator serii konferencji wystąpienia |Int64    |Równa się
DW      |Liczba całkowita cytatu wystąpień konferencji           |Int32      |brak  
ECC     |Liczba całkowita cytatu szacowany wystąpień konferencji |Int32      |brak


## <a name="extended-metadata-attributes"></a>Rozszerzone atrybuty metadanych ##

Name (Nazwa)    | Opis               
--------|---------------------------    
FN      | Pełna nazwa wystąpienia konferencji