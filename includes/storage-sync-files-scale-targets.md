---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 05/05/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 6a053b94813145f9ccd69158d18edb728d5dad61
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "74795958"
---
| Zasób | Środowisko docelowe | Twardy limit |
|----------|--------------|------------|
| Usługi synchronizacji magazynu na region | 20 Usługi synchronizacji pamięci masowej | Tak |
| Synchronizacja grup na usługę synchronizacji magazynu | 100 grup synchronizacji | Tak |
| Zarejestrowane serwery na usługę synchronizacji magazynu | 99 serwerów | Tak |
| Punkty końcowe chmury na grupę synchronizacji | 1 punkt końcowy chmury | Tak |
| Punkty końcowe serwera na grupę synchronizacji | 50 punktów końcowych serwera | Nie |
| Punkty końcowe serwera na serwer | 30 punktów końcowych serwera | Tak |
| Obiekty systemu plików (katalogi i pliki) na grupę synchronizacji | 100 milionów obiektów | Nie |
| Maksymalna liczba obiektów systemu plików (katalogów i plików) w katalogu | 5 milionów obiektów | Tak |
| Maksymalny rozmiar deskryptora zabezpieczeń obiektu (katalogów i plików) | 64 k.k. | Tak |
| Rozmiar pliku | 100 Gib | Nie |
| Minimalny rozmiar pliku, który ma być warstwowy | V9: Na podstawie rozmiaru klastra systemu plików (rozmiar klastra z podwójnym systemem plików). Na przykład jeśli rozmiar klastra systemu plików wynosi 4 kb, minimalny rozmiar pliku będzie 8kb.<br> V8 i starsze: 64 KiB  | Tak |

> [!Note]  
> Punkt końcowy usługi Azure File Sync można skalować do rozmiaru udziału plików platformy Azure. Jeśli zostanie osiągnięty limit rozmiaru udziału plików platformy Azure, synchronizacja nie będzie mogła działać.
