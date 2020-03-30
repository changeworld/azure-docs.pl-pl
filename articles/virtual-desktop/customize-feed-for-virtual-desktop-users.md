---
title: Dostosowywanie kanału informacyjnego dla użytkowników pulpitu wirtualnego systemu Windows — Platforma Azure
description: Jak dostosować kanał informacyjny dla użytkowników pulpitu wirtualnego systemu Windows za pomocą poleceń cmdlet programu PowerShell.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 08/29/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 24a295d220cfaa7efe2fdc0d4eee53bb5c409708
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79128088"
---
# <a name="customize-feed-for-windows-virtual-desktop-users"></a>Dostosowywanie kanału informacyjnego dla użytkowników usługi Windows Virtual Desktop

Możesz dostosować kanał informacyjny, aby zasoby aplikacji RemoteApp i pulpitu zdalnego były wyświetlane w rozpoznawalny sposób dla użytkowników.

Najpierw [pobierz i zaimportuj moduł programu Windows Virtual Desktop PowerShell](/powershell/windows-virtual-desktop/overview/) do użycia w sesji programu PowerShell, jeśli jeszcze tego nie zrobiłeś. Następnie uruchom następujące polecenie cmdlet, aby zalogować się na swoje konto:

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

## <a name="customize-the-display-name-for-a-remoteapp"></a>Dostosowywanie nazwy wyświetlanej aplikacji RemoteApp

Nazwę wyświetlaną opublikowanego aplikacji RemoteApp można zmienić, ustawiając przyjazną nazwę. Domyślnie przyjazna nazwa jest taka sama jak nazwa programu RemoteApp.

Aby pobrać listę opublikowanych aplikacji RemoteApps dla grupy aplikacji, uruchom następujące polecenie cmdlet programu PowerShell:

```powershell
Get-RdsRemoteApp -TenantName <tenantname> -HostPoolName <hostpoolname> -AppGroupName <appgroupname>
```
![Zrzut ekranu przedstawiający polecenie cmdlet programu PowerShell Get-RDSRemoteApp z wyróżnioną podświetleną nazwą i nazwą przyjazną.](media/get-rdsremoteapp.png)

Aby przypisać przyjazną nazwę do aplikacji RemoteApp, uruchom następujące polecenie cmdlet programu PowerShell:

```powershell
Set-RdsRemoteApp -TenantName <tenantname> -HostPoolName <hostpoolname> -AppGroupName <appgroupname> -Name <existingappname> -FriendlyName <newfriendlyname>
```
![Zrzut ekranu przedstawiający zestaw poleceń cmdlet programu PowerShell Set-RDSRemoteApp z wyróżnioną nazwą i nową nazwą przyjazną.](media/set-rdsremoteapp.png)

## <a name="customize-the-display-name-for-a-remote-desktop"></a>Dostosowywanie nazwy wyświetlanej pulpitu zdalnego

Nazwę wyświetlaną opublikowanego pulpitu zdalnego można zmienić, ustawiając przyjazną nazwę. Jeśli ręcznie utworzono grupę aplikacji puli hostów i aplikacji pulpitu za pośrednictwem programu PowerShell, domyślną przyjazną nazwą jest "Pulpit sesji". Jeśli utworzono grupę aplikacji puli hostów i aplikacji klasycznych za pomocą szablonu Usługi Azure Resource Manager lub oferty usługi Azure Marketplace, domyślna przyjazna nazwa jest taka sama jak nazwa puli hosta.

Aby pobrać zasób pulpitu zdalnego, uruchom następujące polecenie cmdlet programu PowerShell:

```powershell
Get-RdsRemoteDesktop -TenantName <tenantname> -HostPoolName <hostpoolname> -AppGroupName <appgroupname>
```
![Zrzut ekranu przedstawiający polecenie cmdlet programu PowerShell Get-RDSRemoteApp z wyróżnioną podświetleną nazwą i nazwą przyjazną.](media/get-rdsremotedesktop.png)

Aby przypisać przyjazną nazwę do zasobu pulpitu zdalnego, uruchom następujące polecenie cmdlet programu PowerShell:

```powershell
Set-RdsRemoteDesktop -TenantName <tenantname> -HostPoolName <hostpoolname> -AppGroupName <appgroupname> -FriendlyName <newfriendlyname>
```
![Zrzut ekranu przedstawiający zestaw poleceń cmdlet programu PowerShell Set-RDSRemoteApp z wyróżnioną nazwą i nową nazwą przyjazną.](media/set-rdsremotedesktop.png)

## <a name="next-steps"></a>Następne kroki

Teraz, gdy dostosowano kanał informacyjny dla użytkowników, możesz zalogować się do klienta pulpitu wirtualnego systemu Windows, aby go przetestować. Aby to zrobić, przejdź do funkcji podłączanie do pulpitu wirtualnego systemu Windows:
    
 * [Łączenie z systemu Windows 10 lub Windows 7](connect-windows-7-and-10.md)
 * [Łączenie z przeglądarki internetowej](connect-web.md) 
