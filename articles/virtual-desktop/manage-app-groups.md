---
title: Zarządzanie grupami aplikacji na potrzeby wersji zapoznawczej pulpitu wirtualnego systemu Windows — Azure
description: Opisuje sposób konfigurowania dzierżawców dla systemu Windows Virtual Desktop w wersji zapoznawczej w Azure Active Directory.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: tutorial
ms.date: 08/29/2019
ms.author: helohr
ms.openlocfilehash: 2bec7e490443727fa294e7be9412bb20ae66e691
ms.sourcegitcommit: 19a821fc95da830437873d9d8e6626ffc5e0e9d6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/29/2019
ms.locfileid: "70163256"
---
# <a name="tutorial-manage-app-groups-for-windows-virtual-desktop-preview"></a>Samouczek: Zarządzanie grupami aplikacji na potrzeby wersji zapoznawczej pulpitu wirtualnego systemu Windows

Domyślna grupa aplikacji utworzona dla nowej puli hostów systemu Windows Virtual Desktop w wersji zapoznawczej również publikuje pełny pulpit. Ponadto można utworzyć co najmniej jedną grupę aplikacji RemoteApp dla puli hostów. Postępuj zgodnie z tym samouczkiem, aby utworzyć grupę aplikacji RemoteApp i opublikować poszczególne aplikacje menu **Start** .

W tym samouczku pokazano, jak:

> [!div class="checklist"]
> * Utwórz grupę usługi RemoteApp.
> * Udzielanie dostępu do programów RemoteApp.

Przed rozpoczęciem [Pobierz i zaimportuj moduł programu PowerShell dla pulpitu wirtualnego systemu Windows](https://docs.microsoft.com/powershell/windows-virtual-desktop/overview) , który ma być używany w sesji programu PowerShell, jeśli jeszcze tego nie zrobiono. Następnie uruchom następujące polecenie cmdlet, aby zalogować się do konta:

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

## <a name="create-a-remoteapp-group"></a>Tworzenie grupy usługi RemoteApp

1. Uruchom następujące polecenie cmdlet programu PowerShell, aby utworzyć nową pustą grupę aplikacji RemoteApp.

   ```powershell
   New-RdsAppGroup <tenantname> <hostpoolname> <appgroupname> -ResourceType "RemoteApp"
   ```

2. Obowiązkowe Aby sprawdzić, czy grupa aplikacji została utworzona, możesz uruchomić następujące polecenie cmdlet, aby wyświetlić listę wszystkich grup aplikacji dla puli hostów.

   ```powershell
   Get-RdsAppGroup <tenantname> <hostpoolname>
   ```

3. Uruchom następujące polecenie cmdlet, aby uzyskać listę aplikacji menu **Start** w obrazie maszyny wirtualnej puli hostów. Zapisz wartości **FilePath**, **IconPath**, **IconIndex**i inne ważne informacje dla aplikacji, którą chcesz opublikować.

   ```powershell
   Get-RdsStartMenuApp <tenantname> <hostpoolname> <appgroupname>
   ```
   
4. Uruchom następujące polecenie cmdlet, aby zainstalować aplikację opartą `AppAlias`na systemie. `AppAlias`staną się widoczne po uruchomieniu danych wyjściowych z kroku 3.

   ```powershell
   New-RdsRemoteApp <tenantname> <hostpoolname> <appgroupname> -Name <remoteappname> -AppAlias <appalias>
   ```

5. Obowiązkowe Uruchom następujące polecenie cmdlet, aby opublikować nowy Program RemoteApp w grupie aplikacji utworzonej w kroku 1.

   ```powershell
   New-RdsRemoteApp <tenantname> <hostpoolname> <appgroupname> -Name <remoteappname> -Filepath <filepath>  -IconPath <iconpath> -IconIndex <iconindex>
   ```

6. Aby sprawdzić, czy aplikacja została opublikowana, uruchom następujące polecenie cmdlet.

   ```powershell
   Get-RdsRemoteApp <tenantname> <hostpoolname> <appgroupname>
   ```

7. Powtórz kroki od 1 do 5 dla każdej aplikacji, która ma zostać opublikowana dla tej grupy aplikacji.
8. Uruchom następujące polecenie cmdlet, aby udzielić użytkownikom dostępu do programów RemoteApp w grupie aplikacji.

   ```powershell
   Add-RdsAppGroupUser <tenantname> <hostpoolname> <appgroupname> -UserPrincipalName <userupn>
   ```

## <a name="next-steps"></a>Następne kroki

W tym samouczku przedstawiono sposób tworzenia grupy aplikacji, wypełniania jej przy użyciu programów RemoteApp i przypisywania użytkowników do grupy aplikacji. Aby dowiedzieć się, jak utworzyć pulę hostów weryfikacji, zobacz poniższy samouczek. Można użyć puli hostów weryfikacji do monitorowania aktualizacji usługi przed ich wycofaniem do środowiska produkcyjnego.

> [!div class="nextstepaction"]
> [Tworzenie puli hostów w celu sprawdzenia poprawności aktualizacji usługi](./create-validation-host-pool.md)
