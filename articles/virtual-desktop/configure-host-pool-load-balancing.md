---
title: Konfigurowanie równoważenia obciążenia pulpitu wirtualnego systemu Windows — Platforma Azure
description: Jak skonfigurować metodę równoważenia obciążenia dla środowiska pulpitu wirtualnego systemu Windows.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 08/29/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 5d8670994791e360f5e3b30b90b4bea5d55464b5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79128308"
---
# <a name="configure-the-windows-virtual-desktop-load-balancing-method"></a>Konfigurowanie metody równoważenia obciążenia usługi Windows Virtual Desktop

Skonfigurowanie metody równoważenia obciążenia dla puli hostów umożliwia dostosowanie środowiska pulpitu wirtualnego systemu Windows do własnych potrzeb.

>[!NOTE]
> Nie dotyczy to trwałej puli hostów pulpitu, ponieważ użytkownicy zawsze mają mapowanie 1:1 do hosta sesji w puli hostów.

## <a name="configure-breadth-first-load-balancing"></a>Konfigurowanie równoważenia obciążenia pierwszego w zakresie szerokości

Równoważenie obciążenia po raz pierwszy jest domyślną konfiguracją dla nowych pul hostów nietrwałych. Równoważenie obciążenia o wysokiej szerokości rozdziela nowe sesje użytkowników we wszystkich dostępnych hostach sesji w puli hostów. Podczas konfigurowania równoważenia obciążenia pierwszego szerokości można ustawić maksymalny limit sesji na host sesji w puli hostów.

Najpierw [pobierz i zaimportuj moduł programu Windows Virtual Desktop PowerShell](/powershell/windows-virtual-desktop/overview/) do użycia w sesji programu PowerShell, jeśli jeszcze tego nie zrobiłeś. Następnie uruchom następujące polecenie cmdlet, aby zalogować się na swoje konto:

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

Aby skonfigurować pulę hostów do równoważenia obciążenia pierwszego zakresu bez dostosowywania maksymalnego limitu sesji, uruchom następujące polecenie cmdlet programu PowerShell:

```powershell
Set-RdsHostPool <tenantname> <hostpoolname> -BreadthFirstLoadBalancer
```

Aby skonfigurować pulę hostów do równoważenia obciążenia pierwszego szerokości i użycia nowego maksymalnego limitu sesji, uruchom następujące polecenie cmdlet programu PowerShell:

```powershell
Set-RdsHostPool <tenantname> <hostpoolname> -BreadthFirstLoadBalancer -MaxSessionLimit ###
```

## <a name="configure-depth-first-load-balancing"></a>Konfigurowanie równoważenia obciążenia z pierwszej głębokości

Równoważenie obciążenia typu głębokość po pierwsze rozdziela nowe sesje użytkownika do dostępnego hosta sesji z największą liczbą połączeń, ale nie osiągnął maksymalnego progu limitu sesji. Podczas konfigurowania równoważenia obciążenia najpierw głębokość, **należy** ustawić maksymalny limit sesji na hosta sesji w puli hosta.

Aby skonfigurować pulę hostów do równoważenia obciążenia po pierwsze głębokości, uruchom następujące polecenie cmdlet programu PowerShell:

```powershell
Set-RdsHostPool <tenantname> <hostpoolname> -DepthFirstLoadBalancer -MaxSessionLimit ###
```
