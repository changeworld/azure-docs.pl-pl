---
title: Dostosuj Kanał informacyjny dla użytkowników pulpitu wirtualnego Windows - Azure
description: Dostosowywanie źródła danych dla użytkowników pulpitu wirtualnego Windows za pomocą poleceń cmdlet programu PowerShell.
services: virtual-desktop
author: v-hevem
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 05/30/2019
ms.author: v-hevem
ms.openlocfilehash: 5fe2a8b8ee5870ff7986ca2d91739f82a5128882
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/07/2019
ms.locfileid: "67618995"
---
# <a name="customize-feed-for-windows-virtual-desktop-users"></a>Dostosowywanie kanału informacyjnego dla użytkowników usługi Windows Virtual Desktop

Źródło danych można dostosować tak, programów RemoteApp i zasoby usług pulpitu zdalnego są wyświetlane w sposób rozpoznawalny dla użytkowników.

Po pierwsze, [Pobierz i zaimportuj moduł programu PowerShell pulpitu wirtualnego Windows](https://docs.microsoft.com/powershell/windows-virtual-desktop/overview) do użycia w sesji programu PowerShell, jeśli jeszcze go.

## <a name="customize-the-display-name-for-a-remoteapp"></a>Dostosowywanie nazwy wyświetlanej usługi RemoteApp

Możesz zmienić nazwę wyświetlaną usługi RemoteApp opublikowanych przez ustawianie przyjaznej nazwy. Domyślnie przyjazna nazwa jest taka sama jak nazwa programu RemoteApp.

Uruchom następujące polecenie cmdlet programu PowerShell, aby pobrać listę opublikowanych RemoteApps dla grupy aplikacji:

```powershell
Get-RdsRemoteApp -TenantName <tenantname> -HostPoolName <hostpoolname> -AppGroupName <appgroupname>
```
![Zrzut ekranu przedstawiający polecenia cmdlet programu PowerShell Get-RDSRemoteApp przy użyciu nazwy i FriendlyName wyróżnione.](media/get-rdsremoteapp.png)

Aby przypisać przyjazną nazwę do programów RemoteApp, uruchom następujące polecenie cmdlet programu PowerShell:

```powershell
Set-RdsRemoteApp -TenantName <tenantname> -HostPoolName <hostpoolname> -AppGroupName <appgroupname> -Name <existingappname> -FriendlyName <newfriendlyname>
```
![Zrzut ekranu przedstawiający polecenia cmdlet programu PowerShell Set-RDSRemoteApp przy użyciu nazwy i nowego FriendlyName wyróżnione.](media/set-rdsremoteapp.png)

## <a name="customize-the-display-name-for-a-remote-desktop"></a>Dostosowywanie nazwy wyświetlanej za pomocą pulpitu zdalnego

Możesz zmienić nazwę wyświetlaną dla opublikowanych usług pulpitu zdalnego, ustawiając przyjazną nazwę. Jeśli ręcznie utworzono hosta, puli i grupy aplikacji klasycznej przy użyciu programu PowerShell, przyjazna nazwa domyślna to "Sesji pulpitu". Jeśli host został utworzony w puli i grupy aplikacji klasycznej przy użyciu szablonu usługi GitHub usługi Azure Resource Manager lub oferty w portalu Azure Marketplace, przyjazna nazwa domyślna jest taka sama jak nazwa hosta w puli.

Aby pobrać zasobu usług pulpitu zdalnego, uruchom następujące polecenia cmdlet programu PowerShell:

```powershell
Get-RdsRemoteDesktop -TenantName <tenantname> -HostPoolName <hostpoolname> -AppGroupName <appgroupname>
```
![Zrzut ekranu przedstawiający polecenia cmdlet programu PowerShell Get-RDSRemoteApp przy użyciu nazwy i FriendlyName wyróżnione.](media/get-rdsremotedesktop.png)

Aby przypisać przyjazną nazwę do zasobu usług pulpitu zdalnego, uruchom następujące polecenie cmdlet programu PowerShell:

```powershell
Set-RdsRemoteDesktop -TenantName <tenantname> -HostPoolName <hostpoolname> -AppGroupName <appgroupname> -FriendlyName <newfriendlyname>
```
![Zrzut ekranu przedstawiający polecenia cmdlet programu PowerShell Set-RDSRemoteApp przy użyciu nazwy i nowego FriendlyName wyróżnione.](media/set-rdsremotedesktop.png)

## <a name="next-steps"></a>Następne kroki

Teraz czy dostosowaniu źródła danych dla użytkowników, możesz zalogować się do klienta pulpitu wirtualnego Windows Aby przetestować działanie. Aby to zrobić, przejdź do nawiązywania połączenia z wirtualnego How-tos pulpitu Windows:
    
 * [Łączenie z systemem Windows 10 lub Windows 7](connect-windows-7-and-10.md)
 * [Łączenie z przeglądarki sieci web](connect-web.md) 
