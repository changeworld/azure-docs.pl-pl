---
title: Utwórz pulę hosta Windows wirtualnego pulpitu (wersja zapoznawcza) przy użyciu programu PowerShell — platformy Azure
description: Jak utworzyć pulę hosta w wersji zapoznawczej pulpitu wirtualnego Windows za pomocą poleceń cmdlet programu PowerShell.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: how-to
ms.date: 03/21/2019
ms.author: helohr
ms.openlocfilehash: bd46e5f7428bab58508521b2c7d4d7cca25d689b
ms.sourcegitcommit: 70550d278cda4355adffe9c66d920919448b0c34
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2019
ms.locfileid: "58439077"
---
# <a name="create-a-host-pool-with-powershell"></a>Tworzenie puli hostów przy użyciu programu PowerShell

Pule hosta to zbiór przynajmniej jednej identycznych maszyn wirtualnych w środowiskach dzierżawy Windows wirtualnego Desktop w wersji zapoznawczej. Każda pula hosta może zawierać grupy aplikacji, które użytkownicy mogą korzystać z tak, jak w fizycznych pulpitu.

## <a name="use-your-powershell-client-to-create-a-host-pool"></a>Umożliwia utworzenie puli hosta przez klienta programu PowerShell

Po pierwsze, [Pobierz i zaimportuj moduł programu PowerShell pulpitu wirtualnego Windows](https://docs.microsoft.com/powershell/windows-virtual-desktop/overview) do użycia w sesji programu PowerShell, jeśli jeszcze go.

Uruchom następujące polecenie cmdlet, aby zalogować się do środowiska wirtualnego pulpitu Windows

```powershell
Add-RdsAccount -DeploymentUrl https://rdbroker.wvd.microsoft.com
```

Następnie uruchom następujące polecenie cmdlet, aby ustawić kontekst do grupy dzierżawy. Jeśli nie masz nazwy grupy dzierżawy dzierżawy najprawdopodobniej w grupie"domyślna dzierżawa" można pominąć to polecenie cmdlet.

```powershell
Set-RdsContext -TenantGroupName <tenantgroupname>
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
4. Zainstaluj lub aktywowania Windows pulpitu wirtualnego stosu side-by-side. Kroki będzie się różnił w zależności od wersji systemu operacyjnego korzysta z maszyny wirtualnej.
   - Jeśli system operacyjny maszyny wirtualnej systemu Windows Server 2016:
     - Pobierz [stosu side-by-side pulpitu wirtualnego Windows](https://go.microsoft.com/fwlink/?linkid=2084270).
     - Kliknij prawym przyciskiem myszy pobrany plik Instalatora, wybierz **właściwości**, wybierz opcję **odblokowanie**, a następnie wybierz **OK**. Dzięki temu system ufać Instalatora.
     - Uruchom Instalatora.
   - W przypadku systemu operacyjnego na maszynie wirtualnej systemu Windows 10 1809 lub nowszym lub Windows Server 2019 lub nowszy:
     - Pobierz [skryptu](https://go.microsoft.com/fwlink/?linkid=2084268) aktywować stosu side-by-side.
     - Kliknij prawym przyciskiem myszy pobranego skryptu, wybierz **właściwości**, wybierz opcję **odblokowanie**, a następnie wybierz **OK**. Dzięki temu system ufać skryptu.
     - Z **Start** menu wyszukiwania dla środowiska Windows PowerShell ISE, kliknij go prawym przyciskiem myszy, a następnie wybierz **Uruchom jako administrator**.
     - Wybierz **pliku**, następnie **Otwórz...** , a następnie znajdź skrypt programu PowerShell z pobranymi plikami i otwórz go.
     - Wybierz przycisk zielony Odtwórz, aby uruchomić skrypt.

## <a name="next-steps"></a>Kolejne kroki

Teraz, gdy wprowadzono puli hosta, możesz go wypełnić RemoteApps. Aby dowiedzieć się więcej na temat sposobu zarządzania aplikacjami w Windows pulpitu wirtualnego, zobacz samouczek grup aplikacji Zarządzanie.

> [!div class="nextstepaction"]
> [Zarządzać samouczek grup aplikacji](./manage-app-groups.md)
