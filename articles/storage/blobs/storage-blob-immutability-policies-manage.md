---
title: Ustawianie zasad niezmienności dla magazynu obiektów Blob i zarządzanie nimi — usługa Azure Storage
description: Dowiedz się, jak używać obsługi WORM (Write Once, Read Many) dla magazynu obiektów blob (obiektu) do przechowywania danych w stanie nieekserwowania, niemodyfikowalne dla określonego interwału.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 11/26/2019
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: 05a155584f0cb69191883cb82b3db0af435ccc12
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78970098"
---
# <a name="set-and-manage-immutability-policies-for-blob-storage"></a>Ustawianie zasad niezmienności dla magazynu obiektów Blob i zarządzanie nimi

Niezmienne magazynu dla magazynu obiektów Blob platformy Azure umożliwia użytkownikom do przechowywania obiektów danych o znaczeniu krytycznym dla firmy w WORM (Zapisz raz, Przeczytaj wiele) stan. Ten stan sprawia, że dane nie można wymazać i nie można modyfikować dla interwału określonego przez użytkownika. Przez cały czas trwania interwału przechowywania obiekty blob mogą być tworzone i odczytywane, ale nie można ich modyfikować ani usuwać. Niezmienne magazynu jest dostępna dla ogólnego przeznaczenia v2 i kont magazynu obiektów Blob we wszystkich regionach platformy Azure.

W tym artykule pokazano, jak ustawić zasady niezmienności i blokady prawne dotyczące danych w magazynie obiektów Blob przy użyciu witryny Azure portal, powershell lub interfejsu wiersza polecenia platformy Azure. Aby uzyskać więcej informacji na temat niezmiennego magazynu, zobacz [Przechowywanie danych obiektów blob o znaczeniu krytycznym dla firmy z niezmiennym magazynem](storage-blob-immutable-storage.md).

## <a name="set-retention-policies-and-legal-holds"></a>Ustawianie zasad przechowywania i blokad prawnych

### <a name="portal"></a>[Portal](#tab/azure-portal)

1. Utwórz nowy kontener lub wybierz istniejący kontener, w którym będą przechowywane obiekty blob wymagające przechowywania w stanie niezmiennym. Kontener musi znajdować się na koncie magazynu ogólnego przeznaczenia w wersji 2 lub blob.

2. Wybierz **pozycję Zasady programu Access** w ustawieniach kontenera. Następnie wybierz pozycję **Dodaj zasady** w obszarze Magazyn **obiektów blob niezmiennie**.

    ![Ustawienia kontenera w portalu](media/storage-blob-immutability-policies-manage/portal-image-1.png)

3. Aby włączyć przechowywanie na podstawie czasu, wybierz z menu rozwijanego **retencję opartą na czasie.**

    !["Retencja oparta na czasie" wybrana w obszarze "Typ zasad"](media/storage-blob-immutability-policies-manage/portal-image-2.png)

4. Wprowadź interwał przechowywania w dniach (dopuszczalne wartości to od 1 do 146000 dni).

    ![Pole "Aktualizuj okres przechowywania do"](media/storage-blob-immutability-policies-manage/portal-image-5-retention-interval.png)

    Początkowy stan zasad jest odblokowany, co pozwala przetestować tę funkcję i wprowadzić zmiany w zasadach przed jej zablokowaniem. Zablokowanie polityki jest niezbędne do zapewnienia zgodności z przepisami, takimi jak SEC 17a-4.

5. Zablokuj zasady. Kliknij prawym przyciskiem myszy wielokropek (**...**), a pojawi się następujące menu z dodatkowymi działaniami:

    !["Zasady blokowania" w menu](media/storage-blob-immutability-policies-manage/portal-image-4-lock-policy.png)

6. Wybierz **pozycję Zablokuj zasady** i potwierdź blokadę. Zasady są teraz zablokowane i nie można ich usunąć, dozwolone będą tylko rozszerzenia interwału przechowywania. Usunięcia obiektów blob i zastąpienia nie są dozwolone. 

    ![Potwierdź "Zablokuj zasady" w menu](media/storage-blob-immutability-policies-manage/portal-image-5-lock-policy.png)

7. Aby włączyć blokady prawne, wybierz pozycję **Dodaj zasady**. Z menu rozwijanego **wybierz pozycję Wstrzymanie prawne.**

    !["Wstrzymanie prawne" w menu w obszarze "Typ polityki"](media/storage-blob-immutability-policies-manage/portal-image-legal-hold-selection-7.png)

8. Utwórz blokadę prawną z co najmniej jednym tagiem.

    ![Pole "Nazwa znacznika" pod typem zasad](media/storage-blob-immutability-policies-manage/portal-image-set-legal-hold-tags.png)

9. Aby wyczyścić blokadę prawną, usuń zastosowany znacznik identyfikatora blokady prawnej.

### <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Funkcja jest zawarta w następujących `az storage container immutability-policy` grupach poleceń: i `az storage container legal-hold`. Uruchom `-h` na nich, aby zobaczyć polecenia.

### <a name="powershell"></a>[Powershell](#tab/azure-powershell)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Moduł Az.Storage obsługuje niezmienną pamięć masową.  Aby włączyć tę funkcję, wykonaj następujące czynności:

1. Upewnij się, że masz zainstalowaną najnowszą `Install-Module PowerShellGet –Repository PSGallery –Force`wersję programu PowerShellGet: .
2. Usuń wszystkie poprzednie instalacje programu Azure PowerShell.
3. Instalowanie programu Azure `Install-Module Az –Repository PSGallery –AllowClobber`PowerShell: .

Poniższy przykładowy skrypt programu PowerShell jest przeznaczony do odwołania. Ten skrypt tworzy nowe konto magazynu i kontener. Następnie pokazuje, jak ustawić i wyczyścić blokad prawnych, utworzyć i zablokować zasady przechowywania oparte na czasie (znany również jako zasady niezmienności) i przedłużyć interwał przechowywania.

Najpierw utwórz konto usługi Azure Storage:

```powershell
$resourceGroup = "<Enter your resource group>"
$storageAccount = "<Enter your storage account name>"
$container = "<Enter your container name>"
$location = "<Enter the storage account location>"

# Log in to Azure
Connect-AzAccount
Register-AzResourceProvider -ProviderNamespace "Microsoft.Storage"

# Create your Azure resource group
New-AzResourceGroup -Name $resourceGroup -Location $location

# Create your Azure storage account
$storageAccount = New-AzStorageAccount -ResourceGroupName $resourceGroup -StorageAccountName `
    $storageAccount -SkuName Standard_ZRS -Location $location -Kind StorageV2

# Create a new container using the context
$container = New-AzStorageContainer -Name $container -Context $account.Context

# List the containers in the account
Get-AzStorageContainer -Context $account.Context

# Remove a container
Remove-AzStorageContainer -Name $container -Context $account.Context
```

Zestaw i jasne blokady prawne:

```powershell
# Set a legal hold
Add-AzRmStorageContainerLegalHold -ResourceGroupName $resourceGroup `
    -StorageAccountName $storageAccount -Name $container -Tag <tag1>,<tag2>,...

# Clear a legal hold
Remove-AzRmStorageContainerLegalHold -ResourceGroupName $resourceGroup `
    -StorageAccountName $storageAccount -Name $container -Tag <tag3>
```

Tworzenie lub aktualizowanie zasad niezmienności opartych na czasie:

```powershell
# Create a time-based immutablity policy
Set-AzRmStorageContainerImmutabilityPolicy -ResourceGroupName $resourceGroup `
    -StorageAccountName $storageAccount -ContainerName $container -ImmutabilityPeriod 10
```

Pobieranie zasad niezmienności:

```powershell
# Get an immutability policy
Get-AzRmStorageContainerImmutabilityPolicy -ResourceGroupName $resourceGroup `
    -StorageAccountName $storageAccount -ContainerName $container
```

Blokowanie zasad niezmienności `-Force` (dodaj, aby odrzucić monit):

```powershell
# Lock immutability policies
$policy = Get-AzRmStorageContainerImmutabilityPolicy -ResourceGroupName `
    $resourceGroup -StorageAccountName $storageAccount -ContainerName $container
Lock-AzRmStorageContainerImmutabilityPolicy -ResourceGroupName `
    $resourceGroup -StorageAccountName $storageAccount -ContainerName $container `
    -Etag $policy.Etag
```

Rozszerzenie zasad niezmienności:

```powershell
# Extend immutability policies
$policy = Get-AzRmStorageContainerImmutabilityPolicy -ResourceGroupName `
    $resourceGroup -StorageAccountName $storageAccount -ContainerName $container

Set-AzRmStorageContainerImmutabilityPolicy -ImmutabilityPolicy `
    $policy -ImmutabilityPeriod 11 -ExtendPolicy
```

Usuń odblokowane zasady niezmienności `-Force` (dodaj, aby odrzucić monit):

```powershell
# Remove an unlocked immutability policy
$policy = Get-AzRmStorageContainerImmutabilityPolicy -ResourceGroupName `
    $resourceGroup -StorageAccountName $storageAccount -ContainerName $container

Remove-AzRmStorageContainerImmutabilityPolicy -ImmutabilityPolicy $policy
```

---

## <a name="enabling-allow-protected-append-blobs-writes"></a>Włączanie zezwalania na zapisy chronionych obiektów blob

### <a name="portal"></a>[Portal](#tab/azure-portal)

![Zezwalaj na dodatkowe zapisy dołączania](media/storage-blob-immutability-policies-manage/immutable-allow-additional-append-writes.png)

### <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Funkcja jest zawarta w następujących `az storage container immutability-policy` grupach poleceń: i `az storage container legal-hold`. Uruchom `-h` na nich, aby zobaczyć polecenia.

### <a name="powershell"></a>[Powershell](#tab/azure-powershell)

```powershell
# Create an immutablity policy with appends allowed
Set-AzRmStorageContainerImmutabilityPolicy -ResourceGroupName $resourceGroup `
    -StorageAccountName $storageAccount -ContainerName $container -ImmutabilityPeriod 10 -AllowProtectedAppendWrite $true
```

---

## <a name="next-steps"></a>Następne kroki

[Przechowywanie danych obiektów blob o znaczeniu krytycznym dla firmy z niezmiennym magazynem](storage-blob-immutable-storage.md)
