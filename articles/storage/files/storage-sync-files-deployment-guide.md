---
title: Wdrażanie usługi Azure File Sync | Dokumentacja firmy Microsoft
description: Dowiedz się, jak wdrożyć usługę Azure File Sync od początku do końca.
services: storage
author: roygara
ms.service: storage
ms.topic: article
ms.date: 07/19/2018
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 0913e1877c63ed1a8e960676be02a12b45a34a7d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66240092"
---
# <a name="deploy-azure-file-sync"></a>Wdrażanie usługi Azure File Sync
Usługa Azure File Sync umożliwia scentralizowanie udziałów plików Twojej organizacji w usłudze Azure Files przy jednoczesnym zachowaniu elastyczności, wydajności i zgodności lokalnego serwera plików. Usługa Azure File Sync przekształca systemu Windows Server w szybką pamięć podręczną udziału plików platformy Azure. Można użyć dowolnego protokołu, który jest dostępny w systemie Windows Server oraz dostęp do danych lokalnie, w tym protokołu SMB, systemu plików NFS i protokołu FTPS. Może mieć dowolną liczbę pamięci podręcznych potrzebnych na całym świecie.

Zdecydowanie zaleca się przeczytanie [Planowanie wdrożenia usługi Azure Files](storage-files-planning.md) i [Planowanie wdrażania usługi Azure File Sync](storage-sync-files-planning.md) przed wykonaniem kroków opisanych w tym artykule.

## <a name="prerequisites"></a>Wymagania wstępne
* Udział plików platformy Azure w tym samym regionie chcesz wdrożyć usługę Azure File Sync. Aby uzyskać więcej informacji, zobacz:
    - [Dostępność w poszczególnych regionach](storage-sync-files-planning.md#region-availability) dla usługi Azure File Sync.
    - [Utwórz udział plików](storage-how-to-create-file-share.md) krok po kroku opis sposobu tworzenia udziału plików.
* Co najmniej jedno wystąpienie obsługiwany system Windows Server lub klastra systemu Windows Server, można zsynchronizować z usługi Azure File Sync. Aby uzyskać więcej informacji o obsługiwanych wersjach systemu Windows Server, zobacz [współdziałanie z systemem Windows Server](storage-sync-files-planning.md#azure-file-sync-system-requirements-and-interoperability).
* Moduł Az PowerShell mogą być używane, za pomocą programu PowerShell 5.1 lub programu PowerShell 6. Moduł Az PowerShell może używać do usługi Azure File Sync, w dowolnym systemie obsługiwanych, łącznie z systemami innych niż Windows, jednak polecenie cmdlet rejestracji serwera musi zawsze działać w bezpośrednio na wystąpienia systemu Windows Server, który w przypadku rejestracji. W systemie Windows Server 2012 R2, możesz sprawdzić, czy korzystasz z co najmniej 5.1 program PowerShell. \* , analizując wartość **PSVersion** właściwość **$PSVersionTable** obiektu:

    ```powershell
    $PSVersionTable.PSVersion
    ```

    Jeśli wartość PSVersion jest mniejsza niż 5.1. \*, co dzieje się tak w większości świeżej instalacji systemu Windows Server 2012 R2, można łatwo uaktualnić przez pobranie i zainstalowanie [Windows Management Framework (WMF) 5.1](https://www.microsoft.com/download/details.aspx?id=54616). Odpowiedni pakiet do pobrania i zainstalowania systemu Windows Server 2012 R2 jest **Win8.1AndW2K12R2 KB\*\*\*\*\*\*\*-x64.msu**. 

    Program PowerShell 6 + mogą być używane z dowolnym obsługiwanym systemie i można je pobrać za pośrednictwem jego [strony GitHub](https://github.com/PowerShell/PowerShell#get-powershell). 

    > [!Important]  
    > Jeśli planujesz korzystanie z interfejsu użytkownika rejestracji serwera zamiast rejestrowanie bezpośrednio z poziomu programu PowerShell, należy użyć programu PowerShell 5.1.

* Jeśli użytkownik zgodził się używać programu PowerShell 5.1, upewnij się, że na co najmniej .NET 4.7.2 jest zainstalowany. Dowiedz się więcej o [wersje programu .NET Framework i zależności](https://docs.microsoft.com/dotnet/framework/migration-guide/versions-and-dependencies) w Twoim systemie.
* Az modułu programu PowerShell, które można zainstalować zgodnie z instrukcjami w tym miejscu: [Instalowanie i konfigurowanie programu Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-Az-ps). 
* Moduł Az.StorageSync, który jest obecnie zainstalowany niezależnie od moduł Az:

    ```PowerShell
    Install-Module Az.StorageSync -AllowClobber
    ```

## <a name="prepare-windows-server-to-use-with-azure-file-sync"></a>Przygotowywanie systemu Windows Server do używania z usługą Azure File Sync
Dla każdego serwera, który ma być używana przy użyciu usługi Azure File Sync, w tym każdy węzeł serwera w klastrze trybu Failover należy wyłączyć **Konfiguracja zwiększonych zabezpieczeń programu Internet Explorer**. Jest to wymagane tylko w przypadku rejestracji serwera początkowego. Tę pozycję można włączyć ponownie po zarejestrowaniu serwera.

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)
1. Otwórz Menedżera serwera.
2. Kliknij przycisk **lokalnego serwera**:  
    !["Serwer lokalny" po lewej stronie interfejsu użytkownika Menedżera serwera](media/storage-sync-files-deployment-guide/prepare-server-disable-IEESC-1.PNG)
3. W okienku podrzędnym **Właściwości** wybierz link do funkcji **Konfiguracja zwiększonych zabezpieczeń programu Internet Explorer**.  
    ![W okienku "Konfiguracja zwiększonych zabezpieczeń" w Interfejsie użytkownika Menedżera serwera](media/storage-sync-files-deployment-guide/prepare-server-disable-IEESC-2.PNG)
4. W **Konfiguracja zwiększonych zabezpieczeń programu Internet Explorer** okno dialogowe, wybierz opcję **poza** dla **Administratorzy** i **użytkowników**:  
    ![Wybrana konfiguracja zwiększonych zabezpieczeń programu Internet Explorer pop — okna z "Wyłączone"](media/storage-sync-files-deployment-guide/prepare-server-disable-IEESC-3.png)

# <a name="powershelltabazure-powershell"></a>[Program PowerShell](#tab/azure-powershell)
Aby wyłączyć konfigurację zwiększonych zabezpieczeń programu Internet Explorer, wykonaj następujące czynności, w sesji programu PowerShell z podwyższonym poziomem uprawnień:

```powershell
# Disable Internet Explorer Enhanced Security Configuration 
# for Administrators
Set-ItemProperty -Path "HKLM:\SOFTWARE\Microsoft\Active Setup\Installed Components\{A509B1A7-37EF-4b3f-8CFC-4F3A74704073}" -Name "IsInstalled" -Value 0 -Force

# Disable Internet Explorer Enhanced Security Configuration 
# for Users
Set-ItemProperty -Path "HKLM:\SOFTWARE\Microsoft\Active Setup\Installed Components\{A509B1A8-37EF-4b3f-8CFC-4F3A74704073}" -Name "IsInstalled" -Value 0 -Force

# Force Internet Explorer closed, if open. This is required to fully apply the setting.
# Save any work you have open in the IE browser. This will not affect other browsers,
# including Microsoft Edge.
Stop-Process -Name iexplore -ErrorAction SilentlyContinue
``` 

---

## <a name="deploy-the-storage-sync-service"></a>Wdrażanie usługi synchronizacji magazynu 
Wdrożenie usługi Azure File Sync zaczyna się od wprowadzania **Usługa synchronizacji magazynu** zasobów w grupie zasobów w wybranej subskrypcji. Firma Microsoft zaleca, inicjowanie obsługi administracyjnej jako kilka z nich, zgodnie z potrzebami. Spowoduje utworzenie relacji zaufania między serwerami i ten zasób, a serwer można rejestrować tylko do jednej usługi synchronizacji magazynu. W rezultacie zalecane jest wdrożyć dowolną liczbę usługi synchronizacji magazynu na potrzeby oddzielnych grup serwerów. Należy pamiętać, że serwery z innego magazynu usługi synchronizacji nie mogą synchronizować ze sobą.

> [!Note]
> Usługa synchronizacji magazynu dziedziczone uprawnienia dostępu z subskrypcji i grupy zasobów, który został wdrożony w. Firma Microsoft zaleca, należy dokładnie sprawdzić kto ma do niego dostęp. Jednostki z dostępu do zapisu można uruchomić synchronizację nowych zestawów plików z serwerów zarejestrowanych ten magazyn Usługa synchronizacji i spowodować, że przepływ danych do usługi Azure storage, który jest dostępny do nich.

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)
Aby wdrożyć usługę synchronizacji magazynu, przejdź do [witryny Azure portal](https://portal.azure.com/), kliknij przycisk *Utwórz zasób* a następnie wyszukaj usługę Azure File Sync. W wynikach wyszukiwania wybierz **usługi Azure File Sync**, a następnie wybierz pozycję **Utwórz** otworzyć **Wdróż synchronizację magazynu** kartę.

W otwartym okienku wprowadź następujące informacje:

- **Nazwa**: Unikatowa nazwa (na subskrypcję) dla usługi synchronizacji magazynu.
- **Subskrypcja**: Subskrypcja, w której chcesz utworzyć usługę synchronizacji magazynu. W zależności od strategii konfiguracji Twojej organizacji może mieć dostęp do co najmniej jedną subskrypcję. Subskrypcja platformy Azure jest najbardziej podstawowym kontenerem dla rozliczeń dla każdej usługi w chmurze (np. usługi Azure Files).
- **Grupa zasobów**: Grupa zasobów to logiczna grupa zasobów platformy Azure, takich jak konto magazynu lub usługa synchronizacji magazynu. Można utworzyć nową grupę zasobów lub użyj istniejącej grupy zasobów dla usługi Azure File Sync. (Zaleca się używanie grup zasobów jak kontenery do izolować zasoby logicznie dla organizacji, takich jak grupowania, godzinę zasoby lub dla określonego projektu.)
- **Lokalizacja**: Region, w której chcesz wdrożyć usługę Azure File Sync. Tylko regiony obsługiwane są dostępne na tej liście.

Gdy skończysz, wybierz pozycję **Utwórz** wdrożyć usługę synchronizacji magazynu.

# <a name="powershelltabazure-powershell"></a>[Program PowerShell](#tab/azure-powershell)
Zastąp **< Az_Region >** , **< RG_Name >** , i **< my_storage_sync_service >** przy użyciu własnych wartości, następnie za pomocą następujących cmds tworzenie i wdrażanie Usługa synchronizacji magazynu:

```powershell
Connect-AzAccount

# this variable holds the Azure region you want to deploy 
# Azure File Sync into
$region = '<Az_Region>'

# Check to ensure Azure File Sync is available in the selected Azure
# region.
$regions = @()
Get-AzLocation | ForEach-Object { 
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
Get-AzResourceGroup | ForEach-Object { 
    $resourceGroups += $_.ResourceGroupName 
}

if ($resourceGroups -notcontains $resourceGroup) {
    New-AzResourceGroup -Name $resourceGroup -Location $region
}

$storageSyncName = "<my_storage_sync_service>"
$storageSync = New-AzStorageSyncService -ResourceGroupName $resourceGroup -Name $storageSyncName -Location $region
```

---

## <a name="install-the-azure-file-sync-agent"></a>Pobieranie agenta usługi Azure File Sync
Agent usługi Azure File Sync to możliwy do pobrania pakiet, który umożliwia synchronizowanie systemu Windows Server z udziałem plików platformy Azure. 

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)
Możesz pobrać agenta z [Microsoft Download Center](https://go.microsoft.com/fwlink/?linkid=858257). Po ukończeniu pobierania kliknij dwukrotnie pakiet MSI, aby rozpocząć instalację agenta usługi Azure File Sync.

> [!Important]  
> Jeśli zamierzasz używać usługi Azure File Sync z klastra trybu Failover, musi być zainstalowany agent usługi Azure File Sync w w każdym węźle w klastrze. Każdy węzeł w klastrze musi być zarejestrowana do pracy z usługi Azure File Sync.

Zaleca się, że wykonano następujące czynności:
- Pozostaw domyślnej ścieżki instalacji (C:\Program Files\Azure\StorageSyncAgent), aby uprościć zarządzanie Rozwiązywanie problemów i serwera.
- Włączenie usługi Microsoft Update w celu aktualizacji usługi Azure File Sync. Wszystkie aktualizacje agenta usługi Azure File Sync, w tym aktualizacje funkcji i poprawek, wykonywane z usługi Microsoft Update. Zaleca się instalowania najnowszych aktualizacji do usługi Azure File Sync. Aby uzyskać więcej informacji, zobacz [zasad aktualizacji usługi Azure File Sync](storage-sync-files-planning.md#azure-file-sync-agent-update-policy).

Po zakończeniu instalacji agenta usługi Azure File Sync interfejsu serwera rejestracji użytkownika jest automatycznie otwiera. Przed zarejestrowaniem; musi być zainstalowana usługa synchronizacji magazynu zobacz następną sekcję na temat tworzenia usługi synchronizacji magazynu.

# <a name="powershelltabazure-powershell"></a>[Program PowerShell](#tab/azure-powershell)
Wykonaj następujący kod programu PowerShell, aby pobrać odpowiednią wersję agenta usługi Azure File Sync dla swojego systemu operacyjnego i zainstaluj go na system.

> [!Important]  
> Jeśli zamierzasz używać usługi Azure File Sync z klastra trybu Failover, musi być zainstalowany agent usługi Azure File Sync w w każdym węźle w klastrze. Każdy węzeł w klastrze musi być zarejestrowana do pracy z usługi Azure File Sync.

```powershell
# Gather the OS version
$osver = [System.Environment]::OSVersion.Version

# Download the appropriate version of the Azure File Sync agent for your OS.
if ($osver.Equals([System.Version]::new(10, 0, 17763, 0))) {
    Invoke-WebRequest `
        -Uri https://aka.ms/afs/agent/Server2019 `
        -OutFile "StorageSyncAgent.msi" 
} elseif ($osver.Equals([System.Version]::new(10, 0, 14393, 0))) {
    Invoke-WebRequest `
        -Uri https://aka.ms/afs/agent/Server2016 `
        -OutFile "StorageSyncAgent.msi" 
} elseif ($osver.Equals([System.Version]::new(6, 3, 9600, 0))) {
    Invoke-WebRequest `
        -Uri https://aka.ms/afs/agent/Server2012R2 `
        -OutFile "StorageSyncAgent.msi" 
} else {
    throw [System.PlatformNotSupportedException]::new("Azure File Sync is only supported on Windows Server 2012 R2, Windows Server 2016, and Windows Server 2019")
}

# Install the MSI. Start-Process is used to PowerShell blocks until the operation is complete.
# Note that the installer currently forces all PowerShell sessions closed - this is a known issue.
Start-Process -FilePath "StorageSyncAgent.msi" -ArgumentList "/quiet" -Wait

# Note that this cmdlet will need to be run in a new session based on the above comment.
# You may remove the temp folder containing the MSI and the EXE installer
Remove-Item -Path ".\StorageSyncAgent.msi" -Recurse -Force
```

---

## <a name="register-windows-server-with-storage-sync-service"></a>Rejestrowanie systemu Windows Server za pomocą usługi synchronizacji magazynu
Zarejestrowanie systemu Windows Server za pomocą usługi synchronizacji magazynu powoduje ustanowienie relacji zaufania między serwerem (lub klastrem) oraz usługą synchronizacji magazynu. Serwer można zarejestrować tylko w jednej usłudze synchronizacji magazynu i można go synchronizować z innymi serwerami i udziałami plików platformy Azure skojarzonymi z tą samą usługą synchronizacji magazynu.

> [!Note]
> Rejestracja serwera używa poświadczeń platformy Azure w celu utworzenia relacji zaufania między Usługa synchronizacji magazynu i systemu Windows Server, jednak później serwer tworzy i używa własną tożsamość, która jest prawidłowa, tak długo, jak serwer pozostaje zarejestrowany i bieżący token Shared Access Signature (SAS magazynu) jest prawidłowa. Nie można wystawić nowy token sygnatury dostępu Współdzielonego z serwerem, gdy serwer jest zarejestrowany, dlatego usunięcie serwera możliwość dostępu do udziałów plików platformy Azure, w zatrzymywanie wszelkie synchronizacji.

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)
Interfejs użytkownika rejestracji serwera powinno spowodować otwarcie automatycznie po zakończeniu instalacji agenta usługi Azure File Sync. Jeśli tak się nie stanie, można otworzyć go ręcznie z lokalizacji pliku: C:\Program Files\Azure\StorageSyncAgent\ServerRegistration.exe. Po otwarciu interfejsu użytkownika rejestracji serwera wybierz **logowania** do rozpoczęcia.

Po zalogowaniu zostanie wyświetlony monit o następujące informacje:

![Zrzut ekranu przedstawiający interfejs użytkownika rejestracji serwera](media/storage-sync-files-deployment-guide/register-server-scubed-1.png)

- **Subskrypcja platformy Azure**: Subskrypcji, która zawiera usługę synchronizacji magazynu (zobacz [wdrożyć usługę synchronizacji magazynu](#deploy-the-storage-sync-service)). 
- **Grupa zasobów**: Grupa zasobów, która zawiera usługę synchronizacji magazynu.
- **Usługa synchronizacji magazynu**: Nazwa usługi synchronizacji magazynu, z którym chcesz zarejestrować.

Po wybraniu odpowiednie informacje, wybierz **zarejestrować** do ukończenia rejestracji serwera. W ramach procesu rejestracji zostanie wyświetlony monit o dodatkowe logowanie.

# <a name="powershelltabazure-powershell"></a>[Program PowerShell](#tab/azure-powershell)
```powershell
$registeredServer = Register-AzStorageSyncServer -ParentObject $storageSync
```

---

## <a name="create-a-sync-group-and-a-cloud-endpoint"></a>Tworzenie grupy synchronizacji i punktu końcowego w chmurze
Grupa synchronizacji definiuje topologię synchronizacji dla zestawu plików. Punkty końcowe w ramach grupy synchronizacji są synchronizowane ze sobą. Grupa synchronizacji musi zawierać jeden punkt końcowy chmury, który reprezentuje udział plików platformy Azure, i co najmniej jeden punkt końcowy serwera. Punkt końcowy serwera reprezentuje ścieżkę na zarejestrowanego serwera. Serwer może mieć punkty końcowe serwera w wiele grup synchronizacji. Można utworzyć dowolną liczbę grup synchronizacji należy odpowiednio opisują topologii żądaną synchronizacji.

Punkt końcowy w chmurze jest wskaźnikiem do udziału plików platformy Azure. Wszystkie punkty końcowe serwera zsynchronizuje się z punktem końcowym chmury, dzięki czemu punkt końcowy w chmurze z Centrum. Konto magazynu dla udziału plików platformy Azure musi znajdować się w tym samym regionie co usługa synchronizacji magazynu. Całość udziału plików platformy Azure zostaną zsynchronizowane z jednym wyjątkiem: Folder specjalny porównywalne z ukrytym folderze "System Volume Information" w woluminie NTFS będzie gotowa. Ten katalog jest nazywany ". SystemShareInformation". Zawiera on istotne synchronizację metadanych, które nie będą synchronizowane z innych punktów końcowych. Nie używać ani nie usuwaj go!

> [!Important]  
> Można wprowadzić zmiany do dowolnego punktu końcowego w chmurze lub punkt końcowy serwera w grupie synchronizacji i zsynchronizowaniu pliki do punktów końcowych w grupie synchronizacji. Jeśli wprowadzisz zmiany do końcowego w chmurze (udział plików platformy Azure) bezpośrednio, zmiany najpierw muszą być odnajdowane przez zadanie wykrywania zmian usługi Azure File Sync. Zadanie wykrywania zmian jest inicjowane dla punktu końcowego w chmurze tylko raz na 24 godziny. Aby uzyskać więcej informacji, zobacz [usługi Azure Files — często zadawane pytania](storage-files-faq.md#afs-change-detection).

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)
Aby utworzyć grupę synchronizacji w [witryny Azure portal](https://portal.azure.com/), przejdź do usługi synchronizacji magazynu, a następnie wybierz **+ grupa synchronizacji**:

![Tworzenie nowej grupy synchronizacji w witrynie Azure Portal](media/storage-sync-files-deployment-guide/create-sync-group-1.png)

W otwartym okienku wprowadź następujące informacje, aby utworzyć grupę synchronizacji z punktem końcowym chmury:

- **Nazwa grupy synchronizacji**: Nazwa grupy synchronizacji, która ma zostać utworzony. Ta nazwa musi być unikatowa w obrębie usługi synchronizacji magazynu, ale może być to dowolna nazwa logiczna z Twojego punktu widzenia.
- **Subskrypcja**: Subskrypcja, w której została wdrożona usługa synchronizacji magazynu w [wdrożyć usługę synchronizacji magazynu](#deploy-the-storage-sync-service).
- **Konto magazynu**: Jeśli wybierzesz **wybierz konto magazynu**, pojawi się na nim inną okienko, w którym można wybrać konto magazynu, który ma udziału plików platformy Azure, które mają być synchronizowane ze.
- **Udział plików platformy Azure**: Nazwa udziału plików platformy Azure, z którym mają być synchronizowane.

# <a name="powershelltabazure-powershell"></a>[Program PowerShell](#tab/azure-powershell)
Aby utworzyć grupy synchronizacji, należy wykonać następujące polecenie programu PowerShell. Pamiętaj, aby zastąpić `<my-sync-group>` z żądaną nazwą grupy synchronizacji.

```powershell
$syncGroupName = "<my-sync-group>"
$syncGroup = New-AzStorageSyncGroup -ParentObject $storageSync -Name $syncGroupName
```

Po pomyślnym utworzeniu grupy synchronizacji można utworzyć punktu końcowego w chmurze. Koniecznie Zastąp `<my-storage-account>` i `<my-file-share>` z oczekiwanych wartości.

```powershell
# Get or create a storage account with desired name
$storageAccountName = "<my-storage-account>"
$storageAccount = Get-AzStorageAccount -ResourceGroupName $resourceGroup | Where-Object {
    $_.StorageAccountName -eq $storageAccountName
}

if ($storageAccount -eq $null) {
    $storageAccount = New-AzStorageAccount `
        -Name $storageAccountName `
        -ResourceGroupName $resourceGroup `
        -Location $region `
        -SkuName Standard_LRS `
        -Kind StorageV2 `
        -EnableHttpsTrafficOnly:$true
}

# Get or create an Azure file share within the desired storage account
$fileShareName = "<my-file-share>"
$fileShare = Get-AzStorageShare -Context $storageAccount.Context | Where-Object {
    $_.Name -eq $fileShareName -and $_.IsSnapshot -eq $false
}

if ($fileShare -eq $null) {
    $fileShare = New-AzStorageShare -Context $storageAccount.Context -Name $fileShareName
}

# Create the cloud endpoint
New-AzStorageSyncCloudEndpoint `
    -Name $fileShare.Name `
    -ParentObject $syncGroup `
    -StorageAccountResourceId $storageAccount.Id `
    -AzureFileShareName $fileShare.Name
```

---

## <a name="create-a-server-endpoint"></a>Tworzenie punktu końcowego serwera
Punkt końcowy serwera reprezentuje określoną lokalizację na zarejestrowanym serwerze, taką jak folder na woluminie serwera. Punkt końcowy serwera musi być ścieżką na zarejestrowanego serwera (zamiast zainstalowanego udziału), a aby korzystać z obsługi warstw w chmurze, ścieżka musi być na woluminie bez systemu. Magazynu dołączone do sieci (NAS) nie jest obsługiwana.

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)
Aby dodać punkt końcowy serwera, przejdź do grupy synchronizacji nowo utworzony, a następnie wybierz **Dodaj punkt końcowy serwera**.

![Dodawanie nowego punktu końcowego serwera w okienku grupy synchronizacji](media/storage-sync-files-deployment-guide/create-sync-group-2.png)

W okienku **Dodawanie punktu końcowego serwera** wprowadź następujące informacje, aby utworzyć punkt końcowy serwera:

- **Zarejestrowany serwer**: Nazwa serwera lub klastra, w której chcesz utworzyć punkt końcowy serwera.
- **Ścieżka**: Ścieżka systemu Windows Server zostanie on zsynchronizowany w ramach grupy synchronizacji.
- **Obsługa warstw w chmurze**: Przełącznik, aby włączyć lub wyłączyć chmury warstw. Dzięki chmurze warstw, rzadko używane lub uzyskać dostępu do plików może należeć do warstwy do usługi Azure Files.
- **Wolne miejsce w woluminie**: Ilość wolnego miejsca, aby zarezerwować na woluminie, na którym znajduje się punkt końcowy serwera. Na przykład jeśli wolne miejsce w woluminie wynosi 50% na woluminie z punktem końcowym pojedynczego serwera, około połowę ilość danych jest warstwy do usługi Azure Files. Niezależnie od tego, czy w chmurze warstw jest włączona, udziału plików platformy Azure ma zawsze pełne kopii danych w grupie synchronizacji.

Aby dodać punkt końcowy serwera, wybierz **Utwórz**. Pliki są teraz synchronizowane między udziału plików platformy Azure i systemu Windows Server. 

# <a name="powershelltabazure-powershell"></a>[Program PowerShell](#tab/azure-powershell)
Wykonaj następujące polecenia programu PowerShell, aby utworzyć punkt końcowy serwera i należy zastąpić `<your-server-endpoint-path>` i `<your-volume-free-space>` z odpowiednie wartości.

```powershell
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
    New-AzStorageSyncServerEndpoint `
        -Name $registeredServer.FriendlyName `
        -SyncGroup $syncGroup `
        -ServerResourceId $registeredServer.ResourceId `
        -ServerLocalPath $serverEndpointPath `
        -CloudTiering `
        -VolumeFreeSpacePercent $volumeFreeSpacePercentage
} else {
    # Create server endpoint
    New-AzStorageSyncServerEndpoint `
        -Name $registeredServer.FriendlyName `
        -SyncGroup $syncGroup `
        -ServerResourceId $registeredServer.ResourceId `
        -ServerLocalPath $serverEndpointPath 
}
```

---

## <a name="onboarding-with-azure-file-sync"></a>Dołączania przy użyciu usługi Azure File Sync
Zalecane czynności do dołączenia w usłudze Azure File Sync w pierwszym z zero Przestój, przy zachowaniu pełnego pliku wierności i listy kontroli dostępu (ACL) są następujące:
 
1. Wdróż usługę synchronizacji magazynu.
2. Utwórz grupę synchronizacji.
3. Zainstaluj agenta usługi Azure File Sync na serwer z pełnym zestawem danych.
4. Zarejestruj ten serwer i utworzyć punkt końcowy serwera w udziale. 
5. Pozwól synchronizacji wykonaj pełny przekazywania do udziału plików platformy Azure (punkt końcowy w chmurze).  
6. Po zakończeniu przekazywania początkowej, należy zainstalować agenta usługi Azure File Sync na wszystkich pozostałych serwerach.
7. Utwórz nowe udziały plików na wszystkich pozostałych serwerach.
8. Na nowe udziały plików przy użyciu zasady obsługi warstw w chmurze, należy utworzyć punkty końcowe serwera, w razie potrzeby. (Ten krok wymaga dodatkowego magazynu, które będą dostępne dla początkowej konfiguracji).
9. Poinformuj agenta usługi Azure File Sync w celu szybkiego przywracania pełną przestrzeni nazw bez transferu danych rzeczywistych. Po wykonaniu synchronizacji pełnej przestrzeni nazw aparatu synchronizacji spowoduje wypełnienie miejsca na dysku lokalnym, na podstawie zasad obsługi warstw chmury dla punktu końcowego serwera. 
10. Upewnij się, synchronizacji kończy i przetestować topologii, zgodnie z potrzebami. 
11. Przekieruj użytkowników i aplikacji do nowego udziału.
12. Opcjonalnie można usunąć wszelkie zduplikowane udziałów na serwerach.
 
Jeśli nie masz dodatkowego magazynu do dołączenia do początkowego i chcesz dołączyć do istniejących udziałów, możesz wstępnie umieszczenia danych w udziałach plików platformy Azure. To podejście jest zalecane tylko wtedy, gdy można zaakceptować przestojów i absolutnie zagwarantować żadnej zmiany danych w udziałach serwera podczas procesu dołączania początkowej. 
 
1. Upewnij się, że dane na żadnym serwerze nie można zmienić podczas procesu dołączania.
2. Udziały plików platformy Azure wstępnie materiału siewnego z danymi serwera przy użyciu dowolnego narzędzia transferu danych za pośrednictwem protokołu SMB np. Robocopy, bezpośrednich kopii SMB. Ponieważ narzędzie AzCopy nie przekazywanie danych za pośrednictwem protokołu SMB, więc nie można używać dla wstępnie rozmieszczania.
3. Tworzenie usługi Azure File Sync topologii z punktami końcowymi żądanego serwera, wskazując istniejących udziałów.
4. Pozwól synchronizacji, Zakończ proces uzgadniania dla wszystkich punktów końcowych. 
5. Po zakończeniu uzgadniania możesz otworzyć udziałów zmian.
 
Należy pamiętać, że obecnie wstępnie rozmieszczania podejście ma kilka ograniczeń- 
- Pełnej rozdzielczości na plikach nie są zachowywane. Na przykład pliki utracić listy kontroli dostępu i sygnatury czasowe.
- Zmiany danych na serwerze przed topologia synchronizacji jest w pełni uruchomiony i działa może spowodować konflikty punkty końcowe serwera.  
- Po utworzeniu punktu końcowego w chmurze usługi Azure File Sync uruchamia proces do wykrywania plików w chmurze, przed rozpoczęciem synchronizacji początkowej. Czas potrzebny do ukończenia tego procesu zależy od różnych czynników, takich jak szybkość sieci, dostępnej przepustowości i liczby plików i folderów. Dla nierównej oceny w wersji zapoznawczej wykrywanie proces jest uruchamiany od 10 plików na sekundę.  W związku z tym nawet wtedy, gdy wstępnie rozmieszczania szybkiego uruchomienia, to całkowity czas, aby uzyskać w pełni działającego systemu może być znacznie dłuższe, gdy dane są wstępnie wypełnionych w chmurze.

## <a name="migrate-a-dfs-replication-dfs-r-deployment-to-azure-file-sync"></a>Migracja wdrożenia replikacji systemu plików DFS (DFS-R) do usługi Azure File Sync
Aby przeprowadzić migrację do wdrożenia systemu plików DFS-R do usługi Azure File Sync:

1. Tworzenie grupy synchronizacji w celu reprezentowania topologii systemu plików DFS-R, który chcesz zastąpić.
2. Uruchom na serwerze, który ma pełny zestaw danych w topologii systemu plików DFS-R, aby przeprowadzić migrację. Zainstaluj usługi Azure File Sync na tym serwerze.
3. Zarejestruj ten serwer i utworzyć punkt końcowy serwera dla pierwszego serwera do migracji. Nie włączaj chmury warstw.
4. Poinformuj wszystkich synchronizacji danych do udziału plików platformy Azure (punkt końcowy w chmurze).
5. Instalowanie i rejestrowanie agenta usługi Azure File Sync na wszystkich pozostałych serwerach systemu plików DFS-R.
6. Wyłącz systemu plików DFS-R. 
7. Utwórz punkt końcowy serwera na wszystkich serwerach systemu plików DFS-R. Nie włączaj chmury warstw.
8. Upewnij się, synchronizacji kończy i przetestować topologii, zgodnie z potrzebami.
9. Retire DFS-R.
10. May obsługi warstw w chmurze można teraz włączyć w dowolnym punkcie końcowym serwera, zgodnie z potrzebami.

Aby uzyskać więcej informacji, zobacz [współdziałania usługi Azure File Sync z rozproszonego systemu plików (DFS)](storage-sync-files-planning.md#distributed-file-system-dfs).

## <a name="next-steps"></a>Kolejne kroki
- [Dodawanie lub usuwanie punktu końcowego serwera synchronizacji plików platformy Azure](storage-sync-files-server-endpoint.md)
- [Zarejestruj lub Wyrejestruj serwer usługi Azure File Sync](storage-sync-files-server-registration.md)
- [Monitorowanie usługi Azure File Sync](storage-sync-files-monitoring.md)
