---
title: Atrybuty jednostki wystąpienia konferencji — Academic Knowledge API
titlesuffix: Azure Cognitive Services
description: Informacje o atrybutach, których można używać z jednostką wystąpienia konferencji w Academic Knowledge API.
services: cognitive-services
author: alch-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: conceptual
ms.date: 03/23/2017
ms.author: alch
ROBOTS: NOINDEX
ms.openlocfilehash: 37a353fbb86ca199b2316dcfba5904f4b46b0276
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/01/2019
ms.locfileid: "68705058"
---
# <a name="conference-instance-entity"></a>Jednostka wystąpienia konferencji

<sub>* Następujące atrybuty są specyficzne dla jednostki wystąpienia konferencji. (Ty = ' 4 ')</sub>

Name (Nazwa)    |Opis                            |Type       | Operacje
------- | ------------------------------------- | --------- | ----------------------------
Id      |Identyfikator jednostki                              |Int64      |Równa się
CIN     |Znormalizowana nazwa wystąpienia konferencji ({ConferenceSeriesNormalizedName} {ConferenceInstanceYear})        |Ciąg     |Równa się
DCN     |Nazwa wyświetlana wystąpienia konferencji ({ConferenceSeriesName}: {ConferenceInstanceYear})       |Ciąg     |brak
JĘZYKA     |Lokalizacja wystąpienia konferencji    |String     |Ubiegł<br/>StartsWith
CISD    |Data rozpoczęcia wystąpienia konferencji  |Date       |Ubiegł<br/>IsBetween
CIED    |Data końcowa wystąpienia konferencji    |Date       |Ubiegł<br/>IsBetween
CIARD   |Data ukończenia rejestracji abstrakcyjnej wystąpienia konferencji  |Date       |Ubiegł<br/>IsBetween
CISDD   |Data ukończenia zgłoszenia wystąpienia konferencji     |Date       |Ubiegł<br/>IsBetween
CIFVD   |Końcowa wersja ukończenia wystąpienia konferencji  |Date       |Ubiegł<br/>IsBetween
CINDD   |Data powiadomienia wystąpienia konferencji   |Date       |Ubiegł<br/>IsBetween
PŁYT. &    |Tytuł zdarzenia wystąpienia konferencji   |Date       |Ubiegł<br/>IsBetween
PŁYT. WYKRES    |Data zdarzenia wystąpienia konferencji    |Date       |Ubiegł<br/>IsBetween
PCS.CN  |Nazwa serii konferencji wystąpienia |String     |Równa się
PCs. Identyfikator |Identyfikator serii konferencji wystąpienia |Int64    |Równa się
CC      |Łączna liczba cytatów wystąpienia konferencji           |Int32      |brak  
ECC     |Łączna liczba cytatów dla wystąpienia konferencji |Int32      |brak


## <a name="extended-metadata-attributes"></a>Rozszerzone atrybuty metadanych ##

Name (Nazwa)    | Opis               
--------|---------------------------    
FN      | Pełna nazwa wystąpienia konferencji
