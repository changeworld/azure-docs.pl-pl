---
title: Atrybuty jednostki przynależności do Academic Knowledge API
titlesuffix: Azure Cognitive Services
description: Informacje o atrybutach, których można używać z jednostką przynależności w Academic Knowledge API.
services: cognitive-services
author: darrine
manager: kuansanw
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: conceptual
ms.date: 11/14/2019
ms.author: darrine
ROBOTS: NOINDEX
ms.openlocfilehash: 52dcccebe1a1cbab7a6afadeb6b543b34b8b1eb7
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/17/2019
ms.locfileid: "74143921"
---
# <a name="affiliation-entity"></a>Jednostka przynależności

> [!NOTE]
> Następujące atrybuty są specyficzne dla jednostki przynależności. (Ty = "5")

Nazwa | Opis | Typ | Operacje
--- | --- | --- | ---
AfN | Nazwa nadana przynależności |Ciąg |Równa się
CC | Łączna liczba cytatów przynależności |Int32        |brak  
DAfN | Nazwa wyświetlana przynależności |Ciąg |brak
E | Rozszerzone metadane</br></br>**Ważne**: ten atrybut jest przestarzały i jest obsługiwany tylko w przypadku starszych aplikacji. Żądanie tego atrybutu pojedynczo (tj. Attributes = ID, TI, E) spowoduje zwrócenie wszystkich atrybutów rozszerzonych metadanych w *serializowanym ciągu JSON*</br></br>Wszystkie atrybuty zawarte w rozszerzonych metadanych są teraz dostępne jako atrybut najwyższego poziomu i mogą być żądane jako takie (tj. atrybuty = ID, TI, DOI, IA) | [Wydłuż](#extended) | Brak
ECC | Łączna liczba cytatów dla przynależności |Int32 |brak
Identyfikator | Identyfikator jednostki |Int64 |Równa się
PC | Łączna liczba publikacji utworzonych w ramach przynależności do tej jednostki | Int32 | Brak

## <a name="extended"></a>Wydłuż

> [!IMPORTANT]
> Ten atrybut jest przestarzały i jest obsługiwany tylko w przypadku starszych aplikacji. Żądanie tego atrybutu pojedynczo (tj. Attributes = ID, TI, E) spowoduje zwrócenie wszystkich atrybutów rozszerzonych metadanych w *serializowanym ciągu JSON*</br></br>Wszystkie atrybuty zawarte w rozszerzonych metadanych są teraz dostępne jako atrybut najwyższego poziomu i mogą być żądane jako takie (tj. atrybuty = ID, TI, DOI, IA)

> [!IMPORTANT]
> Obsługa żądania pojedynczych atrybutów rozszerzonych przy użyciu "E" zakres, tj. "E. DN", jest przestarzały. Mimo że jest to technicznie obsługiwane, żądanie pojedynczych atrybutów rozszerzonych przy użyciu "E". zakres spowoduje zwrócenie wartości atrybutu w dwóch miejscach w odpowiedzi JSON jako część obiektu "E" i jako atrybut najwyższego poziomu.

Nazwa | Opis | Typ | Operacje
--- | --- | --- | ---
PC | Łączna liczba publikacji utworzonych w ramach przynależności do tej jednostki | Int32 | Brak
