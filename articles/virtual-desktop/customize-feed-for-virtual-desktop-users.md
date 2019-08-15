---
title: Dostosowywanie kanału informacyjnego dla użytkowników pulpitu wirtualnego systemu Windows — Azure
description: Jak dostosować źródło danych dla użytkowników pulpitu wirtualnego systemu Windows za pomocą poleceń cmdlet programu PowerShell.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 05/30/2019
ms.author: helohr
ms.openlocfilehash: 40fd863d9ad92b91b8e691fbb7be233e16b82c17
ms.sourcegitcommit: 78ebf29ee6be84b415c558f43d34cbe1bcc0b38a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/12/2019
ms.locfileid: "68950747"
---
# <a name="customize-feed-for-windows-virtual-desktop-users"></a>Dostosowywanie kanału informacyjnego dla użytkowników usługi Windows Virtual Desktop

Możesz dostosować źródło danych, tak aby zasoby usługi RemoteApp i pulpitu zdalnego pojawiały się w rozpoznawanym sposobie dla użytkowników.

Najpierw [Pobierz i zaimportuj moduł programu PowerShell dla pulpitu wirtualnego systemu Windows](https://docs.microsoft.com/powershell/windows-virtual-desktop/overview) , który ma być używany w sesji programu PowerShell, jeśli jeszcze tego nie zrobiono.

## <a name="customize-the-display-name-for-a-remoteapp"></a>Dostosowywanie nazwy wyświetlanej dla usługi RemoteApp

Można zmienić nazwę wyświetlaną opublikowanych programów RemoteApp, ustawiając przyjazną nazwę. Domyślnie przyjazna nazwa jest taka sama jak nazwa programu RemoteApp.

Aby pobrać listę opublikowanych programów RemoteApp dla grupy aplikacji, uruchom następujące polecenie cmdlet programu PowerShell:

```powershell
Get-RdsRemoteApp -TenantName <tenantname> -HostPoolName <hostpoolname> -AppGroupName <appgroupname>
```
![Zrzut ekranu poleceń cmdlet programu PowerShell Get-RDSRemoteApp z wyróżnioną nazwą i przyjaznymi nazwami.](media/get-rdsremoteapp.png)

Aby przypisać przyjazną nazwę do usługi RemoteApp, uruchom następujące polecenie cmdlet programu PowerShell:

```powershell
Set-RdsRemoteApp -TenantName <tenantname> -HostPoolName <hostpoolname> -AppGroupName <appgroupname> -Name <existingappname> -FriendlyName <newfriendlyname>
```
![Zrzut ekranu poleceń cmdlet programu PowerShell Set-RDSRemoteApp o nazwie i nowej FriendlyName wyróżniony.](media/set-rdsremoteapp.png)

## <a name="customize-the-display-name-for-a-remote-desktop"></a>Dostosuj nazwę wyświetlaną dla Pulpit zdalny

Można zmienić nazwę wyświetlaną opublikowanego pulpitu zdalnego, ustawiając przyjazną nazwę. Jeśli utworzono ręcznie pulę hostów i grupę aplikacji klasycznych za pomocą programu PowerShell, domyślną przyjazną nazwą będzie "sesja pulpitu". Jeśli utworzono pulę hostów i grupę aplikacji klasycznych za pomocą szablonu Azure Resource Manager GitHub lub oferty portalu Azure Marketplace, domyślna przyjazna nazwa jest taka sama jak nazwa puli hostów.

Aby pobrać zasób pulpitu zdalnego, uruchom następujące polecenie cmdlet programu PowerShell:

```powershell
Get-RdsRemoteDesktop -TenantName <tenantname> -HostPoolName <hostpoolname> -AppGroupName <appgroupname>
```
![Zrzut ekranu poleceń cmdlet programu PowerShell Get-RDSRemoteApp z wyróżnioną nazwą i przyjaznymi nazwami.](media/get-rdsremotedesktop.png)

Aby przypisać przyjazną nazwę do zasobu pulpitu zdalnego, uruchom następujące polecenie cmdlet programu PowerShell:

```powershell
Set-RdsRemoteDesktop -TenantName <tenantname> -HostPoolName <hostpoolname> -AppGroupName <appgroupname> -FriendlyName <newfriendlyname>
```
![Zrzut ekranu poleceń cmdlet programu PowerShell Set-RDSRemoteApp o nazwie i nowej FriendlyName wyróżniony.](media/set-rdsremotedesktop.png)

## <a name="next-steps"></a>Następne kroki

Teraz, po dostosowaniu źródła danych dla użytkowników, możesz zalogować się do klienta pulpitu wirtualnego systemu Windows w celu przetestowania go. Aby to zrobić, przejdź do programu Windows Virtual Desktop how-Toss:
    
 * [Łączenie z systemem Windows 10 lub Windows 7](connect-windows-7-and-10.md)
 * [Nawiązywanie połączenia z przeglądarki sieci Web](connect-web.md) 
