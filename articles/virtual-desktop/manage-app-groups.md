---
title: Zarządzanie grupami aplikacji dla Windows pulpitu wirtualnego (wersja zapoznawcza) — platformy Azure
description: W tym artykule opisano sposób konfigurowania dzierżaw pulpitu wirtualnego Windows w usłudze Azure Active Directory.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: tutorial
ms.date: 03/21/2019
ms.author: helohr
ms.openlocfilehash: 455e3b4ac4a5020f68b5201bc19f85892ef62cb1
ms.sourcegitcommit: 49c8204824c4f7b067cd35dbd0d44352f7e1f95e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/22/2019
ms.locfileid: "58368990"
---
# <a name="tutorial-manage-app-groups-for-windows-virtual-desktop-preview"></a>Samouczek: Zarządzanie grupami aplikacji dla Windows pulpitu wirtualnego (wersja zapoznawcza)

Domyślna grupa aplikacji utworzonych dla nowej puli hosta, publikuje również pełnego pulpitu. Ponadto można utworzyć co najmniej jedną grupę aplikacji programów RemoteApp (wersja zapoznawcza) dla puli hosta. Postępuj zgodnie z tym samouczkiem, aby utworzyć grupę aplikacji programów RemoteApp i publikowanie poszczególnych aplikacji w menu Start.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Utwórz grupę programów RemoteApp.
> * Udzielanie dostępu do RemoteApps.

Przed przystąpieniem do wykonywania [Pobierz i zaimportuj moduł programu PowerShell pulpitu wirtualnego Windows](https://docs.microsoft.com/powershell/windows-virtual-desktop/overview) modułu pulpitu wirtualnego Windows do użycia w sesji programu PowerShell, jeśli jeszcze go.

## <a name="create-a-remoteapp-group"></a>Utwórz grupę programów RemoteApp

1. Uruchom następujące polecenia cmdlet programu PowerShell do utworzenia nowej pustej grupy programów RemoteApp.

   ```powershell
   New-RdsAppGroup <tenantname> <hostpoolname> <appgroupname> -ResourceType "RemoteApp"
   ```

2. (Opcjonalnie) Aby sprawdzić, czy utworzono grupę aplikacji, można uruchomić następujące polecenie cmdlet, aby wyświetlić listę wszystkich grup aplikacji dla puli hosta.

   ```powershell
   Get-RdsAppGroup <tenantname> <hostpoolname>
   ```

3. Uruchom następujące polecenie cmdlet, aby uzyskać listę start menu aplikacje w obrazach maszyn wirtualnych w puli hosta. Zanotuj wartości **FilePath**, **IconPath**, **IconIndex**oraz inne ważne informacje dla aplikacji, który chcesz opublikować.

   ```powershell
   Get-RdsStartMenuApp <tenantname> <hostpoolname> <appgroupname>
   ```

4. Uruchom następujące polecenie cmdlet, aby opublikować nowe usługi RemoteApp do grupy aplikacji utworzonej w kroku 1.

   ```powershell
   New-RdsRemoteApp <tenantname> <hostpoolname> <appgroupname> <remoteappname> -Filepath <filepath>  -IconPath <iconpath> -IconIndex <iconindex>
   ```

5. (Opcjonalnie) Uruchom następujące polecenie cmdlet do zainstalowania aplikacji, w oparciu o appalias. appalias staje się widoczny po uruchomieniu dane wyjściowe z kroku 3.

   ```powershell
   New-RdsRemoteApp <tenantname> <hostpoolname> <appgroupname> <remoteappname> -AppAlias <appalias>
   ```

6. Aby sprawdzić, czy aplikacja została opublikowana, uruchom następujące polecenie cmdlet.

   ```powershell
   Get-RdsRemoteApp <tenantname> <hostpoolname> <appgroupname>
   ```

7. Powtórz kroki 1 – 5 dla każdej aplikacji, którą chcesz opublikować dla tej grupy aplikacji.
8. Uruchom następujące polecenie cmdlet, aby zezwolić użytkownikom na dostęp do RemoteApps w grupie aplikacji.

   ```powershell
   Add-RdsAppGroupUser <tenantname> <hostpoolname> <appgroupname> -UserPrincipalName <userupn>
   ```

## <a name="next-steps"></a>Kolejne kroki

Po utworzeniu grup aplikacji tworzenie jednostek usługi i przypisać role do użytkowników. Aby dowiedzieć się, jak to zrobić, zobacz samouczek dotyczący sposobu tworzenia jednostki usługi i przypisań ról za pomocą programu PowerShell.

> [!div class="nextstepaction"]
> [Tworzenie jednostek usługi i przypisań ról za pomocą programu PowerShell](create-service-principal-role-powershell.md)
