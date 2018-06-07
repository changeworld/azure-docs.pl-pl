---
title: Używanie programu Azure PowerShell z usługą Azure Storage | Dokumentacja firmy Microsoft
description: Dowiedz się, jak używać poleceń cmdlet programu Azure PowerShell dla usługi Azure Storage.
services: storage
documentationcenter: na
author: roygara
manager: jeconnoc
ms.assetid: f4704f58-abc6-4f89-8b6d-1b1659746f5a
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/02/2017
ms.author: rogarana
ms.openlocfilehash: 951b69877718c5da3c165c24c297906a1ad9a976
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34652505"
---
# <a name="using-azure-powershell-with-azure-storage"></a>Używanie programu Azure PowerShell z usługą Azure Storage

Program Azure PowerShell jest używany do tworzenia i zarządzania zasobami Azure z poziomu wiersza polecenia programu PowerShell lub w skryptach. Dla usługi Azure Storage te polecenia cmdlet można podzielić na dwie kategorie — płaszczyzny sterowania i płaszczyzny danych. Polecenia cmdlet płaszczyzny kontroli umożliwiają zarządzanie kontem magazynu — Tworzenie konta magazynu, ustaw właściwości, usunąć konta magazynu, Obróć klucze dostępu i tak dalej. Polecenia cmdlet płaszczyzna danych służą do zarządzania danymi przechowywanymi *w* konta magazynu. Na przykład obiekty BLOB przekazywanie, tworzenie udziałów plików i dodawanie wiadomości do kolejki.

W tym artykule opisano typowe operacje przy użyciu poleceń cmdlet płaszczyzny zarządzania można zarządzać kontami magazynu. Omawiane kwestie: 

> [!div class="checklist"]
> * Lista kont magazynu
> * Odwołać się do istniejącego konta magazynu
> * Tworzenie konta magazynu 
> * Właściwości konta magazynu
> * Pobierz i ponownie wygenerować klucze dostępu
> * Ochrona dostępu do konta magazynu 
> * Włącz analizy magazynu

Ten artykuł zawiera łącza do kilku innych artykułów programu PowerShell dla magazynu, takich jak sposobu włączania i uzyskać dostęp do analizy magazynu, jak używać poleceń cmdlet płaszczyzna danych i sposobu otwierania Azure chmur niezależnie od takich jak chmury Chin, niemiecki chmury i dla instytucji rządowych Chmura.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

Tego ćwiczenia wymaga programu Azure PowerShell modułu wersja 4.4 lub nowsza. Uruchom polecenie `Get-Module -ListAvailable AzureRM`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie modułu Azure PowerShell](/powershell/azure/install-azurerm-ps). 

W tym ćwiczeniu polecenia można wpisać w regularnych okno programu PowerShell, albo użyć [Windows PowerShell Integrated Scripting Environment (ISE)](/powershell/scripting/getting-started/fundamental/windows-powershell-integrated-scripting-environment--ise-) i wpisz polecenia do edytora, a następnie przetestować co najmniej jedno polecenie jednocześnie jako można przejść przez przykłady. Można wyróżnić wiersze, które chcesz wykonać i kliknij przycisk Uruchom wybrane nieco uruchamiania tych poleceń.

Aby uzyskać więcej informacji o kontach magazynu, zobacz [wprowadzenie do magazynu](storage-introduction.md) i [kont magazynu Azure o](storage-create-storage-account.md).

## <a name="log-in-to-azure"></a>Zaloguj się do platformy Azure.

Zaloguj się do subskrypcji platformy Azure za pomocą polecenia `Connect-AzureRmAccount` i postępuj zgodnie z instrukcjami wyświetlanymi na ekranie.

```powershell
Connect-AzureRmAccount
```

## <a name="list-the-storage-accounts-in-the-subscription"></a>Lista kont magazynu w ramach subskrypcji

Uruchom [Get-AzureRMStorageAccount](/powershell/module/azurerm.resources/get-azurermstorageaccount) polecenia cmdlet, aby pobrać listę kont magazynu w bieżącej subskrypcji. 

```powershell
Get-AzureRMStorageAccount | Select StorageAccountName, Location
```

## <a name="get-a-reference-to-a-storage-account"></a>Pobierz odwołanie do konta magazynu

Następnie należy odwołanie do konta magazynu. Możesz utworzyć nowe konto magazynu lub odwołać się do istniejącego konta magazynu. W tej części opisano obie metody. 

### <a name="use-an-existing-storage-account"></a>Użyj istniejącego konta magazynu 

Aby uzyskać dostęp do istniejącego konta magazynu, należy nazwę grupy zasobów i nazwę konta magazynu. Ustaw zmienne dla tych dwóch pól, a następnie użyj [Get-AzureRmStorageAccount](/powershell/module/azurerm.storage/Get-AzureRmStorageAccount) polecenia cmdlet. 

```powershell
$resourceGroup = "myexistingresourcegroup"
$storageAccountName = "myexistingstorageaccount"

$storageAccount = Get-AzureRmStorageAccount -ResourceGroupName $resourceGroup `
  -Name $storageAccountName 
```

Teraz masz $storageAccount, która wskazuje na istniejące konto magazynu.

### <a name="create-a-storage-account"></a>Tworzenie konta magazynu 

Poniższy skrypt pokazuje, jak utworzyć konto magazynu ogólnego przeznaczenia przy użyciu [New-AzureRmStorageAccount](/powershell/module/azurerm.storage/New-AzureRmStorageAccount). Po utworzeniu konta należy pobrać jego kontekstu, którego można użyć w kolejnych poleceniach odwołująca uwierzytelnianie przy każdym wywołaniu.

```powershell
# Get list of locations and select one.
Get-AzureRmLocation | select Location 
$location = "eastus"

# Create a new resource group.
$resourceGroup = "teststoragerg"
New-AzureRmResourceGroup -Name $resourceGroup -Location $location 

# Set the name of the storage account and the SKU name. 
$storageAccountName = "testpshstorage"
$skuName = "Standard_LRS"
    
# Create the storage account.
$storageAccount = New-AzureRmStorageAccount -ResourceGroupName $resourceGroup `
  -Name $storageAccountName `
  -Location $location `
  -SkuName $skuName

# Retrieve the context. 
$ctx = $storageAccount.Context
```

Skrypt używa następujących poleceń cmdlet programu PowerShell: 

*   [Get-AzureRmLocation](/powershell/module/azurerm.storage/Get-AzureRmLocation) — pobiera listę prawidłowych lokalizacji. W przykładzie użyto `eastus` dla lokalizacji.

*   [Nowy AzureRmResourceGroup](/powershell/module/azurerm.resources/New-AzureRmResourceGroup) — tworzy nową grupę zasobów. Grupa zasobów jest kontenerem logicznym, w której są wdrożone i zarządzane zasobów platformy Azure. Nasza nosi nazwę `teststoragerg`. 

*   [Nowy AzureRmStorageAccount](/powershell/module/azurerm.resources/New-AzureRmStorageAcccount) — tworzy konto magazynu rzeczywista. W przykładzie użyto `testpshstorage`.

Nazwa jednostki SKU wskazuje typ replikacji dla konta magazynu, takich jak LRS (magazyn lokalnie nadmiarowy). Aby uzyskać więcej informacji na temat replikacji, zobacz [replikacji magazynu Azure](storage-redundancy.md).

> [!IMPORTANT]
> Nazwa konta magazynu musi być unikatowa w obrębie platformy Azure i muszą być małymi literami. Ograniczenia i konwencje nazewnictwa, zobacz [nazewnictwa i odwołuje się do kontenerów, obiektów blob i metadanych](/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata).
> 

Teraz masz nowe konto magazynu i odwołanie do niej. 

## <a name="manage-the-storage-account"></a>Zarządzanie kontem magazynu

Teraz, gdy masz odwołanie do nowego konta magazynu lub istniejącego konta magazynu w tej części opisano niektóre polecenia, których można użyć do zarządzania konta magazynu.

### <a name="storage-account-properties"></a>Właściwości konta magazynu

Aby zmienić ustawienia konta magazynu, należy użyć [Set-AzureRmStorageAccount](/powershell/module/azurerm.resources/Set-AzureRmStorageAccount). Gdy nie można zmienić lokalizacji konta magazynu lub grupy zasobów, w której się znajduje, można zmienić wiele innych właściwości. Poniższa lista zawiera niektóre właściwości, które można zmienić za pomocą programu PowerShell.

* **Domeny niestandardowej** przypisane do konta magazynu.

* **Tagi** przypisane do konta magazynu. Tagi są często używane do klasyfikowania zasobów do celów rozliczeń.

* **SKU** jest ustawienie replikacji dla konta magazynu, takich jak LRS lokalnie nadmiarowego magazynu. Na przykład można zmienić zgodne ze standardem\_LRS, Standard\_GRS lub Standard\_RAGRS. Należy pamiętać, że nie można zmienić Standard\_ZRS lub Premium\_LRS na inne wersje, lub zmień te inne wersje.

* **Warstwy dostępu** dla konta magazynu obiektów Blob. Ustawiono wartość dla warstwy dostępu **gorących** lub **chłodnych**, i pozwala zminimalizować koszty, wybierając warstwy dostępu, aby była zgodna z jak używasz konta magazynu. Aby uzyskać więcej informacji, zobacz [Hot, ostudzić oraz archiwum warstw magazynowania](../blobs/storage-blob-storage-tiers.md).

* Zezwalaj tylko na ruch protokołu HTTPS. 

### <a name="manage-the-access-keys"></a>Zarządzaj kluczami dostępu

Konto magazynu Azure ma dwa klucze konta. Aby uzyskać dostęp do kluczy, należy użyć [Get-AzureRmStorageAccountKey](/powershell/module/AzureRM.Storage/Get-AzureRmStorageAccountKey). W tym przykładzie pobierana pierwszego klucza. Aby pobrać innego, należy użyć `Value[1]` zamiast `Value[0]`.

```powershell
$storageAccountKey = `
    (Get-AzureRmStorageAccountKey `
    -ResourceGroupName $resourceGroup `
    -Name $storageAccountName).Value[0]
```

Aby ponownie wygenerować klucz, należy użyć [AzureRmStorageAccountKey nowy](/powershell/module/AzureRM.Storage/New-AzureRmStorageAccountKey). 

```powershell
New-AzureRmStorageAccountKey -ResourceGroupName $resourceGroup `
  -Name $storageAccountName `
  -KeyName key1 
```

Aby ponownie wygenerować klucz innych, należy użyć `key2` jako nazwę klucza, a nie `key1`.

Wygeneruj ponownie jeden z kluczy, a następnie pobrać go ponownie, aby wyświetlić nową wartość.

> [!NOTE] 
> Należy przeprowadzić dokładne planowanie przed Trwa ponowne generowanie klucza dla konta magazynu produkcji. Trwa ponowne generowanie kluczy jeden lub oba spowoduje unieważnienie dostępu dla dowolnej aplikacji przy użyciu klucza, który został ponownie wygenerowany. Aby uzyskać więcej informacji, zobacz [ponowne generowanie kluczy dostępu do magazynu](storage-create-storage-account.md#regenerate-storage-access-keys).


### <a name="delete-a-storage-account"></a>Usuwanie konta magazynu 

Aby usunąć konto magazynu, należy użyć [Remove-AzureRmStorageAccount](/powershell/module/azurerm.storage/Remove-AzureRmStorageAccount).

```powershell
Remove-AzureRmStorageAccount -ResourceGroup $resourceGroup -AccountName $storageAccountName
```

> [!IMPORTANT]
> Podczas usuwania konta magazynu, wszystkie zasoby przechowywane na koncie są również usunięte. Jeśli przypadkowo usunąć konto z działem pomocy technicznej natychmiast i otworzyć bilet można przywrócić konta magazynu. Odzyskiwanie danych nie jest gwarantowana, ale czasami działa. Nie należy tworzyć nowe konto magazynu o tej samej nazwie jak stary, dopóki biletu pomocy technicznej zostanie rozwiązany. 
>

### <a name="protect-your-storage-account-using-vnets-and-firewalls"></a>Chronić swoje konto magazynu przy użyciu sieci wirtualnych i zapór

Domyślnie wszystkie konta magazynu są dostępne dla każdej sieci, który ma dostęp do Internetu. Można jednak skonfigurować reguły sieci, aby zezwolić tylko aplikacji z określonych sieci wirtualnych, aby uzyskać dostęp do konta magazynu. Aby uzyskać więcej informacji, zobacz [Konfigurowanie zapory magazynu Azure i sieci wirtualne](storage-network-security.md). 

Artykuł pokazano, jak zarządzać tymi ustawieniami za pomocą następujących poleceń cmdlet programu PowerShell:
* [Add-AzureRmStorageAccountNetworkRule](/powershell/module/AzureRM.Storage/Add-AzureRmStorageAccountNetworkRule)
* [Update-AzureRmStorageAccountNetworkRuleSet](/powershell/module/azurerm.storage/update-azurermstorageaccountnetworkruleset)
* [Remove-AzureRmStorageAccountNetworkRule](/powershell/module/azurerm.storage/remove-azurermstorage-account-networkrule)

## <a name="use-storage-analytics"></a>Użyj magazynu analityka  

[Analizy usługi Azure Storage](storage-analytics.md) składa się z [metryki analityka magazynu](/rest/api/storageservices/about-storage-analytics-metrics) i [rejestrowania analityka magazynu](/rest/api/storageservices/about-storage-analytics-logging). 

**Metryki analityka magazynu** jest używany na potrzeby zbierania miar dla kont usługi Azure storage, które służy do monitorowania prawidłowości konta magazynu. Można włączyć metryki dla obiektów blob, plików, tabel i kolejek.

**Rejestrowanie analityka magazynu** wykonywany po stronie serwera i umożliwia utworzenie rekordów szczegółów pomyślnie i niepomyślnie żądań do konta magazynu. Dzienniki te umożliwiają szczegóły odczytu, zapisu i usuwania działań przed tabel, kolejek i obiektów blob, a także przyczyny żądań zakończonych niepowodzeniem. Rejestrowanie nie jest dostępne dla plików Azure.

Można skonfigurować monitorowanie za pomocą [portalu Azure](https://portal.azure.com), programu PowerShell, albo programowo z użyciem biblioteki klienta usługi storage. 

> [!NOTE]
> Można włączyć minuty analytics przy użyciu programu PowerShell. Ta funkcja nie jest dostępne w portalu.
>

* Aby dowiedzieć się, jak włączyć i wyświetlania metryk magazynu danych przy użyciu programu PowerShell, zobacz [włączania usługi Azure Storage metryki i wyświetlanie danych metryk](storage-enable-and-view-metrics.md#how-to-enable-metrics-using-powershell).

* Aby dowiedzieć się, jak włączyć i pobrać rejestrowania magazynu danych przy użyciu programu PowerShell, zobacz [jak włączyć rejestrowanie magazynu przy użyciu programu PowerShell](/rest/api/storageservices/Enabling-Storage-Logging-and-Accessing-Log-Data#how-to-enable-storage-logging-using-powershell) i [znajdowanie rejestrowania magazynu danych dziennika](/rest/api/storageservices/Enabling-Storage-Logging-and-Accessing-Log-Data#finding-your-storage-logging-log-data).

* Aby uzyskać szczegółowe informacje dotyczące rozwiązywania problemów z magazynowaniem za pomocą metryki magazynu i rejestrowanie magazynu, zobacz [monitorowanie, diagnozowanie i rozwiązywanie problemów z programem Microsoft Azure Storage](storage-monitoring-diagnosing-troubleshooting.md).

## <a name="manage-the-data-in-the-storage-account"></a>Zarządzanie danymi w ramach konta magazynu

Teraz, że rozumiesz, jak zarządzać kontem magazynu za pomocą programu PowerShell, można skorzystaj z poniższych artykułów, aby dowiedzieć się, jak uzyskać dostęp do obiektów danych na koncie magazynu.

* [Jak zarządzać obiektów blob przy użyciu programu PowerShell](../blobs/storage-how-to-use-blobs-powershell.md)
* [Jak zarządzać plikami przy użyciu programu PowerShell](../files/storage-how-to-use-files-powershell.md)
* [Jak zarządzać kolejki przy użyciu programu PowerShell](../queues/storage-powershell-how-to-use-queues.md)
* [Wykonywanie operacji magazynu tabel Azure przy użyciu programu PowerShell](../../storage/tables/table-storage-how-to-use-powershell.md)

Interfejsu API Azure rozwiązania Cosmos DB tabeli zawiera funkcje premium magazynu tabel gotowe dystrybucji globalnych, odczyty małe opóźnienia i zapisy, automatycznego indeksowania dodatkowej i dedykowanych przepływności. 

* Aby uzyskać więcej informacji, zobacz [interfejsu API Azure rozwiązania Cosmos DB tabeli](../../cosmos-db/table-introduction.md). 
* Aby dowiedzieć się, jak wykonywać operacje interfejsu API Azure rozwiązania Cosmos DB tabeli za pomocą programu PowerShell, zobacz [operacji wykonania rozwiązania Cosmos DB tabeli interfejsu API Azure przy użyciu programu PowerShell](../../cosmos-db/table-powershell.md).

## <a name="independent-cloud-deployments-of-azure"></a>Wdrożenia chmury niezależnie od platformy Azure

Większość użytkowników użyj chmury publicznej Azure globalne wdrożenia usługi Azure. Istnieją również niektórych niezależnie od wdrożenia programu Microsoft Azure ze względu na suwerenności i tak dalej. Te niezależne wdrożenia są określane jako "środowisk". Poniżej przedstawiono dostępne środowiskach:

* [Chmury Azure dla instytucji rządowych](https://azure.microsoft.com/features/gov/)
* [Chmury Azure Chin przez 21Vianet w Chinach](http://www.windowsazure.cn/)
* [Niemiecki chmury Azure](../../germany/germany-welcome.md)

Aby uzyskać informacje na temat dostępu do tych chmur i ich magazynu przy użyciu programu PowerShell, zobacz [Zarządzanie magazynu w Azure niezależne chmur przy użyciu programu PowerShell](storage-powershell-independent-clouds.md).

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli utworzono nową grupę zasobów i konto magazynu dla tego ćwiczenia yous można usunąć wszystkie zasoby utworzone przez usunięcie grupy zasobów. Spowoduje to również usunięcie wszystkich zasobów znajdujących się w grupie. W takim przypadku usuwa utworzono konto magazynu i grupy zasobów, do samej siebie.

```powershell
Remove-AzureRmResourceGroup -Name $resourceGroup
```
## <a name="next-steps"></a>Kolejne kroki

W tym artykule opisano typowe operacje przy użyciu poleceń cmdlet płaszczyzny zarządzania można zarządzać kontami magazynu. W tym samouczku omówiono: 

> [!div class="checklist"]
> * Lista kont magazynu
> * Odwołać się do istniejącego konta magazynu
> * Tworzenie konta magazynu 
> * Właściwości konta magazynu
> * Pobierz i ponownie wygenerować klucze dostępu
> * Ochrona dostępu do konta magazynu 
> * Włącz analizy magazynu

W tym artykule w nim również podane odwołania do kilku inne artykuły, takie jak zarządzanie obiekty danych, jak włączyć analityka magazynu oraz sposobu dostępu Azure chmur niezależnie od takich jak chmura Chin, niemiecki chmury i chmura dla instytucji rządowych. Poniżej przedstawiono niektóre więcej powiązanych artykułach i zasoby dla odwołania:

* [Azure magazynu kontroli płaszczyzny poleceń cmdlet programu PowerShell](/powershell/module/AzureRM.Storage/)
* [Dane z magazynu Azure płaszczyzny poleceń cmdlet programu PowerShell](/powershell/module/azure.storage/)
* [Dokumentacja programu PowerShell systemu Windows](https://msdn.microsoft.com/library/ms714469.aspx)
