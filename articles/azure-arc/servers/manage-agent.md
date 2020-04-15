---
title: Zarządzanie agentem usługi Azure Arc dla serwerów (wersja zapoznawcza)
description: W tym artykule opisano różne zadania zarządzania, które zazwyczaj będą wykonywane podczas cyklu życia usługi Azure Arc dla agenta połączonego komputera serwerów.
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-servers
author: mgoedtel
ms.author: magoedte
ms.date: 04/14/2020
ms.topic: conceptual
ms.openlocfilehash: 5ad2127b4cb9da3ca83aa04bd1885908a88dba62
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2020
ms.locfileid: "81308968"
---
# <a name="managing-and-maintaining-the-connected-machine-agent"></a>Zarządzanie i utrzymywanie agenta Podłączonego komputera

Po początkowym wdrożeniu programu Azure Arc dla serwerów (wersja zapoznawcza) Agent connected machine dla systemu Windows lub Linux może być konieczne ponowne skonfigurowanie agenta, uaktualnienie go lub usunięcie go z komputera, jeśli osiągnął etap przejścia na emeryturę w jego cyklu życia. Te rutynowe zadania konserwacyjne można łatwo zarządzać ręcznie lub za pomocą automatyzacji, co zmniejsza zarówno błędy operacyjne, jak i wydatki.

## <a name="upgrading-agent"></a>Agent uaktualniania

Agent połączonego komputera platformy Azure dla systemów Windows i Linux można uaktualnić do najnowszej wersji ręcznie lub automatycznie w zależności od wymagań. W poniższej tabeli opisano metody obsługiwane do wykonywania uaktualnienia agenta.

| System operacyjny | Metoda uaktualniania |
|------------------|----------------|
| Windows | Ręczne<br> Windows Update |
| Ubuntu | [Apt](https://help.ubuntu.com/lts/serverguide/apt.html) |
| SUSE Linux Enterprise Server | [zamek błyskawiczny](https://en.opensuse.org/SDB:Zypper_usage_11.3) |
| RedHat Enterprise, Amazon, CentOS Linux | [Yum](https://wiki.centos.org/PackageManagement/Yum) | 

### <a name="windows-agent"></a>Agent systemu Windows

Aby zaktualizować agenta na komputerze z systemem Windows do najnowszej wersji, agent jest dostępny w witrynie Microsoft Update i można go wdrożyć przy użyciu istniejącego procesu zarządzania aktualizacjami oprogramowania. Można go również uruchomić ręcznie z wiersza polecenia, ze skryptu lub innego rozwiązania `AzureConnectedMachine.msi`automatyzacji lub z kreatora interfejsu użytkownika, wykonując program . 

> [!NOTE]
> * Aby uaktualnić agenta, musisz mieć uprawnienia *administratora.*
> * Aby uaktualnić ręcznie, należy najpierw pobrać i skopiować pakiet Instalatora do folderu na serwerze docelowym lub z udostępnionego folderu sieciowego. 

Jeśli nie znasz opcji wiersza polecenia dla pakietów Instalatora Windows, zapoznaj się ze [standardowymi opcjami wiersza polecenia msiexec](https://docs.microsoft.com/windows/win32/msi/standard-installer-command-line-options) i [opcjami wiersza polecenia Msiexec](https://docs.microsoft.com/windows/win32/msi/command-line-options).

#### <a name="to-upgrade-using-the-setup-wizard"></a>Aby uaktualnić za pomocą Kreatora instalacji

1. Zaloguj się do komputera przy użyciu konta z uprawnieniami administracyjnymi.

2. Uruchom **usługę AzureConnectedMachineAgent.msi,** aby uruchomić Kreatora instalacji.

Kreator instalacji odnajduje, czy istnieje poprzednia wersja, a następnie automatycznie wykonuje uaktualnienie agenta. Po zakończeniu uaktualnienia Kreator instalacji zostanie automatycznie zamknięty.

#### <a name="to-upgrade-from-the-command-line"></a>Aby uaktualnić z wiersza polecenia

1. Zaloguj się do komputera przy użyciu konta z uprawnieniami administracyjnymi.

2. Aby po cichu uaktualnić agenta `C:\Support\Logs` i utworzyć plik dziennika konfiguracji w folderze, uruchom następujące polecenie.

    ```dos
    msiexec.exe /i AzureConnectedMachineAgent.msi /qn /l*v "C:\Support\Logs\Azcmagentupgradesetup.log"
    ```

### <a name="linux-agent"></a>Agent Linuksa

Aby zaktualizować agenta na komputerze z systemem Linux do najnowszej wersji, obejmuje dwa polecenia. Jedno polecenie, aby zaktualizować indeks pakietu lokalnego z listą najnowszych dostępnych pakietów z repozytoriów i jedno polecenie uaktualniania pakietu lokalnego. 

> [!NOTE]
> Aby uaktualnić agenta, musisz mieć uprawnienia dostępu *głównego* lub konto, które ma podwyższone prawa przy użyciu sudo.

#### <a name="upgrade-ubuntu"></a>Uaktualnij Ubuntu

1. Aby zaktualizować indeks pakietów lokalnych o najnowsze zmiany wprowadzone w repozytoriach, uruchom następujące polecenie:

    ```bash
    apt update
    ```

2. Aby uaktualnić system, uruchom następujące polecenie:

    ```bash
    apt upgrade
    ```

Akcje polecenia [apt,](https://help.ubuntu.com/lts/serverguide/apt.html) takie jak instalacja i usuwanie pakietów, są rejestrowane w pliku `/var/log/dpkg.log` dziennika.

#### <a name="upgrade-red-hatcentosamazon-linux"></a>Uaktualnij Czerwony Kapelusz/CentOS/Amazon Linux

1. Aby zaktualizować indeks pakietów lokalnych o najnowsze zmiany wprowadzone w repozytoriach, uruchom następujące polecenie:

    ```bash
    yum check-update
    ```

2. Aby uaktualnić system, uruchom następujące polecenie:

    ```bash
    yum update
    ```

Akcje polecenia [yum,](https://access.redhat.com/articles/yum-cheat-sheet) takie jak instalacja i usuwanie pakietów, są rejestrowane w pliku `/var/log/yum.log` dziennika. 

#### <a name="upgrade-suse-linux-enterprise"></a>Uaktualnienie SUSE Linux Enterprise

1. Aby zaktualizować indeks pakietów lokalnych o najnowsze zmiany wprowadzone w repozytoriach, uruchom następujące polecenie:

    ```bash
    zypper refresh
    ```

2. Aby uaktualnić system, uruchom następujące polecenie:

    ```bash
    zypper update
    ```

Akcje polecenia [zypper,](https://en.opensuse.org/Portal:Zypper) takie jak instalacja i usuwanie pakietów, są rejestrowane w pliku `/var/log/zypper.log` dziennika. 

## <a name="about-the-azcmagent-tool"></a>Informacje o narzędziu Azcmagent

Narzędzie Azcmagent (Azcmagent.exe) służy do konfigurowania programu Azure Arc dla serwerów (wersja zapoznawcza) agenta połączonego komputera podczas instalacji lub modyfikowania początkowej konfiguracji agenta po instalacji. Program Azcmagent.exe udostępnia parametry wiersza polecenia w celu dostosowania agenta i wyświetlenia jego stanu:

* **Połącz** — aby połączyć komputer z programem Azure Arc

* **Rozłącz** — aby odłączyć komputer od pracy w yłączoneju azure arc

* **Ponowne łączenie** — aby ponownie podłączyć odłączony komputer do usługi Azure Arc

* **Pokaż** — wyświetl stan agenta i jego właściwości konfiguracji (nazwa grupy zasobów, identyfikator subskrypcji, wersja itp.), które mogą pomóc w rozwiązaniu problemu z agentem.

* **-h lub --help** - Pokazuje dostępne parametry wiersza polecenia

    Na przykład, aby wyświetlić szczegółową pomoc `azcmagent reconnect -h`dotyczącą parametru **Ponowne łączenie,** wpisz . 

* **-v lub --verbose** - Włącz rejestrowanie pełne

Połącz , **Connect** **Rozłącz**i **Połącz ponownie** ręcznie po zalogowaniu się interaktywnie lub zautomatyzować przy użyciu tej samej jednostki usługi, która była używana do dołączania wielu agentów lub za pomocą [tokenu dostępu do](../../active-directory/develop/access-tokens.md)platformy tożsamości firmy Microsoft. Jeśli nie użyto jednostki usługi do zarejestrowania komputera w usłudze Azure Arc dla serwerów (w wersji zapoznawczej), zobacz poniższy [artykuł,](onboard-service-principal.md#create-a-service-principal-for-onboarding-at-scale) aby utworzyć jednostkę usługi.

### <a name="connect"></a>Połącz

Ten parametr określa zasób w usłudze Azure Resource Manager reprezentujący komputer jest tworzony na platformie Azure. Zasób znajduje się w określonej grupie subskrypcji i zasobów, a dane `--location` dotyczące komputera są przechowywane w regionie platformy Azure określonym przez to ustawienie. Domyślna nazwa zasobu jest nazwą hosta tego komputera, jeśli nie została określona.

Certyfikat odpowiadający tożsamości urządzenia przypisanemu przez system jest następnie pobierany i przechowywany lokalnie. Po wykonaniu tego kroku usługa metadanych połączonego komputera platformy Azure i agent konfiguracji gościa rozpoczynają synchronizację z programem Azure Arc dla serwerów (wersja zapoznawcza).

Aby połączyć się przy użyciu jednostki usługi, uruchom następujące polecenie:

`azcmagent connect --service-principal-id <serviceprincipalAppID> --service-principal-secret <serviceprincipalPassword> --tenant-id <tenantID> --subscription-id <subscriptionID> --resource-group <ResourceGroupName> --location <resourceLocation>`

Aby połączyć się przy użyciu tokenu dostępu, uruchom następujące polecenie:

`azcmagent connect --access-token <> --subscription-id <subscriptionID> --resource-group <ResourceGroupName> --location <resourceLocation>`

Aby połączyć się z podwyższonym poziomem uprawnień do logowania (interaktywne), uruchom następujące polecenie:

`azcmagent connect --tenant-id <TenantID> --subscription-id <subscriptionID> --resource-group <ResourceGroupName> --location <resourceLocation>`

### <a name="disconnect"></a>Rozłącz

Ten parametr określa zasób w usłudze Azure Resource Manager reprezentujący komputer jest usuwany na platformie Azure. Nie usuwa agenta z komputera, należy to zrobić jako osobny krok. Po odłączeniu komputera, jeśli chcesz ponownie zarejestrować go za pomocą usługi `azcmagent connect` Azure Arc dla serwerów (w wersji zapoznawczej), należy użyć, aby utworzyć dla niego nowy zasób na platformie Azure.

Aby rozłączyć się przy użyciu jednostki usługi, uruchom następujące polecenie:

`azcmagent disconnect --service-principal-id <serviceprincipalAppID> --service-principal-secret <serviceprincipalPassword> --tenant-id <tenantID>`

Aby rozłączyć się za pomocą tokenu dostępu, uruchom następujące polecenie:

`azcmagent disconnect --access-token <accessToken>`

Aby odłączyć się od z podwyższonym poziomem uprawnień do logowania (interakcyjne), uruchom następujące polecenie:

`azcmagent disconnect --tenant-id <tenantID>`

### <a name="reconnect"></a>Ponowne łączenie

Ten parametr ponownie łączy już zarejestrowany lub podłączony komputer z usługą Azure Arc dla serwerów (wersja zapoznawcza). Może to być konieczne, jeśli urządzenie zostało wyłączone, co najmniej 45 dni, aby jego certyfikat wygaśnie. Ten parametr używa opcji uwierzytelniania podanych w celu pobrania nowych poświadczeń odpowiadających zasobowi usługi Azure Resource Manager reprezentującym ten komputer.

To polecenie wymaga wyższych uprawnień niż rola [dołączania połączonego komputera platformy Azure.](overview.md#required-permissions)

Aby ponownie połączyć się przy użyciu jednostki usługi, uruchom następujące polecenie:

`azcmagent reconnect --service-principal-id <serviceprincipalAppID> --service-principal-secret <serviceprincipalPassword> --tenant-id <tenantID>`

Aby ponownie połączyć się przy użyciu tokenu dostępu, uruchom następujące polecenie:

`azcmagent reconnect --access-token <accessToken>`

Aby ponownie połączyć się z podwyższonym poziomem uprawnień do logowania (interaktywne), uruchom następujące polecenie:

`azcmagent reconnect --tenant-id <tenantID>`

## <a name="remove-the-agent"></a>Usuń agenta

Wykonaj jedną z następujących metod, aby odinstalować agenta podłączonego komputera systemu Windows lub Linux z komputera. Usunięcie agenta nie powoduje wyrejestrowania komputera za pomocą systemu Arc dla serwerów (wersja zapoznawcza), jest to oddzielny proces, który wykonujesz, gdy nie trzeba już zarządzać komputerem na platformie Azure.

### <a name="windows-agent"></a>Agent systemu Windows

Obie następujące metody usuwają agenta, ale nie usuwają folderu *C:\Program Files\AzureConnectedMachineAgent* na komputerze.

#### <a name="uninstall-from-control-panel"></a>Odinstalowywanie z Panelu sterowania

1. Aby odinstalować agenta systemu Windows z urządzenia, wykonaj następujące czynności:

    a. Zaloguj się do komputera przy użyciu konta z uprawnieniami administratora.  
    b. W **Panelu sterowania**wybierz pozycję Programy i **funkcje**.  
    d. W **obszarze Programy i funkcje**wybierz pozycję Agent **połączonego komputera platformy Azure**wybierz pozycję **Odinstaluj**, a następnie wybierz pozycję **Tak**.  

    >[!NOTE]
    > Kreator konfiguracji agenta można również uruchomić, klikając dwukrotnie pakiet **instalatora AzureConnectedMachineAgent.msi.**

#### <a name="uninstall-from-the-command-line"></a>Odinstalowywanie z wiersza polecenia

Aby odinstalować agenta ręcznie z wiersza polecenia lub użyć metody zautomatyzowanej, takiej jak skrypt, można użyć poniższego przykładu. Najpierw należy pobrać kod produktu, który jest identyfikatorem GUID, który jest głównym identyfikatorem pakietu aplikacji, z systemu operacyjnego. Odinstalowanie odbywa się za pomocą wiersza polecenia `msiexec /x {Product Code}`Msiexec.exe - .
    
1. Otwórz Edytor rejestru.

2. W obszarze `HKEY_LOCAL_MACHINE\Software\Microsoft\Windows\CurrentVersion\Uninstall`klucz rejestru poszukaj i skopiuj identyfikator GUID kodu produktu.

3. Następnie można odinstalować agenta przy użyciu msiexec przy użyciu następujących przykładów:

   * Od typu wiersza polecenia:

       ```dos
       msiexec.exe /x {product code GUID} /qn
       ```

   * Te same kroki można wykonać za pomocą programu PowerShell:

       ```powershell
       Get-ChildItem -Path HKLM:\Software\Microsoft\Windows\CurrentVersion\Uninstall | `
       Get-ItemProperty | `
       Where-Object {$_.DisplayName -eq "Azure Connected Machine Agent"} | `
       ForEach-Object {MsiExec.exe /x "$($_.PsChildName)" /qn}
       ```

### <a name="linux-agent"></a>Agent Linuksa

> [!NOTE]
> Aby odinstalować agenta, musisz mieć uprawnienia dostępu *głównego* lub konto, które ma podwyższone prawa przy użyciu sudo.

Aby odinstalować agenta Linuksa, polecenie do użycia zależy od systemu operacyjnego Linux.

- W przypadku Ubuntu uruchom następujące polecenie:

    ```bash
    sudo apt purge azcmagent
    ```

- W przypadku RHEL, CentOS i Amazon Linux uruchom następujące polecenie:

    ```bash
    sudo yum remove azcmagent
    ```

- W przypadku SLES uruchom następujące polecenie:

    ```bash
    sudo zypper remove azcmagent
    ```

## <a name="unregister-machine"></a>Wyrejestrować maszynę

Jeśli planujesz zatrzymać zarządzanie komputerem za pomocą usług pomocniczych na platformie Azure, wykonaj następujące kroki, aby wyrejestrować komputer za pomocą funkcji Arc dla serwerów (wersja zapoznawcza). Można wykonać te kroki przed lub po usunięciu agenta podłączonego komputera z komputera.

1. Otwórz usługę Azure Arc dla serwerów (wersja zapoznawcza), przechodząc do [witryny Azure portal](https://aka.ms/hybridmachineportal).

2. Wybierz maszynę z listy, wybierz wielokropek (**...**), a następnie wybierz pozycję **Usuń**.
