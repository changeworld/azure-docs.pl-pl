---
title: Tworzenie atrybutów jednostki — Academic Knowledge API
titlesuffix: Azure Cognitive Services
description: Informacje o atrybutach, których można użyć z jednostką autor w Academic Knowledge API.
services: cognitive-services
author: darrine
manager: kuansanw
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: conceptual
ms.date: 11/14/2019
ms.author: darrine
ROBOTS: NOINDEX
ms.openlocfilehash: d5fc770c380397f605f8810fa41d3a8907f2358e
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/17/2019
ms.locfileid: "74146494"
---
# <a name="author-entity"></a>Tworzenie jednostki

> [!NOTE]
> Następujące atrybuty są specyficzne dla podmiotu autora. (Ty = ' 1 ')

Nazwa | Opis | Typ | Operacje
--- | --- | --- | ---
Identyfikator      | Identyfikator jednostki                             |Int64      |Równa się
AuN     | Nazwa zwyczajowa autora                    |Ciąg     |Równa się
CC      | Łączna liczba cytatów dla autora           |Int32      |Brak  
DAuN    | Nazwa wyświetlana autora                   |Ciąg     |Brak
E | Rozszerzone metadane</br></br>**Ważne**: ten atrybut jest przestarzały i jest obsługiwany tylko w przypadku starszych aplikacji. Żądanie tego atrybutu pojedynczo (tj. Attributes = ID, TI, E) spowoduje zwrócenie wszystkich atrybutów rozszerzonych metadanych w *serializowanym ciągu JSON*</br></br>Wszystkie atrybuty zawarte w rozszerzonych metadanych są teraz dostępne jako atrybut najwyższego poziomu i mogą być żądane jako takie (tj. atrybuty = ID, TI, DOI, IA) | [Wydłuż](#extended) | Brak
ECC     | Całkowita Szacowana Liczba cytatów dla autora |Int32      |Brak
LKA.AfId | Identyfikator jednostki dla ostatniej znanej przynależności dla autora | Int64 | Brak
LKA. AfN | Znormalizowana Nazwa ostatniej znanej przynależności dla autora | Ciąg | Brak
PC | Łączna liczba publikacji autora | Int32 | Brak

## <a name="extended"></a>Wydłuż

> [!IMPORTANT]
> Ten atrybut jest przestarzały i jest obsługiwany tylko w przypadku starszych aplikacji. Żądanie tego atrybutu pojedynczo (tj. Attributes = ID, TI, E) spowoduje zwrócenie wszystkich atrybutów rozszerzonych metadanych w *serializowanym ciągu JSON*</br></br>Wszystkie atrybuty zawarte w rozszerzonych metadanych są teraz dostępne jako atrybut najwyższego poziomu i mogą być żądane jako takie (tj. atrybuty = ID, TI, DOI, IA)

> [!IMPORTANT]
> Obsługa żądania pojedynczych atrybutów rozszerzonych przy użyciu "E" zakres, tj. "E. DN", jest przestarzały. Mimo że jest to technicznie obsługiwane, żądanie pojedynczych atrybutów rozszerzonych przy użyciu "E". zakres spowoduje zwrócenie wartości atrybutu w dwóch miejscach w odpowiedzi JSON jako część obiektu "E" i jako atrybut najwyższego poziomu.

Nazwa | Opis | Typ | Operacje
--- | --- | --- | ---
LKA.AfId | Identyfikator jednostki dla ostatniej znanej przynależności dla autora | Int64 | Brak
LKA. AfN | Znormalizowana Nazwa ostatniej znanej przynależności dla autora | Ciąg | Brak
PC | Łączna liczba publikacji autora | Int32 | Brak
