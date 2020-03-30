---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: 28aab15dc67e051190e8d4e35e92240a56fe54a6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "67183179"
---
Następnie jeśli na serwerach klastra są uruchomione systemy Windows Server 2008 R2 lub Windows Server 2012, należy sprawdzić, czy poprawka [KB2854082](https://support.microsoft.com/kb/2854082) jest zainstalowana na każdym z serwerów lokalnych lub maszyn wirtualnych platformy Azure, które są częścią klastra. Każdy serwer lub maszyna wirtualna, który znajduje się w klastrze, ale nie w grupie dostępności, również powinien mieć zainstalowaną tę poprawkę.

W sesji pulpitu zdalnego dla każdego z węzłów klastra pobierz [plik KB2854082](https://support.microsoft.com/kb/2854082) do katalogu lokalnego. Następnie zainstaluj poprawkę w każdym węźle klastra sekwencyjnie. Jeśli usługa klastrowania jest aktualnie uruchomiona w węźle klastra, serwer zostanie ponownie uruchomiony po zakończeniu instalacji poprawki.

> [!WARNING]
> Zatrzymanie usługi klastrowania lub ponowne uruchomienie serwera wpływa na kondycję kworum klastra i grupy dostępności i może spowodować przejażenie klastra w trybie offline. Aby zachować wysoką dostępność klastra podczas instalacji, upewnij się, że:
> 
> * Klaster jest w optymalnym stanie zdrowia kworum. 
> * Przed zainstalowaniem poprawki w dowolnym węźle wszystkie węzły klastra są w trybie online.
> * Przed zainstalowaniem poprawki w dowolnym innym węźle w klastrze należy zezwolić na uruchomienie instalacji poprawki w celu ukończenia jednego węzła, w tym pełnego ponownego uruchomienia serwera.
> 
> 

