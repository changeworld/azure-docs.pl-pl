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
ms.openlocfilehash: c04875f92ee58c24567fbb4d73d8d01697810f99
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/15/2019
ms.locfileid: "72391728"
---
| Zasób | Cel | Limit sztywny |
|----------|--------------|------------|
| Usługi synchronizacji magazynu na region | 20 usług synchronizacji magazynu | Tak |
| Grupy synchronizacji na usługę synchronizacji magazynu | 100 grup synchronizacji | Tak |
| Zarejestrowane serwery na usługę synchronizacji magazynu | serwery 99 | Tak |
| Punkty końcowe chmury na grupę synchronizacji | 1 punkt końcowy w chmurze | Tak |
| Punkty końcowe serwera dla grupy synchronizacji | punkty końcowe serwera 50 | Nie |
| Punkty końcowe serwera na serwer | 30 punktów końcowych serwera | Tak |
| Obiekty systemu plików (Katalogi i pliki) dla każdej grupy synchronizacji | 50 000 000 obiektów | Nie |
| Maksymalna liczba obiektów systemu plików (katalogów i plików) w katalogu | 5 000 000 obiektów | Tak |
| Maksymalny rozmiar deskryptora zabezpieczeń obiektu (katalogów i plików) | 64 KiB | Tak |
| Rozmiar pliku | 100 GiB | Nie |
| Minimalny rozmiar pliku do warstwowego | 64 KiB | Tak |
| Współbieżne sesje synchronizacji | Wersja w wersji 4 Agent i nowsze: limit zależy od dostępnych zasobów systemowych. <BR> Agent v3: dwie aktywne sesje synchronizacji na procesor lub maksymalnie osiem aktywnych sesji synchronizacji na serwer. | Tak

> [!Note]  
> Punkt końcowy Azure File Sync może skalować w górę do rozmiaru udziału plików platformy Azure. Jeśli osiągnięto limit rozmiaru udziału plików platformy Azure, synchronizacja nie będzie mogła działać.
