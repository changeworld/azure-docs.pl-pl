---
title: Wdrażanie synchronizacji plików platformy Azure | Dokumenty firmy Microsoft
description: Dowiedz się, jak wdrożyć synchronizację plików platformy Azure od początku do końca.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 07/19/2018
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: f2c4e762ebf10a5ca2120c13a52750a7781d60b9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79268069"
---
# <a name="deploy-azure-file-sync"></a>Wdrażanie usługi Azure File Sync
Użyj usługi Azure File Sync, aby scentralizować udziały plików organizacji w usłudze Azure Files, zachowując elastyczność, wydajność i zgodność lokalnego serwera plików. Funkcja Azure File Sync przekształca system Windows Server w szybką pamięć podręczną udziału plików platformy Azure. Można użyć dowolnego protokołu dostępnego w systemie Windows Server, aby uzyskać dostęp do danych lokalnie, w tym SMB, NFS i FTPS. Możesz mieć tyle pamięci podręcznych, ile potrzebujesz na całym świecie.

Zdecydowanie zaleca się [przeczytanie planowania wdrożenia i](storage-files-planning.md) planowania plików platformy Azure dla wdrożenia usługi Azure File Sync przed [wykonaniem](storage-sync-files-planning.md) kroków opisanych w tym artykule.

## <a name="prerequisites"></a>Wymagania wstępne
* Udział plików platformy Azure w tym samym regionie, w który chcesz wdrożyć usługę Azure File Sync. Aby uzyskać więcej informacji, zobacz:
    - [Dostępność regionu](storage-sync-files-planning.md#azure-file-sync-region-availability) dla usługi Azure File Sync.
    - [Utwórz udział plików](storage-how-to-create-file-share.md) dla opisu krok po kroku, jak utworzyć udział plików.
* Co najmniej jedno obsługiwane wystąpienie klastra systemu Windows Server lub Windows Server w celu synchronizacji z synchronizacją plików platformy Azure. Aby uzyskać więcej informacji na temat obsługiwanych wersji systemu Windows Server, zobacz [Interoperacyjność z systemem Windows Server](storage-sync-files-planning.md#windows-file-server-considerations).
* Moduł Az PowerShell może być używany z programem PowerShell 5.1 lub PowerShell 6+. Moduł Az PowerShell dla usługi Azure File Sync można używać w dowolnym obsługiwanym systemie, w tym w systemach innych niż Windows, jednak polecenie cmdlet rejestracji serwera musi być zawsze uruchamiane w zarejestrowanym wystąpieniu systemu Windows Server (można to zrobić bezpośrednio lub za pośrednictwem programu PowerShell remoting). W systemie Windows Server 2012 R2 można sprawdzić, czy jest uruchomiona co najmniej program PowerShell 5.1. \* patrząc na wartość **właściwości PSVersion** **obiektu $PSVersionTable:**

    ```powershell
    $PSVersionTable.PSVersion
    ```

    Jeśli wartość PSVersion jest mniejsza niż 5,1. \*, jak będzie w przypadku większości świeżych instalacji systemu Windows Server 2012 R2, można łatwo uaktualnić, pobierając i instalując [Windows Management Framework (WMF) 5.1](https://www.microsoft.com/download/details.aspx?id=54616). Odpowiedni pakiet do pobrania i zainstalowania dla systemu Windows Server 2012 R2 to **\*\*\*\*\*\*\*Win8.1AndW2K12R2-KB -x64.msu**. 

    Program PowerShell 6+ może być używany z dowolnym obsługiwanym systemem i można go pobrać za pośrednictwem [strony GitHub.](https://github.com/PowerShell/PowerShell#get-powershell) 

    > [!Important]  
    > Jeśli planujesz używać interfejsu użytkownika rejestracji serwera, a nie rejestrowania bezpośrednio z programu PowerShell, należy użyć programu PowerShell 5.1.

* Jeśli zdecydujesz się na użycie programu PowerShell 5.1, upewnij się, że jest zainstalowany co najmniej .NET 4.7.2. Dowiedz się więcej o [wersjach i zależnościach](https://docs.microsoft.com/dotnet/framework/migration-guide/versions-and-dependencies) programu .NET Framework w systemie.

    > [!Important]  
    > Jeśli instalujesz program .NET 4.7.2+ w systemie `quiet` Windows `norestart` Server Core, musisz zainstalować z flagami lub instalacja zakończy się niepowodzeniem. Na przykład w przypadku instalowania platformy .NET 4.8 polecenie będzie wyglądać następująco:
    > ```PowerShell
    > Start-Process -FilePath "ndp48-x86-x64-allos-enu.exe" -ArgumentList "/q /norestart" -Wait
    > ```

* Moduł Az PowerShell, który można zainstalować, postępując zgodnie z instrukcjami tutaj: [Zainstaluj i skonfiguruj program Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-Az-ps).
     
    > [!Note]  
    > Moduł Az.StorageSync jest teraz instalowany automatycznie po zainstalowaniu modułu Az PowerShell.

## <a name="prepare-windows-server-to-use-with-azure-file-sync"></a>Przygotowywanie systemu Windows Server do używania z usługą Azure File Sync
Dla każdego serwera, który ma być używany z synchronizacją plików platformy Azure, w tym dla każdego węzła serwera w klastrze trybu failover, wyłącz **konfigurację zwiększonych zabezpieczeń programu Internet Explorer**. Jest to wymagane tylko w przypadku początkowej rejestracji serwera. Tę pozycję można włączyć ponownie po zarejestrowaniu serwera.

# <a name="portal"></a>[Portal](#tab/azure-portal)
> [!Note]  
> Ten krok można pominąć, jeśli wdrażasz usługę Azure File Sync w systemie Windows Server Core.

1. Otwórz Menedżera serwera.
2. Kliknij pozycję **Serwer lokalny**:  
    ![Pozycja „Serwer lokalny” po lewej stronie interfejsu użytkownika Menedżera serwera](media/storage-sync-files-deployment-guide/prepare-server-disable-IEESC-1.PNG)
3. W okienku podrzędnym **Właściwości** wybierz link do funkcji **Konfiguracja zwiększonych zabezpieczeń programu Internet Explorer**.  
    ![Okienko „Konfiguracja zwiększonych zabezpieczeń” w interfejsie użytkownika Menedżera serwera](media/storage-sync-files-deployment-guide/prepare-server-disable-IEESC-2.PNG)
4. W oknie dialogowym **Konfiguracja zwiększonych zabezpieczeń programu Internet Explorer** wybierz pozycję **Wyłączone** dla **administratorów** i **użytkowników:**  
    ![Okno podręczne „Konfiguracja zwiększonych zabezpieczeń programu Internet Explorer” z wybraną pozycją „Wyłączono”](media/storage-sync-files-deployment-guide/prepare-server-disable-IEESC-3.png)

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)
Aby wyłączyć konfigurację zwiększonych zabezpieczeń programu Internet Explorer, wykonaj następujące czynności z podwyższonej wysokości sesji programu PowerShell:

```powershell
$installType = (Get-ItemProperty "HKLM:\SOFTWARE\Microsoft\Windows NT\CurrentVersion\").InstallationType

# This step is not required for Server Core
if ($installType -ne "Server Core") {
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
}
``` 

---

## <a name="deploy-the-storage-sync-service"></a>Wdrażanie usługi synchronizacji magazynu 
Wdrażanie usługi Azure File Sync rozpoczyna się od umieszczenia zasobu **usługi synchronizacji magazynu** w grupie zasobów wybranej subskrypcji. Zaleca się inicjowanie obsługi administracyjnej jak kilka z nich, w razie potrzeby. Utworzysz relację zaufania między serwerami a tym zasobem, a serwer może być zarejestrowany tylko w jednej usłudze synchronizacji magazynu. W związku z tym zaleca się wdrożenie tyle usług synchronizacji magazynu, jak trzeba do oddzielenia grup serwerów. Należy pamiętać, że serwery z różnych usług synchronizacji magazynu nie mogą być synchronizowane ze sobą.

> [!Note]
> Usługa synchronizacji magazynu dziedziczy uprawnienia dostępu z subskrypcji i grupy zasobów, w którą została wdrożona. Zalecamy dokładne sprawdzenie, kto ma do niego dostęp. Jednostki z dostępem do zapisu można rozpocząć synchronizację nowych zestawów plików z serwerów zarejestrowanych do tej usługi synchronizacji magazynu i spowodować przepływ danych do magazynu platformy Azure, który jest dla nich dostępny.

# <a name="portal"></a>[Portal](#tab/azure-portal)
Aby wdrożyć usługę synchronizacji magazynu, przejdź do [witryny Azure portal](https://portal.azure.com/), kliknij pozycję *Utwórz zasób,* a następnie wyszukaj usługę Azure File Sync. W wynikach wyszukiwania wybierz pozycję **Azure File Sync**, a następnie wybierz pozycję **Utwórz,** aby otworzyć kartę **Wdrażanie synchronizacji magazynu.**

W otwartym okienku wprowadź następujące informacje:

- **Nazwa**: Unikatowa nazwa (na subskrypcję) dla usługi synchronizacji magazynu.
- **Subskrypcja:** subskrypcja, w której chcesz utworzyć usługę synchronizacji magazynu. W zależności od strategii konfiguracji organizacji możesz mieć dostęp do jednej lub więcej subskrypcji. Subskrypcja platformy Azure jest najbardziej podstawowym kontenerem do rozliczeń dla każdej usługi w chmurze (takiej jak usługi Azure Files).
- **Grupa zasobów:** Grupa zasobów to logiczna grupa zasobów platformy Azure, taka jak konto magazynu lub usługa synchronizacji magazynu. Można utworzyć nową grupę zasobów lub użyć istniejącej grupy zasobów dla usługi Azure File Sync. (Zaleca się używanie grup zasobów jako kontenerów do logicznego izolowania zasobów dla organizacji, takich jak grupowanie zasobów zasobów zasobów ludzkich lub zasobów dla określonego projektu).
- **Lokalizacja:** region, w którym chcesz wdrożyć synchronizację plików platformy Azure. Na tej liście są dostępne tylko obsługiwane regiony.

Po zakończeniu wybierz pozycję **Utwórz,** aby wdrożyć usługę synchronizacji magazynu.

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)
Zamień `<Az_Region>` `<my_storage_sync_service>` i `<RG_Name>`za pomocą własnych wartości, a następnie użyj następujących poleceń, aby utworzyć i wdrożyć usługę synchronizacji magazynu:

```powershell
$hostType = (Get-Host).Name

if ($installType -eq "Server Core" -or $hostType -eq "ServerRemoteHost") {
    Connect-AzAccount -UseDeviceAuthentication
}
else {
    Connect-AzAccount
}

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

# <a name="portal"></a>[Portal](#tab/azure-portal)
Agent można pobrać z [Centrum pobierania Microsoft](https://go.microsoft.com/fwlink/?linkid=858257). Po zakończeniu pobierania kliknij dwukrotnie pakiet MSI, aby rozpocząć instalację agenta synchronizacji plików platformy Azure.

> [!Important]  
> Jeśli zamierzasz używać synchronizacji plików platformy Azure z klastrem trybu failover, agent synchronizacji plików azure musi być zainstalowany w każdym węźle w klastrze. Każdy węzeł w klastrze musi być zarejestrowany do pracy z synchronizacją plików platformy Azure.

Zaleca się wykonanie następujących czynności:
- Pozostaw domyślną ścieżkę instalacji (C:\Program Files\Azure\StorageSyncAgent), aby uprościć rozwiązywanie problemów i konserwację serwera.
- Włącz usługę Microsoft Update, aby aktualizować synchronizację plików platformy Azure. Wszystkie aktualizacje do agenta usługi Azure File Sync, w tym aktualizacje funkcji i poprawki, występują z witryny Microsoft Update. Zalecamy zainstalowanie najnowszej aktualizacji do usługi Azure File Sync. Aby uzyskać więcej informacji, zobacz [zasady aktualizacji synchronizacji plików platformy Azure](storage-sync-files-planning.md#azure-file-sync-agent-update-policy).

Po zakończeniu instalacji agenta synchronizacji plików platformy Azure zostanie automatycznie otwarty interfejs użytkownika rejestracji serwera. Przed rejestracją musi istnieć usługa synchronizacji magazynu; zobacz następną sekcję dotyczącą tworzenia usługi synchronizacji magazynu.

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)
Wykonaj następujący kod programu PowerShell, aby pobrać odpowiednią wersję agenta usługi Azure File Sync dla systemu operacyjnego i zainstalować go w systemie.

> [!Important]  
> Jeśli zamierzasz używać synchronizacji plików platformy Azure z klastrem trybu failover, agent synchronizacji plików azure musi być zainstalowany w każdym węźle w klastrze. Każdy węzeł w klastrze musi być zarejestrowany do pracy z synchronizacją plików platformy Azure.

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
> Rejestracja serwera używa poświadczeń platformy Azure do utworzenia relacji zaufania między usługą synchronizacji magazynu a systemem Windows Server, jednak następnie serwer tworzy i używa własnej tożsamości, która jest prawidłowa, o ile serwer pozostaje zarejestrowany i bieżący token sygnatury dostępu współdzielonego (Sygnatura dysków Sygnatury Dostępu Współdzielonego) jest prawidłowy. Nie można wydać nowego tokenu Sygnatury dostępu Współdzielonego na serwerze po wyrejestrowaniu serwera, co eliminuje możliwość dostępu do udziałów plików platformy Azure, zatrzymując synchronizację.

# <a name="portal"></a>[Portal](#tab/azure-portal)
Interfejs użytkownika rejestracji serwera powinien zostać otwarty automatycznie po zainstalowaniu agenta usługi Azure File Sync. W przeciwnym razie można otworzyć go ręcznie w lokalizacji jego pliku: C:\Program Files\Azure\StorageSyncAgent\ServerRegistration.exe. Po otwarciu interfejsu użytkownika rejestracji serwera wybierz pozycję **Zaloguj się,** aby rozpocząć.

Po zalogowaniu się zostanie wyświetlony monit o podanie następujących informacji:

![Zrzut ekranu przedstawiający interfejs użytkownika rejestracji serwera](media/storage-sync-files-deployment-guide/register-server-scubed-1.png)

- **Subskrypcja platformy Azure:** subskrypcja zawierająca usługę synchronizacji magazynu (zobacz [Wdrażanie usługi synchronizacji magazynu).](#deploy-the-storage-sync-service) 
- **Grupa zasobów:** Grupa zasobów zawierająca usługę synchronizacji magazynu.
- **Usługa synchronizacji magazynu:** nazwa usługi synchronizacji magazynu, za pomocą której chcesz się zarejestrować.

Po wybraniu odpowiednich informacji wybierz pozycję **Zarejestruj się,** aby zakończyć rejestrację serwera. W ramach procesu rejestracji zostanie wyświetlony monit o dodatkowe logowanie.

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)
```powershell
$registeredServer = Register-AzStorageSyncServer -ParentObject $storageSync
```

---

## <a name="create-a-sync-group-and-a-cloud-endpoint"></a>Tworzenie grupy synchronizacji i punktu końcowego w chmurze
Grupa synchronizacji definiuje topologię synchronizacji dla zestawu plików. Punkty końcowe w ramach grupy synchronizacji są synchronizowane ze sobą. Grupa synchronizacji musi zawierać jeden punkt końcowy chmury, który reprezentuje udział plików platformy Azure, i co najmniej jeden punkt końcowy serwera. Punkt końcowy serwera reprezentuje ścieżkę na zarejestrowanym serwerze. Serwer może mieć punkty końcowe serwera w wielu grupach synchronizacji. Można utworzyć dowolną liczbę grup synchronizacji, aby odpowiednio opisać żądaną topologię synchronizacji.

Punkt końcowy chmury jest wskaźnikiem do udziału plików platformy Azure. Wszystkie punkty końcowe serwera zostaną zsynchronizowane z punktem końcowym chmury, dzięki czemu punkt końcowy chmury jest centrum. Konto magazynu dla udziału plików platformy Azure musi znajdować się w tym samym regionie co usługa synchronizacji magazynu. Całość udziału plików platformy Azure zostanie zsynchronizowana, z jednym wyjątkiem: Zostanie udostępniony specjalny folder, porównywalny z ukrytym folderem "Informacje o woluminie systemowym" na woluminie NTFS. Ten katalog nosi nazwę ". Informacje o systemie". Zawiera ważne metadane synchronizacji, które nie będą synchronizowane z innymi punktami końcowymi. Nie używaj go ani nie usuwaj!

> [!Important]  
> Można wprowadzać zmiany w dowolnym punkcie końcowym chmury lub punktu końcowego serwera w grupie synchronizacji i synchronizować pliki z innymi punktami końcowymi w grupie synchronizacji. Jeśli dokonasz zmiany w punkcie końcowym chmury (udział plików platformy Azure) bezpośrednio, zmiany muszą najpierw zostać wykryte przez zadanie wykrywania zmian usługi Azure File Sync. Zadanie wykrywania zmian jest inicjowane dla punktu końcowego chmury tylko raz na 24 godziny. Aby uzyskać więcej informacji, zobacz [Często zadawane pytania dotyczące plików Azure .](storage-files-faq.md#afs-change-detection)

# <a name="portal"></a>[Portal](#tab/azure-portal)
Aby utworzyć grupę synchronizacji, w [witrynie Azure portal](https://portal.azure.com/)przejdź do usługi synchronizacji magazynu, a następnie wybierz + **Grupę synchronizacji:**

![Tworzenie nowej grupy synchronizacji w witrynie Azure Portal](media/storage-sync-files-deployment-guide/create-sync-group-1.png)

W otwartym okienku wprowadź następujące informacje, aby utworzyć grupę synchronizacji z punktem końcowym chmury:

- **Nazwa grupy synchronizacji**: Nazwa grupy synchronizacji, która ma zostać utworzona. Ta nazwa musi być unikatowa w obrębie usługi synchronizacji magazynu, ale może być to dowolna nazwa logiczna z Twojego punktu widzenia.
- **Subskrypcja:** Subskrypcja, w której wdrożono usługę synchronizacji magazynu w [usłudze wdrażania usługi synchronizacji magazynu](#deploy-the-storage-sync-service).
- **Konto magazynu:** Jeśli wybierzesz **wybierz konto magazynu,** pojawi się inne okienko, w którym możesz wybrać konto magazynu, z którym ma udział plików platformy Azure, z którym chcesz zsynchronizować.
- **Udział plików platformy Azure:** nazwa udziału plików platformy Azure, z którym chcesz synchronizować.

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)
Aby utworzyć grupę synchronizacji, należy wykonać następujący program PowerShell. Pamiętaj, `<my-sync-group>` aby zastąpić żądaną nazwą grupy synchronizacji.

```powershell
$syncGroupName = "<my-sync-group>"
$syncGroup = New-AzStorageSyncGroup -ParentObject $storageSync -Name $syncGroupName
```

Po pomyślnym utworzeniu grupy synchronizacji można utworzyć punkt końcowy chmury. Pamiętaj, aby `<my-storage-account>` `<my-file-share>` zastąpić i oczekiwane wartości.

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
Punkt końcowy serwera reprezentuje określoną lokalizację na zarejestrowanym serwerze, taką jak folder na woluminie serwera. Punkt końcowy serwera musi być ścieżką na zarejestrowanym serwerze (a nie na zainstalowanym udziale), a aby korzystać z warstw w chmurze, ścieżka musi znajdować się na woluminie niesystemowym. Pamięć masowa podłączona do sieci (NAS) nie jest obsługiwana.

# <a name="portal"></a>[Portal](#tab/azure-portal)
Aby dodać punkt końcowy serwera, przejdź do nowo utworzonej grupy synchronizacji, a następnie wybierz pozycję **Dodaj punkt końcowy serwera**.

![Dodawanie nowego punktu końcowego serwera w okienku grupy synchronizacji](media/storage-sync-files-deployment-guide/create-sync-group-2.png)

W okienku **Dodawanie punktu końcowego serwera** wprowadź następujące informacje, aby utworzyć punkt końcowy serwera:

- **Zarejestrowany serwer**: Nazwa serwera lub klastra, w którym ma zostać utworzony punkt końcowy serwera.
- **Ścieżka:** Ścieżka systemu Windows Server, która ma być synchronizowana jako część grupy synchronizacji.
- **Warstwowa chmura:** przełącznik, aby włączyć lub wyłączyć warstwy w chmurze. Dzięki warstwom w chmurze rzadko używane lub dostępne pliki mogą być warstwowe do usługi Azure Files.
- **Wolne miejsce na wolumin:** Ilość wolnego miejsca do zarezerwowania na woluminie, na którym znajduje się punkt końcowy serwera. Na przykład jeśli wolne miejsce na wolumin jest ustawiona na 50% na woluminie, który ma jeden punkt końcowy serwera, mniej więcej połowa ilości danych jest warstwowa do usługi Azure Files. Niezależnie od tego, czy warstwa w chmurze jest włączona, twój udział plików platformy Azure zawsze ma pełną kopię danych w grupie synchronizacji.

Aby dodać punkt końcowy serwera, wybierz pozycję **Utwórz**. Pliki są teraz synchronizowane między udziałem plików platformy Azure i systemem Windows Server. 

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)
Wykonaj następujące polecenia programu PowerShell, aby utworzyć punkt końcowy `<your-server-endpoint-path>` `<your-volume-free-space>` serwera i należy zastąpić i żądane wartości.

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

## <a name="configure-firewall-and-virtual-network-settings"></a>Konfigurowanie ustawień zapory i sieci wirtualnej

### <a name="portal"></a>Portal
Jeśli chcesz skonfigurować synchronizację plików platformy Azure do pracy z ustawieniami zapory i sieci wirtualnej, wykonaj następujące czynności:

1. W witrynie Azure portal przejdź do konta magazynu, które chcesz zabezpieczyć.
1. Wybierz przycisk **Zapory i sieci wirtualne** w menu po lewej stronie.
1. Wybierz **wybrane sieci** w obszarze **Zezwalaj na dostęp z**.
1. Upewnij się, że adres IP lub sieć wirtualna serwerów są wymienione w odpowiedniej sekcji.
1. Upewnij się, że **opcja Zezwalaj zaufanym usługom firmy Microsoft na dostęp do tego konta magazynu** jest zaznaczona.
1. Wybierz **pozycję Zapisz,** aby zapisać ustawienia.

![Konfigurowanie ustawień zapory i sieci wirtualnej do pracy z synchronizacją plików platformy Azure](media/storage-sync-files-deployment-guide/firewall-and-vnet.png)

## <a name="onboarding-with-azure-file-sync"></a>Dołączanie z synchronizacją plików platformy Azure
Zalecane kroki do wbudowanego w usłudze Azure File Sync dla pierwszego z zerowym przestojem przy zachowaniu pełnej wierności plików i listy kontroli dostępu (ACL) są następujące:
 
1. Wdrażanie usługi synchronizacji magazynu.
2. Utwórz grupę synchronizacji.
3. Zainstaluj agenta usługi Azure File Sync na serwerze z pełnym zestawem danych.
4. Zarejestruj ten serwer i utwórz punkt końcowy serwera w udziale. 
5. Pozwól synchronizacji wykonać pełne przekazywanie do udziału plików platformy Azure (punkt końcowy w chmurze).  
6. Po zakończeniu początkowego przekazywania zainstaluj agenta usługi Azure File Sync na każdym z pozostałych serwerów.
7. Utwórz nowe udziały plików na każdym z pozostałych serwerów.
8. W razie potrzeby utwórz punkty końcowe serwera w nowych udziałach plików za pomocą zasad warstwowych w chmurze. (Ten krok wymaga dodatkowego miejsca do magazynowania dla początkowej konfiguracji).
9. Pozwól agentowi usługi Azure File Sync wykonać szybkie przywracanie pełnego obszaru nazw bez rzeczywistego transferu danych. Po synchronizacji pełnego obszaru nazw aparat synchronizacji wypełni miejsce na dysku lokalnym na podstawie zasad warstwowego chmury dla punktu końcowego serwera. 
10. Upewnij się, że synchronizacja zostanie zakończona i przetestuj topologię zgodnie z potrzebami. 
11. Przekieruj użytkowników i aplikacje do tego nowego udziału.
12. Opcjonalnie można usunąć wszystkie zduplikowane udziały na serwerach.
 
Jeśli nie masz dodatkowego miejsca do początkowego dołączania i chcesz dołączyć do istniejących udziałów, możesz wstępnie wysiewać dane w udziałach plików platformy Azure. Takie podejście jest zalecane, jeśli i tylko wtedy, gdy można zaakceptować przestoje i absolutnie zagwarantować żadnych zmian danych na udziały serwera podczas początkowego procesu dołączania. 
 
1. Upewnij się, że dane na żadnym z serwerów nie mogą ulec zmianie podczas procesu dołączania.
2. Wstępnie ziarnowanie udziałów plików platformy Azure z danymi serwera przy użyciu dowolnego narzędzia transferu danych za pośrednictwem SMB na przykład Robocopy, bezpośrednia kopia SMB. Ponieważ AzCopy nie przekazuje danych za sprawą SMB, więc nie można ich używać do wstępnego wysiewu.
3. Utwórz topologię usługi Azure File Sync z żądanymi punktami końcowymi serwera wskazującymi istniejące udziały.
4. Pozwól, aby proces uzgadniania zakończenia synchronizacji był dla wszystkich punktów końcowych. 
5. Po zakończeniu uzgadniania można otworzyć udziały dla zmian.
 
Obecnie podejście przed wysiewem ma kilka ograniczeń - 
- Pełna wierność plików nie jest zachowywana. Na przykład pliki tracą listy ACL i sygnatury czasowe.
- Zmiany danych na serwerze przed zakończeniem topologii synchronizacji mogą powodować konflikty w punktach końcowych serwera.  
- Po utworzeniu punktu końcowego chmury usługa Azure File Sync uruchamia proces wykrywania plików w chmurze przed rozpoczęciem synchronizacji początkowej. Czas ukończenia tego procesu zależy od różnych czynników, takich jak szybkość sieci, dostępna przepustowość oraz liczba plików i folderów. W przypadku szacunków przybliżonych w wersji zapoznawczej proces wykrywania przebiega w przybliżeniu przy 10 plikach/s.  W związku z tym, nawet jeśli wstępne wysiewu działa szybko, ogólny czas, aby uzyskać w pełni uruchomiony system może być znacznie dłuższy, gdy dane są wstępnie rozstawione w chmurze.

## <a name="self-service-restore-through-previous-versions-and-vss-volume-shadow-copy-service"></a>Przywracanie samoobsługowe za pośrednictwem poprzednich wersji i usługi VSS (usługa kopiowania woluminów w tle)

> [!IMPORTANT]
> Poniższe informacje mogą być używane tylko z wersją 9 (lub powyżej) agenta synchronizacji magazynu. Wersje niższe niż 9 nie będą miały poleceń cmdlet StorageSyncSelfService.

Poprzednie wersje to funkcja systemu Windows, która umożliwia wykorzystanie migawek VSS po stronie serwera woluminu do prezentowania przywracalnych wersji pliku klientowi SMB.
Umożliwia to zaawansowany scenariusz, powszechnie określane jako przywracanie samoobsługowe, bezpośrednio dla pracowników informacji, a nie w zależności od przywracania od administratora IT.

Migawki usługi VSS i poprzednie wersje działają niezależnie od synchronizacji plików platformy Azure. Jednak warstwy w chmurze muszą być ustawione na tryb zgodny. Wiele punktów końcowych serwera synchronizacji plików platformy Azure może istnieć na tym samym woluminie. Należy wykonać następujące wywołanie programu PowerShell na wolumin, który ma nawet jeden punkt końcowy serwera, w którym planujesz lub używasz warstwy w chmurze.

```powershell
Import-Module ‘<SyncAgentInstallPath>\StorageSync.Management.ServerCmdlets.dll’
Enable-StorageSyncSelfServiceRestore [-DriveLetter] <string> [[-Force]] 
```

Migawki usługi VSS są pobierane z całego woluminu. Domyślnie dla danego woluminu może istnieć maksymalnie 64 migawki, a przyznane jest wystarczająco dużo miejsca do przechowywania migawek. Usługa VSS obsługuje to automatycznie. Domyślny harmonogram migawki wykonuje dwie migawki dziennie, od poniedziałku do piątku. Ten harmonogram można konfigurować za pomocą zaplanowanego zadania systemu Windows. Powyższe polecenie cmdlet programu PowerShell wykonuje dwie czynności:
1. Konfiguruje warstwy chmury synchronizacji plików platformy Azure na określonym woluminie, aby były zgodne z poprzednimi wersjami i gwarantuje, że plik można przywrócić z poprzedniej wersji, nawet jeśli został warstwowy do chmury na serwerze. 
2. Włącza domyślny harmonogram vss. Następnie można zdecydować, aby zmodyfikować go później. 

> [!Note]  
> Należy zwrócić uwagę na dwie ważne rzeczy:
>- Jeśli używasz parametru -Force, a usługa VSS jest obecnie włączona, zastąpi on bieżący harmonogram migawki usługi VSS i zastąpi go harmonogramem domyślnym. Przed uruchomieniem polecenia cmdlet upewnij się, że zapisujesz konfigurację niestandardową.
> - Jeśli używasz tego polecenia cmdlet w węźle klastra, należy również uruchomić go na wszystkich innych węzłach w klastrze! 

Aby sprawdzić, czy zgodność z przywracania samoobsługowego jest włączona, można uruchomić następujące polecenie cmdlet.

```powershell
    Get-StorageSyncSelfServiceRestore [[-Driveletter] <string>]
```

Będzie lista wszystkich woluminów na serwerze, jak również liczbę dni zgodnych z warstwami w chmurze dla każdego. Liczba ta jest obliczana automatycznie na podstawie maksymalnej możliwej migawki na wolumin i domyślnego harmonogramu migawki. Tak więc domyślnie wszystkie poprzednie wersje przedstawione do procesu roboczego informacji mogą być używane do przywracania z. To samo dotyczy zmiany domyślnego harmonogramu, aby wykonać więcej migawek.
Jeśli jednak zmienisz harmonogram w sposób, który spowoduje udostępniną migawkę na woluminie, który jest starszy niż zgodna wartość dni, użytkownicy nie będą mogli użyć tej starszej migawki (poprzedniej wersji) do przywrócenia.

> [!Note]
> Włączenie samoobsługowego przywracania może mieć wpływ na zużycie magazynu platformy Azure i rachunek. Ten wpływ jest ograniczony do plików aktualnie warstwowych na serwerze. Włączenie tej funkcji gwarantuje, że w chmurze jest dostępna wersja pliku, do którego można się odwoływać za pomocą wpisu poprzedniej wersji (migawki VSS).
>
> Jeśli ta funkcja zostanie wyłączyna, zużycie magazynu platformy Azure będzie powoli spadać, dopóki nie upłynie okno zgodne dni. Nie ma sposobu, aby to przyspieszyć. 

Domyślna maksymalna liczba migawek usługi VSS na wolumin (64) oraz domyślny harmonogram ich podjęcia, powoduje maksymalnie 45 dni poprzednich wersji, z których proces pracy informacji może przywrócić, w zależności od liczby migawek usługi VSS, które można przechowywać na woluminie.

Jeśli maks. 64 migawki VSS na wolumin nie jest prawidłowe ustawienie dla Ciebie, można [zmienić tę wartość za pomocą klucza rejestru](https://docs.microsoft.com/windows/win32/backup/registry-keys-for-backup-and-restore#maxshadowcopies).
Aby nowy limit został wprowadzony, należy ponownie uruchomić polecenie cmdlet, aby włączyć zgodność poprzedniej wersji na każdym woluminie, który został wcześniej włączony, z flagą -Force, aby uwzględnić nową maksymalną liczbę migawek usługi VSS na wolumin. Spowoduje to nowo obliczoną liczbę zgodnych dni. Należy pamiętać, że ta zmiana będzie obowiązywać tylko w przypadku nowo warstwowych plików i zastąpić wszelkie dostosowania w harmonogramie usługi VSS, które mogły zostać wprowadzone.

## <a name="migrate-a-dfs-replication-dfs-r-deployment-to-azure-file-sync"></a>Migrowanie wdrożenia replikacji systemu plików DFS (DFS-R) do usługi Azure File Sync
Aby przeprowadzić migrację wdrożenia systemu DFS-R do usługi Azure File Sync:

1. Utwórz grupę synchronizacji reprezentującą zastąpianą topologię systemu plików DFS-R.
2. Uruchom na serwerze, na który ma pełny zestaw danych w topologii systemu DFS-R do migracji. Zainstaluj usługę Azure File Sync na tym serwerze.
3. Zarejestruj ten serwer i utwórz punkt końcowy serwera dla pierwszego serwera, który ma zostać zmigrowany. Nie należy włączać warstw w chmurze.
4. Pozwól synchronizacji wszystkich danych z udziałem plików platformy Azure (punkt końcowy w chmurze).
5. Zainstaluj i zarejestruj agenta usługi Azure File Sync na każdym z pozostałych serwerów DFS-R.
6. Wyłącz DFS-R. 
7. Utwórz punkt końcowy serwera na każdym z serwerów DFS-R. Nie należy włączać warstw w chmurze.
8. Upewnij się, że synchronizacja zostanie zakończona i przetestuj topologię zgodnie z potrzebami.
9. Wycofaj DFS-R.
10. Warstwy w chmurze mogą być teraz włączone w dowolnym punkcie końcowym serwera zgodnie z potrzebami.

Aby uzyskać więcej informacji, zobacz [Interop synchronizacji plików azure z rozproszonym systemem plików (DFS)](storage-sync-files-planning.md#distributed-file-system-dfs).

## <a name="next-steps"></a>Następne kroki
- [Dodawanie lub usuwanie punktu końcowego serwera synchronizacji plików platformy Azure](storage-sync-files-server-endpoint.md)
- [Rejestrowanie lub wyrejestrowywalnia serwera za pomocą usługi Azure File Sync](storage-sync-files-server-registration.md)
- [Monitorowanie usługi Azure File Sync](storage-sync-files-monitoring.md)
