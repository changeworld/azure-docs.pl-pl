---
title: Utwórz pulę hosta Windows wirtualnego pulpitu (wersja zapoznawcza) przy użyciu programu PowerShell — platformy Azure
description: Jak utworzyć pulę hosta w wersji zapoznawczej pulpitu wirtualnego Windows za pomocą poleceń cmdlet programu PowerShell.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: how-to
ms.date: 05/06/2019
ms.author: helohr
ms.openlocfilehash: a58e059e800b13d01ba8e50880bd75077d4418ae
ms.sourcegitcommit: 3ced637c8f1f24256dd6ac8e180fff62a444b03c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/17/2019
ms.locfileid: "65833969"
---
# <a name="create-a-host-pool-with-powershell"></a>Tworzenie puli hostów przy użyciu programu PowerShell

Pule hosta to zbiór przynajmniej jednej identycznych maszyn wirtualnych w środowiskach dzierżawy Windows wirtualnego Desktop w wersji zapoznawczej. Każda pula hosta może zawierać grupy aplikacji, które użytkownicy mogą korzystać z tak, jak w fizycznych pulpitu.

## <a name="use-your-powershell-client-to-create-a-host-pool"></a>Umożliwia utworzenie puli hosta przez klienta programu PowerShell

Po pierwsze, [Pobierz i zaimportuj moduł programu PowerShell pulpitu wirtualnego Windows](https://docs.microsoft.com/powershell/windows-virtual-desktop/overview) do użycia w sesji programu PowerShell, jeśli jeszcze go.

Uruchom następujące polecenie cmdlet, aby zalogować się do środowiska wirtualnego pulpitu Windows

```powershell
Add-RdsAccount -DeploymentUrl https://rdbroker.wvd.microsoft.com
```

Następnie uruchom to polecenie cmdlet, aby utworzyć nową pulę hosta w Twojej dzierżawie pulpitu wirtualnego Windows:

```powershell
New-RdsHostPool -TenantName <tenantname> -Name <hostpoolname>
```

Uruchom polecenie cmdlet dalej do utworzenia tokenu rejestracji, aby autoryzować hosta sesji, aby przyłączone do puli hosta i zapisać go do nowego pliku na komputerze lokalnym. Można określić, jak długo tokenu rejestracji jest prawidłowa przy użyciu parametru - ExpirationHours.

```powershell
New-RdsRegistrationInfo -TenantName <tenantname> -HostPoolName <hostpoolname> -ExpirationHours <number of hours> | Select-Object -ExpandProperty Token > <PathToRegFile>
```

Po tym Uruchom to polecenie cmdlet, aby dodać użytkowników usługi Azure Active Directory do domyślnej grupy aplikacji klasycznej dla puli hosta.

```powershell
Add-RdsAppGroupUser -TenantName <tenantname> -HostPoolName <hostpoolname> -AppGroupName "Desktop Application Group" -UserPrincipalName <userupn>
```

**RdsAppGroupUser Dodaj** polecenie cmdlet nie obsługuje dodawania grup zabezpieczeń i doda tylko z jednym użytkownikiem w czasie do grupy aplikacji. Jeśli chcesz dodać wielu użytkowników do grupy aplikacji, należy ponownie uruchomić polecenie cmdlet z głównych nazw użytkowników.

Uruchom następujące polecenie cmdlet można wyeksportować tokenu rejestracji do zmiennej, której użyjesz później w [rejestrowania maszyn wirtualnych do puli hosta wirtualnego pulpitu Windows](#register-the-virtual-machines-to-the-windows-virtual-desktop-preview-host-pool).

```powershell
$token = (Export-RdsRegistrationInfo -TenantName <tenantname> -HostPoolName <hostpoolname>).Token
```

## <a name="create-virtual-machines-for-the-host-pool"></a>Tworzenie maszyn wirtualnych w puli hosta

Teraz możesz utworzyć maszynę wirtualną platformy Azure, który może być częścią puli hosta wirtualnego pulpitu Windows.

Można utworzyć maszynę wirtualną na wiele sposobów:

- [Utwórz maszynę wirtualną z obrazu w galerii systemu Azure](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-portal#create-virtual-machine)
- [Tworzenie maszyny wirtualnej na podstawie obrazu zarządzanego](https://docs.microsoft.com/azure/virtual-machines/windows/create-vm-generalized-managed)
- [Utwórz maszynę wirtualną z obrazu niezarządzanego](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-from-user-image)

## <a name="prepare-the-virtual-machines-for-windows-virtual-desktop-preview-agent-installations"></a>Przygotowywanie maszyn wirtualnych w przypadku instalacji agenta Windows wirtualnego pulpitu (wersja zapoznawcza)

Należy wykonać następujące czynności na przygotowanie maszyn wirtualnych, aby można było zainstalować agentów pulpitu wirtualnego Windows i rejestrowania maszyn wirtualnych do puli hosta wirtualnego pulpitu Windows:

- Należy najpierw przyłączania do domeny komputera. Dzięki temu przychodzących użytkownikom Windows pulpitu wirtualnego można mapować ze swojego konta usługi Azure Active Directory do swojego konta usługi Active Directory i pomyślnie miały dostęp do maszyny wirtualnej.
- Jeśli maszyna wirtualna jest uruchomiona systemie operacyjnym Windows Server, należy zainstalować rolę hosta sesji usług pulpitu zdalnego (RDSH). Rola RDSH umożliwia agentów Windows pulpitu wirtualnego, aby poprawnie zainstalowany.

Aby pomyślnie przyłączania do domeny wykonaj następujące czynności na każdej maszynie wirtualnej:

1. [Łączenie z maszyną wirtualną](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-portal#connect-to-virtual-machine) przy użyciu poświadczeń dostarczonych podczas tworzenia maszyny wirtualnej.
2. Na maszynie wirtualnej Uruchom **Panelu sterowania** i wybierz **systemu**.
3. Wybierz **nazwy komputera**, wybierz opcję **zmiany ustawień**, a następnie wybierz pozycję **zmiany...**
4. Wybierz **domeny** , a następnie wprowadź domeny usługi Active Directory w sieci wirtualnej.
5. Uwierzytelnianie przy użyciu konta domeny, które ma uprawnienia do przyłączania do domeny komputerów.

    >[!NOTE]
    > Jeśli dołączasz maszyn wirtualnych w środowisku usług domenowych Azure AD, upewnij się, że użytkowników przyłączanie do domeny również członkiem [grupy Administratorzy usługi AAD DC](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-getting-started-admingroup#task-3-configure-administrative-group).

## <a name="register-the-virtual-machines-to-the-windows-virtual-desktop-preview-host-pool"></a>Rejestrowanie maszyn wirtualnych do puli hosta Windows wirtualnego pulpitu (wersja zapoznawcza)

Rejestrowanie maszyn wirtualnych do puli hosta Windows pulpitu wirtualnego jest tak proste, jak instalowanie agentów Windows pulpitu wirtualnego.

Aby zarejestrować agentów pulpitu wirtualnego Windows, wykonaj następujące czynności na każdej maszynie wirtualnej:

1. [Łączenie z maszyną wirtualną](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-portal#connect-to-virtual-machine) przy użyciu poświadczeń dostarczonych podczas tworzenia maszyny wirtualnej.
2. Pobierz i zainstaluj agenta pulpitu wirtualnego Windows.
   - Pobierz [wirtualnej agenta pulpitu Windows](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RWrmXv).
   - Kliknij prawym przyciskiem myszy pobrany plik Instalatora, wybierz **właściwości**, wybierz opcję **odblokowanie**, a następnie wybierz **OK**. Dzięki temu system ufać Instalatora.
   - Uruchom Instalatora. Gdy Instalator monituje o tokenu rejestracji, wprowadź wartość uzyskana z **RdsRegistrationInfo eksportu** polecenia cmdlet.
3. Pobierz i zainstaluj inicjującego Windows Agent pulpitu wirtualnego.
   - Pobierz [programu inicjującego agenta pulpitów wirtualnych Windows](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RWrxrH).
   - Kliknij prawym przyciskiem myszy pobrany plik Instalatora, wybierz **właściwości**, wybierz opcję **odblokowanie**, a następnie wybierz **OK**. Dzięki temu system ufać Instalatora.
   - Uruchom Instalatora.

>[!IMPORTANT]
>Aby zabezpieczyć środowisku pulpitu wirtualnego Windows na platformie Azure, zalecamy nie otwieraj portu wejściowego 3389 na maszynach wirtualnych. Pulpit wirtualny Windows nie wymaga otwartego portu dla ruchu przychodzącego 3389 dla użytkowników puli hosta maszyn wirtualnych. Jeśli musisz otworzyć port 3389 na potrzeby rozwiązywania problemów, zalecamy użycie [dostęp do maszyny Wirtualnej just-in-time](https://docs.microsoft.com/azure/security-center/security-center-just-in-time).

## <a name="next-steps"></a>Kolejne kroki

Teraz, gdy wprowadzono puli hosta, możesz go wypełnić RemoteApps. Aby dowiedzieć się więcej na temat sposobu zarządzania aplikacjami w Windows pulpitu wirtualnego, zobacz samouczek grup aplikacji Zarządzanie.

> [!div class="nextstepaction"]
> [Zarządzać samouczek grup aplikacji](./manage-app-groups.md)
