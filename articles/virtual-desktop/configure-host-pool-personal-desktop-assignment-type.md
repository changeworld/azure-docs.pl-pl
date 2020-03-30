---
title: Typ przypisania pulpitu osobistego pulpitu pulpitu wirtualnego systemu Windows — Platforma Azure
description: Jak skonfigurować typ przypisania dla puli hostów pulpitu osobistego pulpitu wirtualnego systemu Windows.
services: virtual-desktop
author: HeidiLohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 12/10/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 41b24a94d36b21fe5d5f539e056abb535bda433a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79128291"
---
# <a name="configure-the-personal-desktop-host-pool-assignment-type"></a>Konfigurowanie typu przydziału puli hostów pulpitu osobistego

Typ przydziału puli hostów pulpitu osobistego można skonfigurować, aby dostosować środowisko pulpitu wirtualnego systemu Windows do własnych potrzeb. W tym temacie pokażemy Ci, jak skonfigurować automatyczne lub bezpośrednie przypisanie dla użytkowników.

>[!NOTE]
> Instrukcje opisane w tym artykule dotyczą tylko pul hostów pulpitu osobistego, a nie puli hostów zbiorczych, ponieważ użytkownicy w puli hostów nie są przypisywani do określonych hostów sesji.

## <a name="configure-automatic-assignment"></a>Konfigurowanie automatycznego przypisywania

Automatyczne przypisywanie jest domyślnym typem przypisania dla nowych pul hostów pulpitu osobistego utworzonych w środowisku pulpitu wirtualnego systemu Windows. Automatyczne przypisywanie użytkowników nie wymaga określonego hosta sesji.

Aby automatycznie przypisać użytkowników, najpierw przypisz ich do puli hostów pulpitu osobistego, aby mogli zobaczyć pulpit w pliku danych. Gdy przypisany użytkownik uruchomi pulpit w swoim kanale, będzie ubiegać się o dostępnego hosta sesji, jeśli nie został jeszcze połączony z pulą hosta, która kończy proces przypisywania.

Przed rozpoczęciem [pobierania i importowania modułu programu Windows Virtual Desktop PowerShell,](/powershell/windows-virtual-desktop/overview/) jeśli jeszcze tego nie zrobiłeś. 

> [!NOTE]
> Przed postępujeniem zgodnie z tymi instrukcjami upewnij się, że zainstalowano moduł programu Windows Virtual Desktop PowerShell w wersji 1.0.1534.2001 lub nowszej.

Następnie uruchom następujące polecenie cmdlet, aby zalogować się na swoje konto:

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

Aby skonfigurować pulę hostów do automatycznego przypisywania użytkowników do maszyn wirtualnych, uruchom następujące polecenie cmdlet programu PowerShell:

```powershell
Set-RdsHostPool <tenantname> <hostpoolname> -AssignmentType Automatic
```

Aby przypisać użytkownika do puli hostów pulpitu osobistego, uruchom następujące polecenie cmdlet programu PowerShell:

```powershell
Add-RdsAppGroupUser <tenantname> <hostpoolname> "Desktop Application Group" -UserPrincipalName <userupn>
```

## <a name="configure-direct-assignment"></a>Konfigurowanie przypisania bezpośredniego

W przeciwieństwie do automatycznego przypisywania, gdy używasz przypisania bezpośredniego, należy przypisać użytkownika zarówno do puli hostów pulpitu osobistego, jak i do określonego hosta sesji, aby mógł połączyć się z pulpitem osobistym. Jeśli użytkownik jest przypisany tylko do puli hosta bez przypisania hosta sesji, nie będzie mógł uzyskać dostępu do zasobów.

Aby skonfigurować pulę hostów w taki sposób, aby wymagała bezpośredniego przypisania użytkowników do hostów sesji, uruchom następujące polecenie cmdlet programu PowerShell:

```powershell
Set-RdsHostPool <tenantname> <hostpoolname> -AssignmentType Direct
```

Aby przypisać użytkownika do puli hostów pulpitu osobistego, uruchom następujące polecenie cmdlet programu PowerShell:

```powershell
Add-RdsAppGroupUser <tenantname> <hostpoolname> "Desktop Application Group" -UserPrincipalName <userupn>
```

Aby przypisać użytkownika do określonego hosta sesji, uruchom następujące polecenie cmdlet programu PowerShell:

```powershell
Set-RdsSessionHost <tenantname> <hostpoolname> -Name <sessionhostname> -AssignedUser <userupn>
```

## <a name="next-steps"></a>Następne kroki

Po skonfigurowaniu typu przypisania pulpitu osobistego można zalogować się do klienta pulpitu wirtualnego systemu Windows, aby przetestować go w ramach sesji użytkownika. Te dwa następne instrukcje powiedzą Ci, jak połączyć się z sesją przy użyciu wybranego klienta:

- [Łączenie się z klientem klasycznym systemu Windows](connect-windows-7-and-10.md)
- [Łączenie się z klientem internetowym](connect-web.md)
