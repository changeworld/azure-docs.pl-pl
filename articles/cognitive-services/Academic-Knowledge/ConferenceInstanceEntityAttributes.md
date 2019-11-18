---
title: Atrybuty jednostki wystąpienia konferencji — Academic Knowledge API
titlesuffix: Azure Cognitive Services
description: Informacje o atrybutach, których można używać z jednostką wystąpienia konferencji w Academic Knowledge API.
services: cognitive-services
author: darrine
manager: kuansanw
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: conceptual
ms.date: 11/14/2019
ms.author: darrine
ROBOTS: NOINDEX
ms.openlocfilehash: c6761206a58724dae96d9dc6f6234658892d4d18
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/17/2019
ms.locfileid: "74146564"
---
# <a name="conference-instance-entity"></a>Jednostka wystąpienia konferencji

> [!NOTE]
> Następujące atrybuty są specyficzne dla jednostki wystąpienia konferencji. (Ty = ' 4 ')

Nazwa | Opis | Typ | Operacje
--- | --- | --- | ---
CC      |Łączna liczba cytatów wystąpienia konferencji           |Int32      |Brak  
Płyt. Wykres    |Data zdarzenia wystąpienia konferencji    |Date       |Equals, isBetween
Płyt. &    |Tytuł zdarzenia wystąpienia konferencji   |Date       |Equals, isBetween
CIARD   |Data ukończenia rejestracji abstrakcyjnej wystąpienia konferencji  |Date       |Equals, isBetween
CIED    |Data końcowa wystąpienia konferencji    |Date       |Equals, isBetween
CIFVD   |Końcowa wersja ukończenia wystąpienia konferencji  |Date       |Equals, isBetween
JĘZYKA     |Lokalizacja wystąpienia konferencji    |Ciąg     |Równa się, StartsWith
CIN     |Nazwa znormalizowana wystąpienia konferencji |Ciąg        |Równa się
CINDD   |Data powiadomienia wystąpienia konferencji   |Date       |Equals, isBetween
CISD    |Data rozpoczęcia wystąpienia konferencji  |Date       |Equals, isBetween
CISDD   |Data ukończenia zgłoszenia wystąpienia konferencji     |Date       |Equals, isBetween
DCN     |Nazwa wyświetlana wystąpienia konferencji  |Ciąg      |Brak
E | Rozszerzone metadane</br></br>**Ważne**: ten atrybut jest przestarzały i jest obsługiwany tylko w przypadku starszych aplikacji. Żądanie tego atrybutu pojedynczo (tj. Attributes = ID, TI, E) spowoduje zwrócenie wszystkich atrybutów rozszerzonych metadanych w *serializowanym ciągu JSON*</br></br>Wszystkie atrybuty zawarte w rozszerzonych metadanych są teraz dostępne jako atrybut najwyższego poziomu i mogą być żądane jako takie (tj. atrybuty = ID, TI, DOI, IA) | [Wydłuż](#extended) | Brak
ECC     |Łączna liczba cytatów dla wystąpienia konferencji |Int32      |Brak
FN | Pełna nazwa wystąpienia konferencji | Ciąg | Brak
Identyfikator      |Identyfikator jednostki                              |Int64      |Równa się
PC | Łączna liczba publikacji wystąpienia konferencji | Int32 | Brak
PCS.CN  |Nazwa nadrzędnej serii konferencji wystąpienia |Ciąg  |Równa się
PCs. Identyfikator |Identyfikator nadrzędnej serii konferencji wystąpienia |Int64     |Równa się

## <a name="extended"></a>Wydłuż

> [!IMPORTANT]
> Ten atrybut jest przestarzały i jest obsługiwany tylko w przypadku starszych aplikacji. Żądanie tego atrybutu pojedynczo (tj. Attributes = ID, TI, E) spowoduje zwrócenie wszystkich atrybutów rozszerzonych metadanych w *serializowanym ciągu JSON*</br></br>Wszystkie atrybuty zawarte w rozszerzonych metadanych są teraz dostępne jako atrybut najwyższego poziomu i mogą być żądane jako takie (tj. atrybuty = ID, TI, DOI, IA)

> [!IMPORTANT]
> Obsługa żądania pojedynczych atrybutów rozszerzonych przy użyciu "E" zakres, tj. "E. DN", jest przestarzały. Mimo że jest to technicznie obsługiwane, żądanie pojedynczych atrybutów rozszerzonych przy użyciu "E". zakres spowoduje zwrócenie wartości atrybutu w dwóch miejscach w odpowiedzi JSON jako część obiektu "E" i jako atrybut najwyższego poziomu.

Nazwa | Opis | Typ | Operacje
--- | --- | --- | ---
FN | Pełna nazwa wystąpienia konferencji | Ciąg | Brak
PC | Łączna liczba publikacji wystąpienia konferencji | Int32 | Brak
