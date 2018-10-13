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
ms.openlocfilehash: a29f1c4a625552dd958884c6a172bee470e61ca6
ms.sourcegitcommit: c282021dbc3815aac9f46b6b89c7131659461e49
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/12/2018
ms.locfileid: "49312507"
---
| Zasób | Środowisko docelowe | Stały limit |
|----------|--------------|------------|
| Usługi synchronizacji magazynu na subskrypcję | 15 usługi synchronizacji magazynu | Nie |
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
| Synchronizacja jednoczesnych sesji | 2 sesji ActiveSync na procesora lub maksymalnej 8 sesji ActiveSync na serwerze | Yes |
