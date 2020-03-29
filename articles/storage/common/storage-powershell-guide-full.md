---
title: Korzystanie z programu Azure PowerShell z usługą Azure Storage | Dokumenty firmy Microsoft
description: Dowiedz się, jak używać poleceń cmdlet programu Azure PowerShell dla usługi Azure Storage.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 08/16/2018
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: d2404ee58f5f44fbe5625f267e6d1c504d0bd237
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75465097"
---
# <a name="using-azure-powershell-with-azure-storage"></a>Używanie programu Azure PowerShell z usługą Azure Storage

Program Azure PowerShell służy do tworzenia zasobów platformy Azure i zarządzania nimi z wiersza polecenia programu PowerShell lub w skryptach. W przypadku usługi Azure Storage te polecenia cmdlet dzielą się na dwie kategorie — płaszczyznę sterowania i płaszczyznę danych. Polecenia cmdlet płaszczyzny sterowania służą do zarządzania kontem magazynu — do tworzenia kont magazynu, ustawiania właściwości, usuwania kont magazynu, obracania kluczy dostępu itd. Polecenia cmdlet płaszczyzny danych są używane do zarządzania danymi *przechowywanymi* na koncie magazynu. Na przykład przekazywanie obiektów blob, tworzenie udziałów plików i dodawanie wiadomości do kolejki.

W tym artykule opisano typowe operacje przy użyciu poleceń cmdlet płaszczyzny zarządzania do zarządzania kontami magazynu. Omawiane kwestie:

> [!div class="checklist"]
> * Lista kont magazynu
> * Uzyskaj odwołanie do istniejącego konta magazynu
> * Tworzenie konta magazynu
> * Ustawianie właściwości konta magazynu
> * Pobieranie i ponowne generowanie kluczy dostępu
> * Ochrona dostępu do konta magazynu
> * Włącz analizę magazynu

Ten artykuł zawiera łącza do kilku innych artykułów programu PowerShell dla magazynu, takich jak włączanie analizy magazynu i uzyskiwanie do niej dostępu, używanie poleceń cmdlet płaszczyzny danych oraz uzyskiwanie dostępu do niezależnych chmur platformy Azure, takich jak China Cloud, German Cloud i Government Chmurze.

Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) przed rozpoczęciem.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

To ćwiczenie wymaga modułu Azure PowerShell Az w wersji 0.7 lub nowszej. Uruchom polecenie `Get-Module -ListAvailable Az`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie modułu Azure PowerShell](/powershell/azure/install-Az-ps).

W tym ćwiczeniu można wpisać polecenia w zwykłym oknie programu PowerShell lub użyć [zintegrowanego środowiska skryptów programu Windows PowerShell (ISE)](/powershell/scripting/components/ise/exploring-the-windows-powershell-ise) i wpisać polecenia w edytorze, a następnie przetestować jedno lub więcej poleceń naraz podczas przechodzenia przez przykłady. Możesz wyróżnić wiersze, które chcesz wykonać, a następnie kliknąć przycisk Uruchom zaznaczone, aby po prostu uruchomić te polecenia.

Aby uzyskać więcej informacji na temat kont magazynu, zobacz [Wprowadzenie do magazynu](storage-introduction.md) i informacje o [kontach magazynu platformy Azure](storage-create-storage-account.md).

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Zaloguj się do subskrypcji platformy Azure za pomocą polecenia `Connect-AzAccount` i postępuj zgodnie z instrukcjami wyświetlanymi na ekranie.

```powershell
Connect-AzAccount
```

## <a name="list-the-storage-accounts-in-the-subscription"></a>Wyświetlanie listy kont magazynu w ramach subskrypcji

Uruchom polecenie cmdlet [Get-AzStorageAccount,](/powershell/module/az.storage/Get-azStorageAccount) aby pobrać listę kont magazynu w bieżącej subskrypcji.

```powershell
Get-AzStorageAccount | Select StorageAccountName, Location
```

## <a name="get-a-reference-to-a-storage-account"></a>Uzyskaj odwołanie do konta magazynu

Następnie potrzebujesz odwołania do konta magazynu. Można utworzyć nowe konto magazynu lub uzyskać odwołanie do istniejącego konta magazynu. W poniższej sekcji przedstawiono obie metody.

### <a name="use-an-existing-storage-account"></a>Używanie istniejącego konta magazynu

Aby pobrać istniejące konto magazynu, potrzebna jest nazwa grupy zasobów i nazwa konta magazynu. Ustaw zmienne dla tych dwóch pól, a następnie użyj polecenia cmdlet [Get-AzStorageAccount.](/powershell/module/az.storage/Get-azStorageAccount)

```powershell
$resourceGroup = "myexistingresourcegroup"
$storageAccountName = "myexistingstorageaccount"

$storageAccount = Get-AzStorageAccount -ResourceGroupName $resourceGroup `
  -Name $storageAccountName
```

Teraz masz $storageAccount, który wskazuje na istniejące konto magazynu.

### <a name="create-a-storage-account"></a>Tworzenie konta magazynu

W poniższym skrypcie pokazano, jak utworzyć ogólne przeznaczenie konta magazynu przy użyciu [new-AzStorageAccount](/powershell/module/az.storage/New-azStorageAccount). Po utworzeniu konta pobierz jego kontekst, który może być używany w kolejnych poleceniach, zamiast określać uwierzytelnianie przy każdym wywołaniu.

```powershell
# Get list of locations and select one.
Get-AzLocation | select Location
$location = "eastus"

# Create a new resource group.
$resourceGroup = "teststoragerg"
New-AzResourceGroup -Name $resourceGroup -Location $location

# Set the name of the storage account and the SKU name.
$storageAccountName = "testpshstorage"
$skuName = "Standard_LRS"

# Create the storage account.
$storageAccount = New-AzStorageAccount -ResourceGroupName $resourceGroup `
  -Name $storageAccountName `
  -Location $location `
  -SkuName $skuName

# Retrieve the context.
$ctx = $storageAccount.Context
```

Skrypt używa następujących poleceń cmdlet programu PowerShell:

*   [Get-AzLocation](/powershell/module/az.resources/get-azlocation) — pobiera listę prawidłowych lokalizacji. W przykładzie `eastus` użyto lokalizacji.

*   [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) — tworzy nową grupę zasobów. Grupa zasobów to kontener logiczny, w którym zasoby platformy Azure są wdrażane i zarządzane. Nasz nazywa `teststoragerg`się .

*   [New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount) — tworzy konto magazynu. W przykładzie `testpshstorage`użyto pliku .

Nazwa jednostki SKU wskazuje typ replikacji dla konta magazynu, na przykład LRS (Locally Redundant Storage). Aby uzyskać więcej informacji na temat replikacji, zobacz [Replikacja usługi Azure Storage](storage-redundancy.md).

> [!IMPORTANT]
> Nazwa konta magazynu musi być unikatowa na platformie Azure i musi być mała litera. Aby zapoznać się z konwencjami i ograniczeniami nazewnictwa, zobacz [Nazewnictwo i odwoływanie się do kontenerów, obiektów blob i metadanych](/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata).
>

Teraz masz nowe konto magazynu i odwołanie do niego.

## <a name="manage-the-storage-account"></a>Zarządzanie kontem magazynu

Teraz, gdy masz odwołanie do nowego konta magazynu lub istniejącego konta magazynu, w poniższej sekcji przedstawiono niektóre polecenia, których można użyć do zarządzania kontem magazynu.

### <a name="storage-account-properties"></a>Właściwości konta magazynu

Aby zmienić ustawienia konta magazynu, użyj [funkcji Set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount). Chociaż nie można zmienić lokalizacji konta magazynu lub grupy zasobów, w której się znajduje, można zmienić wiele innych właściwości. Poniżej wymieniono niektóre właściwości, które można zmienić za pomocą programu PowerShell.

* **Domena niestandardowa** przypisana do konta magazynu.

* **Tagi przypisane** do konta magazynu. Tagi są często używane do kategoryzowania zasobów do celów rozliczeniowych.

* Jednostka **SKU** jest ustawieniem replikacji dla konta magazynu, takim jak LRS dla magazynu lokalnie nadmiarowego. Na przykład można zmienić\_ze standardowego LRS na standard GRS\_lub standard\_RAGRS. Należy pamiętać, że nie\_można zmienić standardu ZRS, Standard\_GZRS, Standard\_RAGZRS lub Premium\_LRS na inne jednostki SKU ani zmieniać innych jednostek SKU na te.

* Warstwa **dostępu** dla kont magazynu obiektów Blob. Wartość warstwy dostępu jest ustawiona na **gorąca** lub **chłodna**i umożliwia zminimalizowanie kosztów przez wybranie warstwy dostępu, która jest zgodna ze sposobu korzystania z konta magazynu. Aby uzyskać więcej informacji, zobacz [Warstwy gorąca, chłodna i archiwizowana pamięć masowa](../blobs/storage-blob-storage-tiers.md).

* Zezwalaj tylko na ruch HTTPS.

### <a name="manage-the-access-keys"></a>Zarządzanie kluczami dostępu

Konto usługi Azure Storage zawiera dwa klucze konta. Aby pobrać klucze, użyj [get-AzStorageAccountKey](/powershell/module/az.Storage/Get-azStorageAccountKey). W tym przykładzie pobiera pierwszy klucz. Aby pobrać drugi, użyj `Value[1]` zamiast `Value[0]`.

```powershell
$storageAccountKey = `
    (Get-AzStorageAccountKey `
    -ResourceGroupName $resourceGroup `
    -Name $storageAccountName).Value[0]
```

Aby ponownie wygenerować klucz, użyj [programu New-AzStorageAccountKey](/powershell/module/az.Storage/New-azStorageAccountKey).

```powershell
New-AzStorageAccountKey -ResourceGroupName $resourceGroup `
  -Name $storageAccountName `
  -KeyName key1
```

Aby ponownie wygenerować `key2` inny klucz, użyj `key1`jako nazwy klucza zamiast .

Ponownie wygeneruj jeden z kluczy, a następnie pobierz go ponownie, aby wyświetlić nową wartość.

> [!NOTE]
> Przed wygenerowaniem klucza dla produkcyjnego konta magazynu należy wykonać staranne planowanie. Ponowne generowanie jednego lub obu kluczy spowoduje unieważnienie dostępu dla dowolnej aplikacji przy użyciu klucza, który został ponownie wygenerowany. Aby uzyskać więcej informacji, zobacz [Zarządzanie kluczami dostępu do konta magazynu](storage-account-keys-manage.md).


### <a name="delete-a-storage-account"></a>Usuwanie konta magazynu

Aby usunąć konto magazynu, użyj [usuń konto AzStorageAccount](/powershell/module/az.storage/Remove-azStorageAccount).

```powershell
Remove-AzStorageAccount -ResourceGroup $resourceGroup -AccountName $storageAccountName
```

> [!IMPORTANT]
> Po usunięciu konta magazynu wszystkie zasoby przechowywane na koncie są również usuwane. Jeśli przypadkowo usuniesz konto, natychmiast zadzwoń do pomocy technicznej i otwórz bilet, aby przywrócić konto magazynu. Odzyskiwanie danych nie jest gwarantowane, ale czasami działa. Nie należy tworzyć nowego konta magazynu o takiej samej nazwie jak stare, dopóki bilet pomocy technicznej nie zostanie rozwiązany.
>

### <a name="protect-your-storage-account-using-vnets-and-firewalls"></a>Ochrona konta magazynu przy użyciu sieci wirtualnych i zapór sieciowych

Domyślnie wszystkie konta magazynu są dostępne dla każdej sieci, która ma dostęp do Internetu. Można jednak skonfigurować reguły sieciowe tak, aby zezwalać tylko aplikacjom z określonych sieci wirtualnych na dostęp do konta magazynu. Aby uzyskać więcej informacji, zobacz [Konfigurowanie zapór usługi Azure Storage i sieci wirtualnych](storage-network-security.md).

W tym artykule pokazano, jak zarządzać tymi ustawieniami przy użyciu następujących poleceń cmdlet programu PowerShell:
* [Reguła dodatku AzStorageAccountNetworkRule](/powershell/module/az.Storage/Add-azStorageAccountNetworkRule)
* [Update-AzStorageAccountNetworkRuleSet](/powershell/module/az.storage/update-azstorageaccountnetworkruleset)
* [Usuń-AzStorageAccountNetworkRule](https://docs.microsoft.com/powershell/module/az.storage/remove-azstorageaccountnetworkrule)

## <a name="use-storage-analytics"></a>Korzystanie z analizy pamięci masowej

[Usługa Azure Storage Analytics](storage-analytics.md) składa się z [metryk analizy magazynu](/rest/api/storageservices/about-storage-analytics-metrics) i [rejestrowania analizy magazynu.](/rest/api/storageservices/about-storage-analytics-logging)

**Metryki analizy magazynu** służy do zbierania metryk dla kont magazynu platformy Azure, które można użyć do monitorowania kondycji konta magazynu. Metryki mogą być włączone dla obiektów blob, plików, tabel i kolejek.

**Rejestrowanie analizy magazynu** odbywa się po stronie serwera i umożliwia rejestrowanie szczegółów zarówno dla pomyślnych, jak i nieudanych żądań na koncie magazynu. Te dzienniki umożliwiają wyświetlanie szczegółów operacji odczytu, zapisu i usuwania dla tabel, kolejek i obiektów blob, a także przyczyn nieudanych żądań. Rejestrowanie nie jest dostępne dla usługi Azure Files.

Monitorowanie można skonfigurować przy użyciu [portalu Azure Portal](https://portal.azure.com), PowerShell lub programowo przy użyciu biblioteki klienta magazynu.

> [!NOTE]
> Za pomocą programu PowerShell można włączyć analizę minutową. Ta funkcja nie jest dostępna w portalu.
>

* Aby dowiedzieć się, jak włączyć i wyświetlić dane metryk magazynu za pomocą programu PowerShell, zobacz [Metryki analizy magazynu](storage-analytics-metrics.md).

* Aby dowiedzieć się, jak włączyć i pobrać dane rejestrowania magazynu za pomocą programu PowerShell, zobacz [Jak włączyć rejestrowanie magazynu przy użyciu programu PowerShell](/rest/api/storageservices/Enabling-Storage-Logging-and-Accessing-Log-Data) i [znajdowanie danych dziennika rejestrowania magazynu](/rest/api/storageservices/Enabling-Storage-Logging-and-Accessing-Log-Data).

* Aby uzyskać szczegółowe informacje dotyczące używania metryk magazynu i rejestrowania magazynu w celu rozwiązywania problemów z magazynem, zobacz [Monitorowanie, diagnozowanie i rozwiązywanie problemów z usługą Microsoft Azure Storage](storage-monitoring-diagnosing-troubleshooting.md).

## <a name="manage-the-data-in-the-storage-account"></a>Zarządzanie danymi na koncie magazynu

Teraz, gdy wiesz, jak zarządzać kontem magazynu za pomocą programu PowerShell, możesz użyć następujących artykułów, aby dowiedzieć się, jak uzyskać dostęp do obiektów danych na koncie magazynu.

* [Jak zarządzać obiektami blob za pomocą programu PowerShell](../blobs/storage-how-to-use-blobs-powershell.md)
* [Jak zarządzać plikami za pomocą programu PowerShell](../files/storage-how-to-use-files-powershell.md)
* [Jak zarządzać kolejkami za pomocą programu PowerShell](../queues/storage-powershell-how-to-use-queues.md)
* [Wykonywanie operacji magazynu tabel platformy Azure za pomocą programu PowerShell](../../storage/tables/table-storage-how-to-use-powershell.md)

Interfejs API tabeli usługi Azure Cosmos DB udostępnia funkcje premium dla magazynu tabel, takie jak dystrybucja globalna pod klucz, odczyty i zapisy o małym opóźnieniu, automatyczne indeksowanie pomocnicze i dedykowana przepływność.

* Aby uzyskać więcej informacji, zobacz [Interfejs API tabeli usługi Azure Cosmos DB](../../cosmos-db/table-introduction.md).

## <a name="independent-cloud-deployments-of-azure"></a>Niezależne wdrożenia platformy Azure w chmurze

Większość osób korzysta z usługi Azure Public Cloud do globalnego wdrażania platformy Azure. Istnieje również kilka niezależnych wdrożeń platformy Microsoft Azure ze względu na suwerenność i tak dalej. Te niezależne wdrożenia są określane jako "środowiska". Oto dostępne środowiska:

* [Chmura dla instytucji rządowych platformy Azure](https://azure.microsoft.com/features/gov/)
* [Usługa Azure China 21Vianet Cloud obsługiwana przez firmę 21Vianet w Chinach](http://www.windowsazure.cn/)
* [Niemiecka chmura platformy Azure](../../germany/germany-welcome.md)

Aby uzyskać informacje dotyczące uzyskiwania dostępu do tych chmur i ich magazynu za pomocą programu PowerShell, zobacz [Zarządzanie magazynem w niezależnych chmurach platformy Azure przy użyciu programu PowerShell](storage-powershell-independent-clouds.md).

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli utworzono nową grupę zasobów i konto magazynu dla tego ćwiczenia, yous można usunąć wszystkie zasoby utworzone przez usunięcie grupy zasobów. Spowoduje to również usunięcie wszystkich zasobów znajdujących się w grupie. W takim przypadku usuwa utworzone konto magazynu i samą grupę zasobów.

```powershell
Remove-AzResourceGroup -Name $resourceGroup
```
## <a name="next-steps"></a>Następne kroki

W tym artykule opisano typowe operacje przy użyciu poleceń cmdlet płaszczyzny zarządzania do zarządzania kontami magazynu. W tym samouczku omówiono:

> [!div class="checklist"]
> * Lista kont magazynu
> * Uzyskaj odwołanie do istniejącego konta magazynu
> * Tworzenie konta magazynu
> * Ustawianie właściwości konta magazynu
> * Pobieranie i ponowne generowanie kluczy dostępu
> * Ochrona dostępu do konta magazynu
> * Włącz analizę magazynu

W tym artykule podano również odwołania do kilku innych artykułów, takich jak jak zarządzać obiektami danych, jak włączyć usługę Analizy magazynu i jak uzyskać dostęp do niezależnych chmur platformy Azure, takich jak China Cloud, German Cloud i Government Cloud. Oto kilka powiązanych artykułów i zasobów w celach informacyjnych:

* [Polecenia cmdlet płaszczyzny sterowania usługi Azure Storage powershell](/powershell/module/az.storage/)
* [Polecenia cmdlet na płaszczyźnie danych usługi Azure Storage programu PowerShell](/powershell/module/azure.storage/)
* [Dokumentacja programu Windows PowerShell](/powershell/scripting/overview)
