---
title: Tworzenie puli hostów pulpitu wirtualnego systemu Windows PowerShell — platforma Azure
description: Jak utworzyć pulę hosta w programie Windows Virtual Desktop z poleceniami cmdlet programu PowerShell.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 08/29/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: b390c0beb20b7557294c18f889a0f41023513e2a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80246963"
---
# <a name="create-a-host-pool-with-powershell"></a>Tworzenie puli hostów przy użyciu programu PowerShell

Pule hostów to zbiór co najmniej jednej identycznej maszyny wirtualnej w środowiskach dzierżawy pulpitu wirtualnego systemu Windows. Każda pula hostów może zawierać grupę aplikacji, z którą użytkownicy mogą wchodzić w interakcje, tak jak na pulpicie fizycznym.

## <a name="use-your-powershell-client-to-create-a-host-pool"></a>Tworzenie puli hostów za pomocą klienta programu PowerShell

Najpierw [pobierz i zaimportuj moduł programu Windows Virtual Desktop PowerShell](/powershell/windows-virtual-desktop/overview/) do użycia w sesji programu PowerShell, jeśli jeszcze tego nie zrobiłeś.

Uruchom następujące polecenie cmdlet, aby zalogować się do środowiska pulpitu wirtualnego systemu Windows

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

Następnie uruchom to polecenie cmdlet, aby utworzyć nową pulę hostów w dzierżawie pulpitu wirtualnego systemu Windows:

```powershell
New-RdsHostPool -TenantName <tenantname> -Name <hostpoolname>
```

Uruchom następne polecenie cmdlet, aby utworzyć token rejestracji, aby autoryzować hosta sesji do dołączenia do puli hostów i zapisać go w nowym pliku na komputerze lokalnym. Można określić, jak długo token rejestracji jest prawidłowy przy użyciu parametru -ExpirationHours.

```powershell
New-RdsRegistrationInfo -TenantName <tenantname> -HostPoolName <hostpoolname> -ExpirationHours <number of hours> | Select-Object -ExpandProperty Token | Out-File -FilePath <PathToRegFile>
```

Następnie uruchom to polecenie cmdlet, aby dodać użytkowników usługi Azure Active Directory do domyślnej grupy aplikacji klasycznych dla puli hostów.

```powershell
Add-RdsAppGroupUser -TenantName <tenantname> -HostPoolName <hostpoolname> -AppGroupName "Desktop Application Group" -UserPrincipalName <userupn>
```

Polecenie cmdlet **Add-RdsAppGroupUser** nie obsługuje dodawania grup zabezpieczeń i dodaje tylko jednego użytkownika naraz do grupy aplikacji. Jeśli chcesz dodać wielu użytkowników do grupy aplikacji, uruchom ponownie polecenie cmdlet z odpowiednimi nazwami głównymi użytkowników.

Uruchom następujące polecenie cmdlet, aby wyeksportować token rejestracji do zmiennej, której użyjesz później w [dziel się maszyn wirtualnych do puli hostów pulpitu wirtualnego systemu Windows](#register-the-virtual-machines-to-the-windows-virtual-desktop-host-pool).

```powershell
$token = (Export-RdsRegistrationInfo -TenantName <tenantname> -HostPoolName <hostpoolname>).Token
```

## <a name="create-virtual-machines-for-the-host-pool"></a>Tworzenie maszyn wirtualnych dla puli hostów

Teraz możesz utworzyć maszynę wirtualną platformy Azure, którą można przyłączyć do puli hostów pulpitu wirtualnego systemu Windows.

Maszynę wirtualną można utworzyć na wiele sposobów:

- [Tworzenie maszyny wirtualnej na podstawie obrazu usługi Azure Gallery](../virtual-machines/windows/quick-create-portal.md#create-virtual-machine)
- [Tworzenie maszyny wirtualnej na podstawie obrazu zarządzanego](../virtual-machines/windows/create-vm-generalized-managed.md)
- [Tworzenie maszyny wirtualnej na podstawie obrazu niezarządzanego](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-user-image-data-disks)

>[!NOTE]
>Jeśli wdrażasz maszynę wirtualną przy użyciu systemu Windows 7 jako systemu operacyjnego hosta, proces tworzenia i wdrażania będzie nieco inny. Aby uzyskać więcej informacji, zobacz [Wdrażanie maszyny wirtualnej systemu Windows 7 na pulpicie wirtualnym systemu Windows](deploy-windows-7-virtual-machine.md).

Po utworzeniu maszyn wirtualnych hosta sesji [zastosuj licencję systemu Windows do maszyny wirtualnej hosta sesji,](./apply-windows-license.md#apply-a-windows-license-to-a-session-host-vm) aby uruchomić maszyny wirtualne systemu Windows lub Windows Server bez płacenia za inną licencję. 

## <a name="prepare-the-virtual-machines-for-windows-virtual-desktop-agent-installations"></a>Przygotowywanie maszyn wirtualnych dla instalacji agentów pulpitu wirtualnego systemu Windows

Aby zainstalować agentów pulpitu wirtualnego systemu Windows i zarejestrować maszyny wirtualne w puli hostów pulpitu wirtualnego systemu Windows, należy wykonać następujące czynności:

- Musisz dołączyć do domeny komputera. Dzięki temu przychodzące pulpitu wirtualnego systemu Windows mogą być mapowane z konta usługi Azure Active Directory do konta usługi Active Directory i pomyślnie mieć dostęp do maszyny wirtualnej.
- Jeśli na maszynie wirtualnej jest uruchomiony system operacyjny Windows Server, należy zainstalować rolę Host sesji usług pulpitu zdalnego (RDSH). Rola RDSH umożliwia agentom pulpitu wirtualnego systemu Windows prawidłowe zainstalowanie.

Aby pomyślnie dołączyć do domeny, wykonaj następujące czynności na każdej maszynie wirtualnej:

1. [Połącz się z maszyną wirtualną](../virtual-machines/windows/quick-create-portal.md#connect-to-virtual-machine) z poświadczeniami podanymi podczas tworzenia maszyny wirtualnej.
2. Na maszynie wirtualnej uruchom **Panel sterowania** i wybierz **pozycję System**.
3. Wybierz **pozycję Nazwa komputera**, wybierz pozycję Zmień **ustawienia**, a następnie wybierz pozycję **Zmień...**
4. Wybierz **pozycję Domena,** a następnie wprowadź domenę usługi Active Directory w sieci wirtualnej.
5. Uwierzytelnij się przy użyciu konta domeny, które ma uprawnienia do komputerów dołączanych do domeny.

    >[!NOTE]
    > Jeśli łączysz maszyny wirtualne ze środowiskiem usług domenowych Usługi domenowe Active Directory platformy Azure (Usługi Usługi Azure AD DS), upewnij się, że użytkownik dołączania do domeny jest również członkiem [grupy Administratorzy kontrolera domeny usługi AAD.](../active-directory-domain-services/tutorial-create-instance-advanced.md#configure-an-administrative-group)

## <a name="register-the-virtual-machines-to-the-windows-virtual-desktop-host-pool"></a>Rejestrowanie maszyn wirtualnych w puli hostów pulpitu wirtualnego systemu Windows

Rejestrowanie maszyn wirtualnych w puli hostów pulpitu wirtualnego systemu Windows jest tak proste, jak instalowanie agentów pulpitu wirtualnego systemu Windows.

Aby zarejestrować agentów pulpitu wirtualnego systemu Windows, wykonaj następujące czynności na każdej maszynie wirtualnej:

1. [Połącz się z maszyną wirtualną](../virtual-machines/windows/quick-create-portal.md#connect-to-virtual-machine) z poświadczeniami podanymi podczas tworzenia maszyny wirtualnej.
2. Pobierz i zainstaluj agenta pulpitu wirtualnego systemu Windows.
   - Pobierz [program Windows Virtual Desktop Agent](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RWrmXv).
   - Kliknij prawym przyciskiem myszy pobrany instalator, wybierz **polecenie Właściwości**, wybierz pozycję **Odblokuj**, a następnie wybierz przycisk **OK**. Pozwoli to systemowi zaufać instalatorowi.
   - Uruchom instalatora. Gdy instalator poprosi o token rejestracji, wprowadź wartość, którą otrzymałeś z polecenia cmdlet **Export-RdsRegistrationInfo.**
3. Pobierz i zainstaluj program rozruchowy programu Windows Virtual Desktop Agent.
   - Pobierz [program Windows Virtual Desktop Agent Bootloader](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RWrxrH).
   - Kliknij prawym przyciskiem myszy pobrany instalator, wybierz **polecenie Właściwości**, wybierz pozycję **Odblokuj**, a następnie wybierz przycisk **OK**. Pozwoli to systemowi zaufać instalatorowi.
   - Uruchom instalatora.

>[!IMPORTANT]
>Aby zabezpieczyć środowisko pulpitu wirtualnego systemu Windows na platformie Azure, zalecamy, aby nie otwierać portu przychodzącego 3389 na maszynach wirtualnych. Pulpit wirtualny systemu Windows nie wymaga otwartego portu przychodzącego 3389, aby użytkownicy mogli uzyskiwać dostęp do maszyn wirtualnych puli hostów. Jeśli musisz otworzyć port 3389 w celu rozwiązywania problemów, zalecamy użycie [dostępu do maszyn wirtualnych just-in-time](../security-center/security-center-just-in-time.md).

## <a name="next-steps"></a>Następne kroki

Teraz, gdy masz już pulę hostów, możesz wypełnić ją za pomocą aplikacji RemoteApps. Aby dowiedzieć się więcej o zarządzaniu aplikacjami na pulpicie wirtualnym systemu Windows, zobacz samouczek Zarządzanie grupami aplikacji.

> [!div class="nextstepaction"]
> [Samouczek Zarządzanie grupami aplikacji](./manage-app-groups.md)
