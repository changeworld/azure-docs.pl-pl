---
title: Wdrażanie synchronizacji plików Azure (wersja zapoznawcza) | Dokumentacja firmy Microsoft
description: Dowiedz się, jak wdrożyć Azure plik synchronizacji od początku do końca.
services: storage
documentationcenter: ''
author: wmgries
manager: aungoo
editor: tamram
ms.assetid: 297f3a14-6b3a-48b0-9da4-db5907827fb5
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/05/2017
ms.author: wgries
ms.openlocfilehash: f1230cbc4d654bfb59bb328ed7d75c6fa76ff10c
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/19/2018
ms.locfileid: "36268248"
---
# <a name="deploy-azure-file-sync-preview"></a>Wdrażanie synchronizacji plików Azure (wersja zapoznawcza)
Umożliwia synchronizacji plików Azure (wersja zapoznawcza) scentralizowanie udziałów plików w organizacji w plikach Azure, przy zachowaniu elastyczności, wydajności i zgodności serwera plików lokalnych. Synchronizacja programu Azure pliku przy użyciu systemu Windows Server do szybkiego pamięci podręcznej udziału plików na platformę Azure. Można użyć każdego protokołu, który jest dostępny w systemie Windows Server dostępu do danych lokalnie, w tym protokołu SMB, systemu plików NFS i FTPS. Może mieć dowolną liczbę pamięci podręcznych zgodnie z potrzebami na całym świecie.

Zdecydowanie zaleca się przeczytanie [planowania wdrożenia usługi pliki Azure](storage-files-planning.md) i [planowania wdrożenia synchronizacji plików Azure](storage-sync-files-planning.md) przed wykonaniem kroków opisanych w tym artykule.

## <a name="prerequisites"></a>Wymagania wstępne
* Konto usługi Azure storage i Azure udziału plików w tym samym regionie, który chcesz wdrożyć synchronizacji plików Azure. Aby uzyskać więcej informacji, zobacz:
    - [Dostępność w danym regionie](storage-sync-files-planning.md#region-availability) synchronizacji plików usługi Azure.
    - [Utwórz konto magazynu](../common/storage-create-storage-account.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json) krok opis sposobu tworzenia konta magazynu.
    - [Tworzenie udziału plików](storage-how-to-create-file-share.md) krok opis sposobu tworzenia udziału plików.
* Co najmniej jedno wystąpienie obsługiwanych systemu Windows Server lub klastra systemu Windows Server do synchronizacji z usługą Azure synchronizacji plików. Aby uzyskać więcej informacji o obsługiwanych wersjach systemu Windows Server, zobacz [współdziałanie z systemem Windows Server](storage-sync-files-planning.md#azure-file-sync-interoperability).
* Upewnij się, że zainstalowano programu PowerShell w wersji 5.1 w systemie Windows Server. Jeśli używasz systemu Windows Server 2012 R2, upewnij się, że uruchamiasz co najmniej 5.1 programu PowerShell. \*. Można bezpiecznie pominąć sprawdzanie w systemie Windows Server 2016 jako 5.1 programu PowerShell jest domyślna wersja out-of-box. W systemie Windows Server 2012 R2 można sprawdzić, czy korzystasz z programu PowerShell w wersji 5.1. \* sprawdzając wartość **PSVersion** właściwość **$PSVersionTable** obiektu:

    ```PowerShell
    $PSVersionTable.PSVersion
    ```

    Jeśli wartość PSVersion jest mniejsza niż 5.1. \*, jak będzie w przypadku większości nowej instalacji systemu Windows Server 2012 R2, można łatwo przeprowadzić uaktualnienie przez pobranie i zainstalowanie [Windows Management Framework (WMF) 5.1](https://www.microsoft.com/download/details.aspx?id=54616). Odpowiedniego pakietu do pobrania i zainstalowania systemu Windows Server 2012 R2 jest **Win8.1AndW2K12R2 KB\*\*\*\*\*\*\*-x64.msu**.

    > [!Note]  
    > Synchronizacja programu Azure plik jeszcze nie obsługuje programu PowerShell 6 w systemie Windows Server 2012 R2 lub Windows Server 2016.
* Moduł AzureRM PowerShell na serwerach, które chcesz korzystać z synchronizacji plików Azure. Aby uzyskać więcej informacji na temat instalowania modułów programu AzureRM PowerShell, zobacz [Instalowanie i konfigurowanie programu Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps). Zaleca się zawsze przy użyciu najnowszej wersji modułów programu Azure PowerShell. 

## <a name="prepare-windows-server-to-use-with-azure-file-sync"></a>Przygotowanie systemu Windows Server do użycia z synchronizacji plików Azure
Dla każdego serwera, który ma być używany z synchronizacji plików Azure, w tym każdy węzeł serwera w klastrze trybu Failover należy wyłączyć **Konfiguracja zwiększonych zabezpieczeń programu Internet Explorer**. Jest to wymagane tylko w przypadku rejestracji serwera początkowego. Możesz je ponownie włączyć po zarejestrowaniu serwera.

# <a name="portaltabportal"></a>[Portal](#tab/portal)
1. Otwórz Menedżera serwera.
2. Kliknij przycisk **lokalnego serwera**:  
    !["Serwer lokalny" po lewej stronie interfejsu użytkownika Menedżera serwera](media/storage-sync-files-deployment-guide/prepare-server-disable-IEESC-1.PNG)
3. Na **właściwości** subpane, wybierz link **Konfiguracja zwiększonych zabezpieczeń**.  
    ![W okienku "Konfiguracja zwiększonych zabezpieczeń" w Interfejsie użytkownika Menedżera serwera](media/storage-sync-files-deployment-guide/prepare-server-disable-IEESC-2.PNG)
4. W **Konfiguracja zwiększonych zabezpieczeń programu Internet Explorer** okno dialogowe, wybierz opcję **poza** dla **Administratorzy** i **użytkowników**:  
    ![Wybrana konfiguracja zwiększonych zabezpieczeń programu Internet Explorer pop okna z "Off"](media/storage-sync-files-deployment-guide/prepare-server-disable-IEESC-3.png)

# <a name="powershelltabpowershell"></a>[Program PowerShell](#tab/powershell)
Aby wyłączyć konfigurację zwiększonych zabezpieczeń programu Internet Explorer, wykonaj następujące czynności, w sesji programu PowerShell z podwyższonym poziomem uprawnień:

```PowerShell
# Disable Internet Explorer Enhanced Security Configuration 
# for Administrators
Set-ItemProperty -Path "HKLM:\SOFTWARE\Microsoft\Active Setup\Installed Components\{A509B1A7-37EF-4b3f-8CFC-4F3A74704073}" -Name "IsInstalled" -Value 0 -Force

# Disable Internet Explorer Enhanced Security Configuration 
# for Users
Set-ItemProperty -Path "HKLM:\SOFTWARE\Microsoft\Active Setup\Installed Components\{A509B1A8-37EF-4b3f-8CFC-4F3A74704073}" -Name "IsInstalled" -Value 0 -Force

# Force Internet Explorer closed, if open. This is required to fully apply the setting.
# Save any work you have open in the IE browser. This will not affect other browsers,
# including Edge.
Stop-Process -Name iexplore -ErrorAction SilentlyContinue
``` 

---

## <a name="install-the-azure-file-sync-agent"></a>Zainstaluj agenta synchronizacji plików Azure
Agent synchronizacji plików Azure jest pobrania pakietu, który umożliwia systemu Windows Server można synchronizować z udziałem plików na platformę Azure. 

# <a name="portaltabportal"></a>[Portal](#tab/portal)
Możesz pobrać agenta z [Microsoft Download Center](https://go.microsoft.com/fwlink/?linkid=858257). Po zakończeniu pobierania kliknij dwukrotnie pakiet MSI do rozpoczęcia instalacji agenta synchronizacji plików Azure.

> [!Important]  
> Jeśli zamierzasz używać synchronizacji plików Azure z klastrem trybu Failover, musi być zainstalowany agent synchronizacji plików Azure w na każdym węźle w klastrze.

Zaleca się wykonanie następujących czynności:
- Pozostaw domyślnej ścieżki instalacji (C:\Program Files\Azure\StorageSyncAgent), aby uprościć zarządzanie Rozwiązywanie problemów i serwera.
- Włączyć usługę Microsoft Update aktualności synchronizacji plików Azure. Wszystkie aktualizacje agenta synchronizacji plików Azure, w tym aktualizacje funkcji i poprawek, wystąpić z witryny Microsoft Update. Zaleca się zainstalowanie najnowszej aktualizacji do synchronizacji plików Azure. Aby uzyskać więcej informacji, zobacz [zasady aktualizacji synchronizacji plików Azure](storage-sync-files-planning.md#azure-file-sync-agent-update-policy).

Po zakończeniu instalacji agenta synchronizacji plików Azure, interfejsu użytkownika rejestracji serwera jest automatycznie otwiera. Musi być zainstalowana usługa synchronizacji magazynu, przed registrating; zobacz następną sekcję na temat tworzenia magazynu usługi synchronizacji.

# <a name="powershelltabpowershell"></a>[Program PowerShell](#tab/powershell)
Wykonaj następujący kod programu PowerShell, Pobierz odpowiednią wersję agenta synchronizacji plików Azure zawiera system operacyjny i zainstalować go w tym systemie.

```PowerShell
# Gather the OS version
$osver = [System.Environment]::OSVersion.Version

# Download the appropriate version of the Azure File Sync agent for your OS.
if ($osver.Equals([System.Version]::new(10, 0, 14393, 0))) {
    Invoke-WebRequest `
        -Uri https://go.microsoft.com/fwlink/?linkid=875004 `
        -OutFile "StorageSyncAgent.exe" 
}
elseif ($osver.Equals([System.Version]::new(6, 3, 9600, 0))) {
    Invoke-WebRequest `
        -Uri https://go.microsoft.com/fwlink/?linkid=875002 `
        -OutFile "StorageSyncAgent.exe" 
}
else {
    throw [System.PlatformNotSupportedException]::new("Azure File Sync is only supported on Windows Server 2012 R2 and Windows Server 2016")
}

# Extract the MSI from the install package
$tempFolder = New-Item -Path "afstemp" -ItemType Directory
Start-Process -FilePath ".\StorageSyncAgent.exe" -ArgumentList "/C /T:$tempFolder" -Wait

# Install the MSI. Start-Process is used to PowerShell blocks until the operation is complete.
# Note that the installer currently forces all PowerShell sessions closed - this is a known issue.
Start-Process -FilePath "$($tempFolder.FullName)\StorageSyncAgent.msi" -ArgumentList "/quiet" -Wait

# Note that this cmdlet will need to be run in a new session based on the above comment.
# You may remove the temp folder containing the MSI and the EXE installer
Remove-Item -Path ".\StorageSyncAgent.exe", ".\afstemp" -Recurse -Force
```

---

## <a name="deploy-the-storage-sync-service"></a>Wdrażanie usługi synchronizacji magazynu 
Wdrażanie synchronizacji plików Azure rozpoczyna się od wprowadzania **Usługa synchronizacji magazynu** zasobów w grupie zasobów wybranej subskrypcji. Zaleca się udostępniania jako kilka z nich, zgodnie z potrzebami. Utworzysz relację zaufania między serwerami i tego zasobu i serwer mogą być rejestrowane tylko do jednej usługi synchronizacji magazynu. W związku z tym zaleca się wdrożenie tyle usług synchronizacji magazynu potrzebnych do oddzielnych grup serwerów. Należy pamiętać, że serwery z innego magazynu usługi synchronizacji nie można zsynchronizować ze sobą.

> [!Note]
> Usługa synchronizacji magazynu dziedziczone uprawnienia dostępu z subskrypcji i grupy zasobów, które zostało wdrożone w. Zaleca się uważnie sprawdzanie kto ma dostęp do niego. Jednostki z dostępu do zapisu można uruchomić synchronizację nowych zestawów plików z serwerów zarejestrowanych tego magazynu Usługa synchronizacji i spowodować, że przepływ danych do magazynu Azure, który jest dostępny do nich.

# <a name="portaltabportal"></a>[Portal](#tab/portal)
Aby wdrożyć usługę synchronizacji magazynu, przejdź do [portalu Azure](https://portal.azure.com/), kliknij przycisk *nowy* , a następnie wyszukaj synchronizacji plików Azure. W wynikach wyszukiwania wybierz **synchronizacji plików Azure (wersja zapoznawcza)**, a następnie wybierz **Utwórz** otworzyć **Wdrażanie synchronizacji magazynu** kartę.

W okienku otwartym wprowadź następujące informacje:

- **Nazwa**: unikatową nazwę usługi synchronizacji magazynu (na subskrypcję).
- **Subskrypcja**: subskrypcji, w którym chcesz utworzyć usługę synchronizacji magazynu. W zależności od strategii konfiguracji w organizacji możesz mieć dostępu do co najmniej jedna subskrypcja. Subskrypcja platformy Azure jest najbardziej podstawową kontenera rozliczeń dla każdej usługi w chmurze (takich jak pliki Azure).
- **Grupa zasobów**: Grupa zasobów to logiczne grupy zasobów platformy Azure, takich jak konta magazynu lub usługa synchronizacji magazynu. Można utworzyć nową grupę zasobów lub użyć istniejącej grupy zasobów dla synchronizacji plików Azure. (Zalecamy używanie grup zasobów jako kontenery do izolowania zasobów logicznie dla organizacji, takich jak grupowanie HR zasobów lub zasobów dla określonego projektu.)
- **Lokalizacja**: regionu, w której chcesz wdrożyć synchronizacji plików Azure. Tylko obsługiwane regiony są dostępne na tej liście.

Gdy skończysz, wybierz **Utwórz** wdrożenie usługi synchronizacji magazynu.

# <a name="powershelltabpowershell"></a>[Program PowerShell](#tab/powershell)
Przed interakcji z poleceń cmdlet do zarządzania synchronizacji plików Azure, musisz zaimportować biblioteki DLL i utworzyć kontekstu zarządzania synchronizacji plików Azure. Jest to wymagane, ponieważ poleceń cmdlet do zarządzania Azure plik synchronizacji nie są jeszcze częścią tego modułu programu PowerShell w usłudze AzureRM.

> [!Note]  
> Pakiet StorageSync.Management.PowerShell.Cmdlets.dll, który zawiera polecenia cmdlet do zarządzania synchronizacji plików Azure, (celowo) zawiera polecenie cmdlet z niezatwierdzonych zlecenie (`Login`). Nazwa `Login-AzureRmStorageSync` został wybrany do dopasowania `Login-AzureRmAccount` alias polecenia cmdlet w module środowiska AzureRM PowerShell. Ten komunikat o błędzie (i polecenia cmdlet) zostaną usunięte agent synchronizacji plików Azure jest dodawany do modułu środowiska AzureRM PowerShell.

```PowerShell
$acctInfo = Login-AzureRmAccount

# The location of the Azure File Sync Agent. If you have installed the Azure File Sync 
# agent to a non-standard location, please update this path.
$agentPath = "C:\Program Files\Azure\StorageSyncAgent"

# Import the Azure File Sync management cmdlets
Import-Module "$agentPath\StorageSync.Management.PowerShell.Cmdlets.dll"

# this variable stores your subscription ID 
# get the subscription ID by logging onto the Azure portal
$subID = $acctInfo.Context.Subscription.Id

# this variable holds your Azure Active Directory tenant ID
# use Login-AzureRMAccount to get the ID from that context
$tenantID = $acctInfo.Context.Tenant.Id

# this variable holds the Azure region you want to deploy 
# Azure File Sync into
$region = '<Az_Region>'

# Check to ensure Azure File Sync is available in the selected Azure
# region.
$regions = @()
Get-AzureRmLocation | ForEach-Object { 
    if ($_.Providers -contains "Microsoft.StorageSync") { 
        $regions += $_.Location 
    } 
}

if ($regions -notcontains $region) {
    throw [System.Exception]::new("Azure File Sync is either not available in the selected Azure Region or the region is mistyped.")
}

# the resource group to deploy the Storage Sync Service into
$resourceGroup = '<RG_Name>'

# Check to ensure resource group exists and create it if doesn't
$resourceGroups = @()
Get-AzureRmResourceGroup | ForEach-Object { 
    $resourceGroups += $_.ResourceGroupName 
}

if ($resourceGroups -notcontains $resourceGroup) {
    New-AzureRmResourceGroup -Name $resourceGroup -Location $region
}

# the following command creates an AFS context 
# it enables subsequent AFS cmdlets to be executed with minimal 
# repetition of parameters or separate authentication 
Login-AzureRmStorageSync `
    –SubscriptionId $subID `
    -ResourceGroupName $resourceGroup `
    -TenantId $tenantID `
    -Location $region
```

Po utworzeniu kontekstu synchronizacji plików Azure z `Login-AzureRmStorageSync` polecenia cmdlet, można utworzyć magazynu usługi synchronizacji. Pamiętaj zastąpić `<my-storage-sync-service>` z żądaną nazwą usługi synchronizacji magazynu.

```PowerShell
$storageSyncName = "<my-storage-sync-service>"
New-AzureRmStorageSyncService -StorageSyncServiceName $storageSyncName
```

---

## <a name="register-windows-server-with-storage-sync-service"></a>Rejestr systemu Windows Server z magazynu usługi synchronizacji
Rejestrowanie serwera z systemem Windows za pomocą usługi synchronizacji magazynu ustanawia relację zaufania między serwerem (lub klastra) i usługi synchronizacji magazynu. Serwer może być zarejestrowany tylko do jednej usługi synchronizacji magazynu i można synchronizować z innymi serwerami i Azure udziały skojarzone z tej samej usługi synchronizacji magazynu plików.

> [!Note]
> Rejestracja serwera używa poświadczenia platformy Azure w celu utworzenia relacji zaufania między usługą synchronizacji magazynu i serwera z systemem Windows, jednak następnie serwer tworzy i korzysta z własnym tożsamości, która jest prawidłowa, tak długo, jak serwer pozostaje zarejestrowane i tokenu bieżącego sygnatura dostępu współdzielonego (SAS magazynu) jest nieprawidłowa. Nie można wystawić nowy token sygnatury dostępu Współdzielonego na serwerze, gdy serwer jest zarejestrowany, w związku z tym usuwanie serwera umożliwia dostęp do udziałów plików na platformę Azure zatrzymanie wszelkich synchronizacji.

# <a name="portaltabportal"></a>[Portal](#tab/portal)
Interfejs użytkownika rejestracji serwera należy otwierać automatycznie po instalacji agenta synchronizacji plików Azure. Jeśli nie, można otworzyć go ręcznie z lokalizacji pliku: C:\Program Files\Azure\StorageSyncAgent\ServerRegistration.exe. Po uruchomieniu interfejsu użytkownika rejestracji serwera wybierz **logowania** do rozpoczęcia.

Po zalogowaniu, zostanie wyświetlony monit o następujące informacje:

![Zrzut ekranu interfejsu użytkownika serwera rejestracji](media/storage-sync-files-deployment-guide/register-server-scubed-1.png)

- **Subskrypcja platformy Azure**: subskrypcji, który zawiera usługę synchronizacji magazynu (zobacz [wdrożyć usługę synchronizacji magazynu](#deploy-the-storage-sync-service)). 
- **Grupa zasobów**: grupy zasobów, który zawiera usługę synchronizacji magazynu.
- **Usługa synchronizacji magazynu**: Nazwa usługi synchronizacji magazynu, z którym chcesz zarejestrować.

Po wybraniu odpowiednie informacje, wybierz **zarejestrować** do ukończenia rejestracji serwera. W ramach procesu rejestracji zostanie wyświetlony monit o dodatkowe logowania.

# <a name="powershelltabpowershell"></a>[Program PowerShell](#tab/powershell)
```PowerShell
$registeredServer = Register-AzureRmStorageSyncServer -StorageSyncServiceName $storageSyncName
```

---

## <a name="create-a-sync-group-and-a-cloud-endpoint"></a>Tworzenie grupy synchronizacji i punktu końcowego w chmurze
Grupy synchronizacji definiuje topologia synchronizacji dla grupy plików. Punkty końcowe w ramach grupy synchronizacji są zsynchronizowane ze sobą. Grupy synchronizacji musi zawierać co najmniej jedna chmura punktu końcowego, który reprezentuje udział plików na platformę Azure i jedną lub punktów końcowych serwera. Punkt końcowy serwera reprezentuje ścieżkę na zarejestrowanego serwera. Serwer może mieć punkty końcowe serwera w wielu grupach synchronizacji. Można utworzyć dowolną liczbę grup synchronizacji należy do odpowiednio opisują topologii żądaną synchronizacji.

Punkt końcowy w chmurze jest wskaźnikiem do udziału plików na platformę Azure. Wszystkie punkty końcowe serwera zsynchronizuje się z punktu końcowego w chmurze, co koncentratora końcowego w chmurze. Konto magazynu dla udziału plików na platformę Azure musi znajdować się w tym samym regionie co magazyn usługi synchronizacji. Zostanie on zsynchronizowany całości udziału plików na platformę Azure z jednym wyjątkiem: będą udostępniane folderu specjalnego porównywalne do folderu "System Volume Information" ukryte na woluminie NTFS. Ten katalog jest nazywany ". SystemShareInformation". Zawiera on istotne synchronizacji metadanych, które zostanie zsynchronizowany z innymi punktami końcowymi. Nie używać ani nie usuwaj go!

> [!Important]  
> Można wprowadzić zmiany do dowolnego punktu końcowego w chmurze lub punkt końcowy serwera w grupie synchronizacji i zsynchronizowaniu pliki do punktów końcowych w grupie synchronizacji. Jeśli bezpośrednio wprowadzić zmianę do punktu końcowego w chmurze (udział plików na platformę Azure), zmiany najpierw trzeba być odnajdowane przez zadanie wykrywania zmian synchronizacji plików Azure. Zadanie wykrywania zmian jest inicjowane dla punktu końcowego w chmurze tylko raz na 24 godziny. Aby uzyskać więcej informacji, zobacz [plików Azure — często zadawane pytania](storage-files-faq.md#afs-change-detection).

# <a name="portaltabportal"></a>[Portal](#tab/portal)
Aby utworzyć grupę synchronizacji w [portalu Azure](https://portal.azure.com/), przejdź do usługi synchronizacji magazynu, a następnie wybierz **+ grupy synchronizacji**:

![Utwórz nową grupę synchronizacji w portalu Azure](media/storage-sync-files-deployment-guide/create-sync-group-1.png)

W okienku zostanie otwarty wprowadź następujące informacje, aby utworzyć grupę synchronizacji z punktu końcowego w chmurze:

- **Nazwa grupy synchronizacji**: Nazwa grupy synchronizacji ma zostać utworzony. Ta nazwa musi być unikatowa w ramach usługi synchronizacji magazynu, ale może być dowolną nazwę logiczną dla Ciebie.
- **Subskrypcja**: subskrypcji, w której wdrożono usługę synchronizacji magazynu w [wdrożyć usługę synchronizacji magazynu](#deploy-the-storage-sync-service).
- **Konto magazynu**: w przypadku wybrania **wybierz konto magazynu**, innego okienka pojawia się, w którym można wybrać konto magazynu, który ma udziału plików na platformę Azure, do którego mają być synchronizowane z.
- **Udział plików na platformę Azure**: Nazwa udziału plików na platformę Azure, z którym mają być synchronizowane.

# <a name="powershelltabpowershell"></a>[Program PowerShell](#tab/powershell)
Aby utworzyć grupę synchronizacji, należy wykonać następujące środowiska PowerShell. Pamiętaj, aby zastąpić `<my-sync-group>` z żądaną nazwą grupy synchronizacji.

```PowerShell
$syncGroupName = "<my-sync-group>"
New-AzureRmStorageSyncGroup -SyncGroupName $syncGroupName -StorageSyncService $storageSyncName
```

Po pomyślnym utworzeniu grupy synchronizacji można utworzyć punktu końcowego w chmurze. Pamiętaj zastąpić `<my-storage-account>` i `<my-file-share>` z oczekiwanych wartości.

```PowerShell
# Get or create a storage account with desired name
$storageAccountName = "<my-storage-account>"
$storageAccount = Get-AzureRmStorageAccount -ResourceGroupName $resourceGroup | Where-Object {
    $_.StorageAccountName -eq $storageAccountName
}

if ($storageAccount -eq $null) {
    $storageAccount = New-AzureRmStorageAccount `
        -Name $storageAccountName `
        -ResourceGroupName $resourceGroup `
        -Location $region `
        -SkuName Standard_LRS `
        -Kind StorageV2 `
        -EnableHttpsTrafficOnly:$true
}

# Get or create an Azure file share within the desired storage account
$fileShareName = "<my-file-share>"
$fileShare = Get-AzureStorageShare -Context $storageAccount.Context | Where-Object {
    $_.Name -eq $fileShareName -and $_.IsSnapshot -eq $false
}

if ($fileShare -eq $null) {
    $fileShare = New-AzureStorageShare -Context $storageAccount.Context -Name $fileShareName
}

# Create the cloud endpoint
New-AzureRmStorageSyncCloudEndpoint `
    -StorageSyncServiceName $storageSyncName `
    -SyncGroupName $syncGroupName ` 
    -StorageAccountResourceId $storageAccount.Id
    -StorageAccountShareName $fileShare.Name
```

---

## <a name="create-a-server-endpoint"></a>Tworzenie punktu końcowego serwera
Punkt końcowy serwera reprezentuje konkretnej lokalizacji na zarejestrowanego serwera, takie jak folder na serwerze woluminu. Punkt końcowy serwera musi być ścieżką zarejestrowanego serwera (zamiast zainstalowany udział), a aby korzystać z chmury, dodając funkcje warstw, ścieżka musi być na woluminie z systemem innym niż system. Magazynu dołączone do sieci (NAS) nie jest obsługiwana.

# <a name="portaltabportal"></a>[Portal](#tab/portal)
Aby dodać punktu końcowego serwera, przejdź do grupy nowo utworzonych sync, a następnie wybierz **dodać punkt końcowy serwera**.

![Dodaj nowy punkt końcowy serwera w okienku grupy synchronizacji](media/storage-sync-files-deployment-guide/create-sync-group-2.png)

W **dodać punkt końcowy serwera** okienku, wprowadź następujące informacje, aby utworzyć punktu końcowego serwera:

- **Zarejestrowanego serwera**: Nazwa serwera lub klastra, w której chcesz utworzyć punkt końcowy serwera.
- **Ścieżka**: ścieżka systemu Windows Server można synchronizować w ramach grupy synchronizacji.
- **Obsługa poziomów w chmurze**: przełącznik, aby włączyć lub wyłączyć chmury warstwy. Z chmurą, dodając funkcje warstw, rzadko używane lub uzyskać dostępu do plików może należeć do warstwy do usługi pliki Azure.
- **Wolne miejsce w woluminie**: ilość wolnego miejsca do zarezerwowania na woluminie, na którym znajduje się punkt końcowy serwera. Na przykład jeśli wolne miejsce w woluminie jest ustawiona na 50% na wolumin, który ma punkt końcowy pojedynczego serwera, około połowa ilości danych jest warstwowa do usługi pliki Azure. Niezależnie od tego, czy w chmurze, dodając funkcje warstw jest włączona, na udział plików na platformę Azure ma zawsze pełną kopię danych do grupy synchronizacji.

Aby dodać punkt końcowy serwera, wybierz **Utwórz**. Pliki są teraz zsynchronizowane na udział plików na platformę Azure i w systemie Windows Server. 

# <a name="powershelltabpowershell"></a>[Program PowerShell](#tab/powershell)
Uruchom następujące polecenia programu PowerShell do tworzenia serwera punktu końcowego i pamiętaj zastąpić `<your-server-endpoint-path>` i `<your-volume-free-space>` z odpowiednie wartości.

```PowerShell
$serverEndpointPath = "<your-server-endpoint-path>"
$cloudTieringDesired = $true
$volumeFreeSpacePercentage = <your-volume-free-space>

if ($cloudTieringDesired) {
    # Ensure endpoint path is not the system volume
    $directoryRoot = [System.IO.Directory]::GetDirectoryRoot($serverEndpointPath)
    $osVolume = "$($env:SystemDrive)\"
    if ($directoryRoot -eq $osVolume) {
        throw [System.Exception]::new("Cloud tiering cannot be enabled on the system volume")
    }

    # Create server endpoint
    New-AzureRmStorageSyncServerEndpoint `
        -StorageSyncServiceName $storageSyncName `
        -SyncGroupName $syncGroupName `
        -ServerId $registeredServer.Id `
        -ServerLocalPath $serverEndpointPath `
        -CloudTiering $true `
        -VolumeFreeSpacePercent $volumeFreeSpacePercentage
}
else {
    # Create server endpoint
    New-AzureRmStorageSyncServerEndpoint `
        -StorageSyncServiceName $storageSyncName `
        -SyncGroupName $syncGroupName `
        -ServerId $registeredServer.Id `
        -ServerLocalPath $serverEndpointPath 
}
```

---

## <a name="onboarding-with-azure-file-sync"></a>Dołączania z synchronizacją plików na platformę Azure
Zalecane kroki do dołączenia na synchronizacji plików Azure w pierwszym z zero przestoju przy zachowaniu całego pliku wierności i listy kontroli dostępu (ACL) są następujące:
 
1. Wdrażanie usługi synchronizacji magazynu.
2. Utwórz grupę synchronizacji.
3. Zainstaluj agenta synchronizacji plików Azure na serwerze z pełnym zestawem danych.
4. Zarejestruj tego serwera i utworzyć punktu końcowego serwera w udziale. 
5. Let synchronizacji wykonaj pełny przekazywania do udziału plików na platformę Azure (punktu końcowego w chmurze).  
6. Po zakończeniu przekazywania początkowej synchronizacji plików Azure agenta należy zainstalować na każdej z pozostałych serwerów.
7. Utwórz nowe udziały plików na każdej z pozostałych serwerów.
8. Utwórz punkty końcowe serwera na nowe udziały plików z zasadami obsługi poziomów w chmurze, w razie potrzeby. (Ten krok wymaga dodatkowego magazynu, które mają być dostępne dla konfiguracji początkowej).
9. Let agent synchronizacji plików Azure do szybkiego przywracania pełne przestrzeni nazw bez przenoszenia danych rzeczywistych. Po zakończeniu synchronizacji pełnej przestrzeni nazw aparatu synchronizacji spowoduje wypełnienie miejsce na dysku lokalnym na podstawie zasad warstw chmury dla punktu końcowego serwera. 
10. Upewnij się, synchronizacji kończy i przetestować topologii zgodnie z potrzebami. 
11. Przekieruj użytkowników i aplikacji do tego nowego udziału.
12. Opcjonalnie można usunąć wszystkie zduplikowane udziałów na serwerach.
 
Jeśli nie masz dodatkowe miejsce do magazynowania do dołączenia do początkowej i chcesz dołączyć do istniejących udziałów, można wstępne dodanie danych w udziałach plików platformy Azure. Takie podejście jest zalecane tylko wtedy, gdy mogą akceptować Przestój i absolutnie zagwarantować żadne zmiany danych w udziałach serwera podczas procesu dołączania początkowej. 
 
1. Upewnij się, że dane na żadnym serwerze nie można zmienić podczas procesu dołączania.
2. Wstępnie nasion plików na platformę Azure udostępnia dane serwera przy użyciu dowolnego narzędzia transferu danych za pośrednictwem protokołu SMB np. Robocopy, kopiowania SMB direct. Ponieważ AzCopy nie przekazywanie danych za pośrednictwem protokołu SMB, więc nie może służyć do obsługi wstępnie.
3. Utwórz topologia synchronizacji plików Azure z punktami końcowymi żądanego serwera, wskazując do istniejących udziałów.
4. Pozwolić, aby zakończyć proces uzgadniania dla wszystkich punktów końcowych synchronizacji. 
5. Po zakończeniu uzgadniania możesz otworzyć udziałów zmian.
 
Należy pamiętać, że obecnie wstępnie wstępne wypełnianie podejście ma kilka ograniczeń- 
- Pełnej rozdzielczości na pliki nie są zachowywane. Na przykład pliki utratę listy kontroli dostępu i sygnatur czasowych.
- Zmiany danych na serwerze przed topologia synchronizacji pełni jest uruchomiony i działa może spowodować konflikty w punktach końcowych serwera.  
- Po utworzeniu punktu końcowego w chmurze Azure plik synchronizacji uruchamia proces do wykrywania plików w chmurze przed rozpoczęciem synchronizacji początkowej. Czas potrzebny na zakończenie tego procesu zależy od różnych czynników, takich jak szybkość sieci, dostępnej przepustowości i liczby plików i folderów. Dla nierównej oszacowania w wersji zapoznawczej proces wykrywania uruchamia od 10 plików na sekundę.  W związku z tym nawet wtedy, gdy wstępnie wstępne wypełnianie uruchamia szybkie, to całkowity czas można uzyskać w pełni systemem może być znaczne wydłużenie, gdy dane są wstępnie wprowadzonych w chmurze.

## <a name="migrate-a-dfs-replication-dfs-r-deployment-to-azure-file-sync"></a>Migracja wdrożenia replikacji systemu plików DFS (DFS-R) do synchronizacji plików Azure
Migracja wdrożenia systemu plików DFS-R do synchronizacji usługi Azure pliku:

1. Utwórz grupę synchronizacji do reprezentowania topologii systemu plików DFS-R, który chcesz zastąpić.
2. Uruchom na serwerze, który ma pełny zestaw danych w topologii systemu plików DFS-R, aby przeprowadzić migrację. Instalowanie synchronizacji plików Azure na tym serwerze.
3. Zarejestruj tego serwera i utworzyć punktu końcowego serwera dla pierwszego serwera do migracji. Nie należy włączać chmury warstwy.
4. Poinformuj wszystkich synchronizacji danych do udziału plików na platformę Azure (punktu końcowego w chmurze).
5. Zainstaluj i zarejestruj agenta synchronizacji plików Azure na każdej z pozostałych serwerów systemu plików DFS-R.
6. Wyłącz R. systemu plików DFS 
7. Tworzenie punktu końcowego serwera na wszystkich serwerach systemu plików DFS-R. Nie należy włączać chmury warstwy.
8. Upewnij się, synchronizacji kończy i przetestować topologii zgodnie z potrzebami.
9. Wycofywanie R. systemu plików DFS
10. Chmury warstw może teraz można włączyć dla dowolnego punktu końcowego serwera zgodnie z potrzebami.

Aby uzyskać więcej informacji, zobacz [interop synchronizacji plików Azure z rozproszonego systemu plików (DFS)](storage-sync-files-planning.md#distributed-file-system-dfs).

## <a name="next-steps"></a>Kolejne kroki
- [Dodawanie lub usuwanie punktu końcowego serwera synchronizacji plików Azure](storage-sync-files-server-endpoint.md)
- [Zarejestruj lub Wyrejestruj serwer z synchronizacji plików Azure](storage-sync-files-server-registration.md)