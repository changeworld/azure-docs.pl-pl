---
title: Zarządzanie grupami aplikacji dla pulpitu wirtualnego systemu Windows — Platforma Azure
description: W tym artykule opisano sposób konfigurowania dzierżaw pulpitu wirtualnego systemu Windows w usłudze Azure Active Directory.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: tutorial
ms.date: 08/29/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 9a9d92ea525c6b5a64fdf7cc74babdce6a97f923
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "79127813"
---
# <a name="tutorial-manage-app-groups-for-windows-virtual-desktop"></a>Samouczek: Zarządzanie grupami aplikacji dla pulpitu wirtualnego systemu Windows

Domyślna grupa aplikacji utworzona dla nowej puli hostów pulpitu wirtualnego systemu Windows również publikuje pełny pulpit. Ponadto można utworzyć jedną lub więcej grup aplikacji RemoteApp dla puli hostów. Wykonaj ten samouczek, aby utworzyć grupę aplikacji RemoteApp i opublikować poszczególne aplikacje menu **Start.**

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie grupy remoteapp.
> * Udziel dostępu do programów RemoteApp.

Przed rozpoczęciem [pobierz i zaimportuj moduł programu Windows Virtual Desktop PowerShell](/powershell/windows-virtual-desktop/overview/) do użycia w sesji programu PowerShell, jeśli jeszcze tego nie zrobiłeś. Następnie uruchom następujące polecenie cmdlet, aby zalogować się na swoje konto:

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

## <a name="create-a-remoteapp-group"></a>Tworzenie grupy aplikacji RemoteApp

1. Uruchom następujące polecenie cmdlet programu PowerShell, aby utworzyć nową pustą grupę aplikacji RemoteApp.

   ```powershell
   New-RdsAppGroup <tenantname> <hostpoolname> <appgroupname> -ResourceType "RemoteApp"
   ```

2. (Opcjonalnie) Aby sprawdzić, czy grupa aplikacji została utworzona, można uruchomić następujące polecenie cmdlet, aby wyświetlić listę wszystkich grup aplikacji dla puli hostów.

   ```powershell
   Get-RdsAppGroup <tenantname> <hostpoolname>
   ```

3. Uruchom następujące polecenie cmdlet, aby uzyskać listę aplikacji menu **Start** na obrazie maszyny wirtualnej puli hosta. Zapisz wartości **dla FilePath**, **IconPath**, **IconIndex**i inne ważne informacje dla aplikacji, którą chcesz opublikować.

   ```powershell
   Get-RdsStartMenuApp <tenantname> <hostpoolname> <appgroupname>
   ```
   
4. Uruchom następujące polecenie cmdlet, aby `AppAlias`zainstalować aplikację na podstawie pliku . `AppAlias`staje się widoczna po uruchomieniu wyjścia z kroku 3.

   ```powershell
   New-RdsRemoteApp <tenantname> <hostpoolname> <appgroupname> -Name <remoteappname> -AppAlias <appalias>
   ```

5. (Opcjonalnie) Uruchom następujące polecenie cmdlet, aby opublikować nowy program RemoteApp w grupie aplikacji utworzonej w kroku 1.

   ```powershell
   New-RdsRemoteApp <tenantname> <hostpoolname> <appgroupname> -Name <remoteappname> -Filepath <filepath>  -IconPath <iconpath> -IconIndex <iconindex>
   ```

6. Aby sprawdzić, czy aplikacja została opublikowana, uruchom następujące polecenie cmdlet.

   ```powershell
   Get-RdsRemoteApp <tenantname> <hostpoolname> <appgroupname>
   ```

7. Powtórz kroki od 1 do 5 dla każdej aplikacji, którą chcesz opublikować dla tej grupy aplikacji.
8. Uruchom następujące polecenie cmdlet, aby udzielić użytkownikom dostępu do programów RemoteApp w grupie aplikacji.

   ```powershell
   Add-RdsAppGroupUser <tenantname> <hostpoolname> <appgroupname> -UserPrincipalName <userupn>
   ```

## <a name="next-steps"></a>Następne kroki

W tym samouczku dowiesz się, jak utworzyć grupę aplikacji, wypełnić ją programami RemoteApp i przypisać użytkowników do grupy aplikacji. Aby dowiedzieć się, jak utworzyć pulę hostów sprawdzania poprawności, zobacz następujący samouczek. Puli hostów sprawdzania poprawności można monitorować aktualizacje usług przed wprowadzeniem ich do środowiska produkcyjnego.

> [!div class="nextstepaction"]
> [Tworzenie puli hostów na potrzeby weryfikacji aktualizacji usług](./create-validation-host-pool.md)
