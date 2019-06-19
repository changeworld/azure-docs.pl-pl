---
title: Zarządzanie grupami aplikacji dla Windows wirtualnego pulpitu (wersja zapoznawcza) — platformy Azure
description: W tym artykule opisano sposób konfigurowania dzierżaw Windows wirtualnego pulpitu (wersja zapoznawcza) w usłudze Azure Active Directory.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: tutorial
ms.date: 03/21/2019
ms.author: helohr
ms.openlocfilehash: 73425df1f0cfedd2a681650fc2b536a652b621d5
ms.sourcegitcommit: b7a44709a0f82974578126f25abee27399f0887f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/18/2019
ms.locfileid: "67206672"
---
# <a name="tutorial-manage-app-groups-for-windows-virtual-desktop-preview"></a>Samouczek: Zarządzanie grupami aplikacji dla Windows wirtualnego pulpitu (wersja zapoznawcza)

Domyślna grupa aplikacji utworzonych dla nowej puli hosta Windows wirtualnego pulpitu (wersja zapoznawcza) są również publikowane pełnego pulpitu. Ponadto można utworzyć co najmniej jedną grupę aplikacji programów RemoteApp puli hosta. Postępuj zgodnie z tym samouczkiem, aby utworzyć grupę aplikacji programów RemoteApp i publikowanie poszczególnych **Start** menu aplikacji.

W tym samouczku pokazano, jak:

> [!div class="checklist"]
> * Utwórz grupę programów RemoteApp.
> * Udzielanie dostępu do programów RemoteApp.

Przed przystąpieniem do wykonywania [Pobierz i zaimportuj moduł programu PowerShell pulpitu wirtualnego Windows](https://docs.microsoft.com/powershell/windows-virtual-desktop/overview) do użycia w sesji programu PowerShell, jeśli jeszcze go.

## <a name="create-a-remoteapp-group"></a>Utwórz grupę programów RemoteApp

1. Uruchom następujące polecenia cmdlet programu PowerShell do tworzenia pustej grupy aplikacji programów RemoteApp.

   ```powershell
   New-RdsAppGroup <tenantname> <hostpoolname> <appgroupname> -ResourceType "RemoteApp"
   ```

2. (Opcjonalnie) Aby sprawdzić, czy utworzono grupę aplikacji, można uruchomić następujące polecenie cmdlet, aby wyświetlić listę wszystkich grup aplikacji dla puli hosta.

   ```powershell
   Get-RdsAppGroup <tenantname> <hostpoolname>
   ```

3. Uruchom następujące polecenie cmdlet, aby uzyskać listę **Start** menu aplikacje w obrazach maszyn wirtualnych w puli hosta. Zanotuj wartości **FilePath**, **IconPath**, **IconIndex**oraz inne ważne informacje dla aplikacji, którą chcesz opublikować.

   ```powershell
   Get-RdsStartMenuApp <tenantname> <hostpoolname> <appgroupname>
   ```
   
4. Uruchom następujące polecenie cmdlet, aby zainstalować aplikację na podstawie `AppAlias`. `AppAlias` Po uruchomieniu dane wyjściowe z kroku 3, staje się widoczny.

   ```powershell
   New-RdsRemoteApp <tenantname> <hostpoolname> <appgroupname> -Name <remoteappname> -AppAlias <appalias>
   ```

5. (Opcjonalnie) Uruchom następujące polecenie cmdlet, aby opublikować nowy program RemoteApp do grupy aplikacji utworzonej w kroku 1.

   ```powershell
   New-RdsRemoteApp <tenantname> <hostpoolname> <appgroupname> -Name <remoteappname> -Filepath <filepath>  -IconPath <iconpath> -IconIndex <iconindex>
   ```

6. Aby sprawdzić, czy aplikacja została opublikowana, uruchom następujące polecenie cmdlet.

   ```powershell
   Get-RdsRemoteApp <tenantname> <hostpoolname> <appgroupname>
   ```

7. Powtórz kroki 1 – 5 dla każdej aplikacji, którą chcesz opublikować dla tej grupy aplikacji.
8. Uruchom następujące polecenie cmdlet, aby zezwolić użytkownikom na dostęp do programów RemoteApp w grupie aplikacji.

   ```powershell
   Add-RdsAppGroupUser <tenantname> <hostpoolname> <appgroupname> -UserPrincipalName <userupn>
   ```

## <a name="next-steps"></a>Kolejne kroki

W tym samouczku przedstawiono sposób tworzenia grupy aplikacji, go wypełnić przy użyciu programów RemoteApp i przypisywanie użytkowników do grupy aplikacji. Aby dowiedzieć się, jak utworzyć pulę hosta weryfikacji, zobacz następujące samouczki. Pula hosta sprawdzania poprawności służy do monitorowania usługi aktualizacji przed zarządzeniem do środowiska produkcyjnego.

> [!div class="nextstepaction"]
> [Utwórz pulę hosta do sprawdzania poprawności aktualizacje usług](./create-validation-host-pool.md)
