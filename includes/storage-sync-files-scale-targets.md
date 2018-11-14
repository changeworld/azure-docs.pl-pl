---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: storage
author: wmgries
ms.service: storage
ms.topic: include
ms.date: 07/18/2018
ms.author: wgries
ms.custom: include file
ms.openlocfilehash: 03fe587ede297ac7dea90b7a5fb2d5323f60659e
ms.sourcegitcommit: 1f9e1c563245f2a6dcc40ff398d20510dd88fd92
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/14/2018
ms.locfileid: "51628182"
---
| Zasób | Środowisko docelowe | Stały limit |
|----------|--------------|------------|
| Usługi synchronizacji magazynu na subskrypcję | 15 usługi synchronizacji magazynu na region | Nie |
| Grupy synchronizacji na usługę synchronizacji magazynu | 100 grupy synchronizacji | Yes |
| Zarejestrowane serwery na usługę synchronizacji magazynu | serwery 99 | Yes |
| Punkty końcowe w chmurze dla każdej grupy synchronizacji | punkt końcowy w chmurze 1 | Yes |
| Punkty końcowe serwera dla każdej grupy synchronizacji | 50 punkty końcowe serwera | Nie |
| Punkty końcowe serwera na serwer | punkty końcowe serwera 33-99 | Tak, ale różnią się w zależności od konfiguracji (procesor CPU, pamięci, woluminy, postęp dokonany w pliku, liczba plików itp.) |
| Rozmiar końcowy | 4 TiB | Nie |
| Obiekty systemu (plików i katalogów) dla każdej grupy synchronizacji plików | 25 milionów obiektów | Nie |
| Maksymalna liczba obiektów systemu plików (pliki i katalogi) w katalogu | 200 000 obiektów. | Yes |
| Obiekt maksymalna długość nazwy (pliki i katalogi) | 255 znaków | Yes |
| Maksymalna (pliki i katalogi) zabezpieczeń deskryptora rozmiar obiektu | 4 KiB | Yes |
| Rozmiar pliku | 100 GiB | Nie |
| Minimalny rozmiar pliku do się warstwy | 64 KiB | Yes |
| Synchronizacja jednoczesnych sesji | Agent w wersji 4: Limit różni się w zależności od dostępnych zasobów systemowych. <BR> Agent w wersji 3: 2 sesji ActiveSync na procesora lub maksymalnej liczby 8 active synchronizacji sesji na serwerze | Yes
