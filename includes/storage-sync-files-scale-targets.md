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
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/18/2019
ms.locfileid: "67183438"
---
| Resource | Cel | Stały limit |
|----------|--------------|------------|
| Usługi synchronizacji magazynu na region | 20 usługi synchronizacji magazynu | Tak |
| Grupy synchronizacji na usługę synchronizacji magazynu | 100 grupy synchronizacji | Yes |
| Zarejestrowane serwery na usługę synchronizacji magazynu | serwery 99 | Tak |
| Punkty końcowe w chmurze dla każdej grupy synchronizacji | punkt końcowy w chmurze 1 | Tak |
| Punkty końcowe serwera dla każdej grupy synchronizacji | 50 punkty końcowe serwera | Nie |
| Punkty końcowe serwera na serwer | 30 punkty końcowe serwera | Tak |
| Obiekty systemu (plików i katalogów) dla każdej grupy synchronizacji plików | 25 milionów obiektów | Nie |
| Maksymalna liczba obiektów systemu plików (pliki i katalogi) w katalogu | 1 mln obiektów | Tak |
| Maksymalna (pliki i katalogi) zabezpieczeń deskryptora rozmiar obiektu | 64 KiB | Yes |
| Rozmiar pliku | 100 GiB | Nie |
| Minimalny rozmiar pliku do się warstwy | 64 KiB | Tak |
| Synchronizacja jednoczesnych sesji | Agent w wersji 4 lub nowszy: Limit zależy od dostępnych zasobów systemowych. <BR> V3 agent: Dwie sesje ActiveSync na procesor lub maksymalnie osiem sesji ActiveSync na serwer. | Tak

> [!Note]  
> Punkt końcowy usługi Azure File Sync skalować w górę do rozmiaru udziału plików platformy Azure. W przypadku osiągnięcia limitu rozmiaru udziału plików platformy Azure, synchronizacji nie będzie działać.