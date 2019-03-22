---
title: Konfigurowanie pulpitu wirtualnego Windows metody równoważenia obciążenia (wersja zapoznawcza) — Azure
description: Jak skonfigurować metodę równoważenia obciążenia dla środowiska wirtualnego pulpitu Windows.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 03/21/2019
ms.author: helohr
ms.openlocfilehash: 10a1066b85b16749fe95e373e696d486b0e7bafa
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/21/2019
ms.locfileid: "58318347"
---
# <a name="configure-the-windows-virtual-desktop-load-balancing-method"></a>Konfigurowanie metody równoważenia obciążenia wirtualnego pulpitu Windows

Konfigurowanie metody równoważenia obciążenia dla puli hosta pozwala na dostosowanie środowiska pulpitu wirtualnego Windows (wersja zapoznawcza), aby lepiej spełniane były Twoich potrzeb.

>[!NOTE]
> To nie ma zastosowania do puli trwałego hosta pulpitu ponieważ użytkownicy mają zawsze mapowanie 1:1 na hoście sesji w ramach puli hosta.

## <a name="configure-breadth-first-load-balancing"></a>Konfigurowanie równoważenia obciążenia szerokość pierwszej

Równoważenie obciążenia szerokość pierwszej jest konfigurację domyślną dla nowych pul nietrwałe hosta. Równoważenie obciążenia szeroki zakres pierwsze rozdziela nowych sesji użytkownika na wszystkich hostach dostępnych sesji w puli hosta. Podczas konfigurowania równoważenia obciążenia szerokość pierwszej, może być Ustaw limit maksymalny czas trwania sesji na hoście sesji w puli hosta.

Po pierwsze, [Pobierz i zaimportuj moduł programu PowerShell pulpitu wirtualnego Windows](https://docs.microsoft.com/powershell/windows-virtual-desktop/overview) do użycia w sesji programu PowerShell, jeśli jeszcze go.

Aby skonfigurować pulę hosta, aby wykonać szerokość pierwszej Równoważenie obciążenia bez dostosowując limit maksymalny czas trwania sesji, uruchom następujące polecenie cmdlet programu PowerShell:

```powershell
Set-RdsHostPool <tenantname> <hostpoolname> -BreadthFirstLoadBalancer
```

Aby skonfigurować pulę hosta do wykonywania równoważenia obciążenia szerokość pierwszej i wykorzystywania limit maksymalny czas trwania sesji nowe, uruchom następujące polecenie cmdlet programu PowerShell:

```powershell
Set-RdsHostPool <tenantname> <hostpoolname> -BreadthFirstLoadBalancer -MaxSessionLimit ###
```

## <a name="configure-depth-first-load-balancing"></a>Konfigurowanie równoważenia obciążenia pierwszego głębokości

Równoważenie obciążenia pierwszego głębokość dystrybuuje nowych sesji użytkownika do hosta z najwyższą liczbą połączeń dostępnych sesji, ale jeszcze nie przetworzył przekroczyła wartość progową limit maksymalny czas trwania sesji. Podczas konfigurowania równoważenia obciążenia pierwszego głębi, można **musi** Ustaw limit maksymalny czas trwania sesji na hoście sesji w puli hosta.

Aby skonfigurować pulę hosta do wykonania, równoważenie obciążenia pierwszego głębi, uruchom następujące polecenie cmdlet programu PowerShell:

```powershell
Set-RdsHostPool <tenantname> <hostpoolname> -DepthFirstLoadBalancer -MaxSessionLimit ###
```
