---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: storage
author: wmgries
ms.service: storage
ms.topic: include
ms.date: 05/05/2019
ms.author: wgries
ms.custom: include file
ms.openlocfilehash: 2614c9290bf31813d59ee753a31622bccf0682b8
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/23/2019
ms.locfileid: "66114505"
---
| Resource | Obiekt docelowy | Stały limit |
|----------|--------------|------------|
| Usługi synchronizacji magazynu na region | 20 usługi synchronizacji magazynu | Tak |
| Grupy synchronizacji na usługę synchronizacji magazynu | 100 grupy synchronizacji | Tak |
| Zarejestrowane serwery na usługę synchronizacji magazynu | serwery 99 | Tak |
| Punkty końcowe w chmurze dla każdej grupy synchronizacji | punkt końcowy w chmurze 1 | Tak |
| Punkty końcowe serwera dla każdej grupy synchronizacji | 50 punkty końcowe serwera | Nie |
| Punkty końcowe serwera na serwer | 30 punkty końcowe serwera | Tak |
| Obiekty systemu (plików i katalogów) dla każdej grupy synchronizacji plików | 25 milionów obiektów | Nie |
| Maksymalna liczba obiektów systemu plików (pliki i katalogi) w katalogu | 1 mln obiektów | Tak |
| Maksymalna (pliki i katalogi) zabezpieczeń deskryptora rozmiar obiektu | 64 KiB | Tak |
| Rozmiar pliku | 100 GiB | Nie |
| Minimalny rozmiar pliku do się warstwy | 64 KiB | Tak |
| Synchronizacja jednoczesnych sesji | Agent w wersji 4 lub nowszy: Limit zależy od dostępnych zasobów systemowych. <BR> V3 agent: Dwie sesje ActiveSync na procesor lub maksymalnie osiem sesji ActiveSync na serwer. | Tak

> [!Note]  
> Punkt końcowy usługi Azure File Sync skalować w górę do rozmiaru udziału plików platformy Azure. W przypadku osiągnięcia limitu rozmiaru udziału plików platformy Azure, synchronizacji nie będzie działać.