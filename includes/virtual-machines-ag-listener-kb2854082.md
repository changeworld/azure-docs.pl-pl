---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: d656d756759c997972eb034e194355185be93e1a
ms.sourcegitcommit: 6e09760197a91be564ad60ffd3d6f48a241e083b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/29/2018
ms.locfileid: "50227175"
---
Następnie, jeśli wszystkie serwery w klastrze są uruchomione w systemie Windows Server 2008 R2 lub Windows Server 2012, należy sprawdzić, czy poprawka [KB2854082](http://support.microsoft.com/kb/2854082) jest zainstalowany na każdym z serwerów lokalnych lub maszyn wirtualnych platformy Azure, które są częścią klastra. Dowolny serwer lub maszynę Wirtualną, która jest w klastrze, ale nie w grupie dostępności powinny mieć również po zainstalowaniu tej poprawki.

W sesji usług pulpitu zdalnego dla każdego z węzłów klastra, Pobierz [KB2854082](http://support.microsoft.com/kb/2854082) do katalogu lokalnego. Następnie należy zainstalować poprawkę w każdym węźle klastra po kolei. Jeśli usługa klastrowania jest aktualnie uruchomione w węźle klastra, ponownym uruchomieniu serwera po zakończeniu instalacji poprawki.

> [!WARNING]
> Zatrzymanie usługi klastrowania lub ponowne uruchomienie serwera wpływa na kondycji kworum klastra i grupy dostępności i może dojść do klastra, aby przejść do trybu offline. Aby zapewnić wysoką dostępność klastra podczas instalacji, upewnij się, że:
> 
> * Klaster znajduje się w optymalny kworum kondycji. 
> * Przed zainstalowaniem poprawki na dowolnym węźle wszystkie węzły klastra są w trybie online.
> * Przed zainstalowaniem poprawki na jednym z węzłów w klastrze, Zezwalaj na instalację poprawkę do uruchomienia do ukończenia na jednym węźle, w tym w pełni ponowne uruchomienie serwera.
> 
> 

