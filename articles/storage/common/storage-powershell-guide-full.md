---
title: Używanie Azure PowerShell z usługą Azure Storage | Microsoft Docs
description: Dowiedz się, jak używać poleceń cmdlet Azure PowerShell dla usługi Azure Storage.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 08/16/2018
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: d2404ee58f5f44fbe5625f267e6d1c504d0bd237
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75465097"
---
# <a name="using-azure-powershell-with-azure-storage"></a>Używanie programu Azure PowerShell z usługą Azure Storage

Azure PowerShell służy do tworzenia zasobów platformy Azure i zarządzania nimi z poziomu wiersza polecenia programu PowerShell lub skryptów. W przypadku usługi Azure Storage te polecenia cmdlet dzielą się na dwie kategorie — płaszczyzny kontroli i płaszczyzny danych. Polecenia cmdlet płaszczyzny kontroli służą do zarządzania kontem magazynu — aby utworzyć konta magazynu, ustawić właściwości, usunąć konta magazynu, obrócić klucze dostępu i tak dalej. Polecenia cmdlet płaszczyzny danych służą do zarządzania danymi przechowywanymi *na* koncie magazynu. Na przykład przekazywanie obiektów blob, tworzenie udziałów plików i Dodawanie komunikatów do kolejki.

Ten artykuł zawiera opis typowych operacji za pomocą poleceń cmdlet płaszczyzny zarządzania do zarządzania kontami magazynu. Omawiane kwestie:

> [!div class="checklist"]
> * Wyświetlanie listy kont magazynu
> * Pobierz odwołanie do istniejącego konta magazynu
> * Tworzenie konta magazynu
> * Ustawianie właściwości konta magazynu
> * Pobieranie i ponowne generowanie kluczy dostępu
> * Ochrona dostępu do konta magazynu
> * Włącz analityka magazynu

Ten artykuł zawiera linki do kilku innych artykułów programu PowerShell dotyczących magazynu, na przykład sposobu włączania i uzyskiwania dostępu do analityka magazynu, sposobu korzystania z poleceń cmdlet dotyczących płaszczyzny danych oraz uzyskiwania dostępu do chmur niezależnych od platformy Azure, takich jak chmura Chińska, Chmura niemiecka i rząd Chmury.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

To ćwiczenie wymaga modułu Azure PowerShell AZ w wersji 0,7 lub nowszej. Uruchom polecenie `Get-Module -ListAvailable Az`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie modułu Azure PowerShell](/powershell/azure/install-Az-ps).

W tym ćwiczeniu można wpisać polecenia do zwykłego okna programu PowerShell lub użyć [środowiska Windows PowerShell Integrated Scripting Environment (ISE)](/powershell/scripting/components/ise/exploring-the-windows-powershell-ise) , a następnie wpisać polecenia do edytora, a następnie przetestować co najmniej jedno polecenie w czasie, przechodząc do przykładów. Można podświetlić wiersze, które mają zostać wykonane, a następnie kliknąć pozycję Uruchom wybrane, aby po prostu uruchomić te polecenia.

Aby uzyskać więcej informacji na temat kont magazynu, zobacz [wprowadzenie do magazynu](storage-introduction.md) i [konta usługi Azure Storage](storage-create-storage-account.md).

## <a name="sign-in-to-azure"></a>Zaloguj się w usłudze Azure

Zaloguj się do subskrypcji platformy Azure za pomocą polecenia `Connect-AzAccount` i postępuj zgodnie z instrukcjami wyświetlanymi na ekranie.

```powershell
Connect-AzAccount
```

## <a name="list-the-storage-accounts-in-the-subscription"></a>Wyświetlanie listy kont magazynu w ramach subskrypcji

Uruchom polecenie cmdlet [Get-AzStorageAccount](/powershell/module/az.storage/Get-azStorageAccount) , aby pobrać listę kont magazynu w bieżącej subskrypcji.

```powershell
Get-AzStorageAccount | Select StorageAccountName, Location
```

## <a name="get-a-reference-to-a-storage-account"></a>Pobierz odwołanie do konta magazynu

Następnie musisz odwołać się do konta magazynu. Można utworzyć nowe konto magazynu lub odwołać się do istniejącego konta magazynu. W poniższej sekcji przedstawiono obie metody.

### <a name="use-an-existing-storage-account"></a>Użyj istniejącego konta magazynu

Aby można było pobrać istniejące konto magazynu, wymagana jest nazwa grupy zasobów i nazwa konta magazynu. Ustaw zmienne dla tych dwóch pól, a następnie użyj polecenia cmdlet [Get-AzStorageAccount](/powershell/module/az.storage/Get-azStorageAccount) .

```powershell
$resourceGroup = "myexistingresourcegroup"
$storageAccountName = "myexistingstorageaccount"

$storageAccount = Get-AzStorageAccount -ResourceGroupName $resourceGroup `
  -Name $storageAccountName
```

Teraz masz $storageAccount, które wskazuje na istniejące konto magazynu.

### <a name="create-a-storage-account"></a>Tworzenie konta magazynu

Poniższy skrypt pokazuje, jak utworzyć konto magazynu ogólnego przeznaczenia przy użyciu polecenia [New-AzStorageAccount](/powershell/module/az.storage/New-azStorageAccount). Po utworzeniu konta Pobierz jego kontekst, który może być używany w kolejnych poleceniach zamiast określania uwierzytelniania przy każdym wywołaniu.

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

*   [Get-AzLocation](/powershell/module/az.resources/get-azlocation) --pobiera listę prawidłowych lokalizacji. W przykładzie używa się `eastus` do lokalizacji.

*   [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) --tworzy nową grupę zasobów. Grupa zasobów to logiczny kontener, w którym są wdrażane i zarządzane zasoby platformy Azure. Nasza jest nazywana `teststoragerg`.

*   [New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount) --tworzy konto magazynu. W przykładzie zastosowano `testpshstorage`.

Nazwa jednostki SKU wskazuje typ replikacji konta magazynu, na przykład LRS (Magazyn lokalnie nadmiarowy). Aby uzyskać więcej informacji na temat replikacji, zobacz [Replikacja usługi Azure Storage](storage-redundancy.md).

> [!IMPORTANT]
> Nazwa konta magazynu musi być unikatowa w ramach platformy Azure i musi być małymi literami. W przypadku konwencji nazewnictwa i ograniczeń można znaleźć w temacie [nazywanie i odwoływanie się do kontenerów, obiektów blob i metadanych](/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata).
>

Teraz masz nowe konto magazynu i odwołanie do niego.

## <a name="manage-the-storage-account"></a>Zarządzanie kontem magazynu

Teraz, gdy masz odwołanie do nowego konta magazynu lub istniejącego konta magazynu, w poniższej sekcji przedstawiono niektóre z poleceń, których można użyć do zarządzania kontem magazynu.

### <a name="storage-account-properties"></a>Właściwości konta magazynu

Aby zmienić ustawienia dla konta magazynu, użyj polecenie [Set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount). Chociaż nie można zmienić lokalizacji konta magazynu lub grupy zasobów, w której znajduje się, można zmienić wiele innych właściwości. Poniżej wymieniono niektóre właściwości, które można zmienić przy użyciu programu PowerShell.

* **Domena niestandardowa** przypisana do konta magazynu.

* **Tagi** przypisane do konta magazynu. Tagi są często używane do klasyfikowania zasobów na potrzeby rozliczeń.

* **Jednostka SKU** to ustawienie replikacji dla konta magazynu, takie jak LRS dla magazynu lokalnie nadmiarowego. Na przykład można zmienić z standardowego\_LRS na Standard\_GRS lub standard\_RAGRS. Należy pamiętać, że nie można zmienić standardowego\_ZRS, standardowego\_GZRS, standard\_RAGZRS lub Premium\_LRS na inne jednostki SKU ani zmieniać innych jednostek SKU.

* **Warstwa dostępu** dla kont usługi BLOB Storage. Wartość w polu Warstwa dostępu jest ustawiana na **gorąca** lub **chłodna**i pozwala zminimalizować koszt, wybierając warstwę dostępu, która jest wyrównana do sposobu korzystania z konta magazynu. Aby uzyskać więcej informacji, zobacz [warstwy magazynowania gorąca, chłodna i archiwalna](../blobs/storage-blob-storage-tiers.md).

* Zezwalaj tylko na ruch HTTPS.

### <a name="manage-the-access-keys"></a>Zarządzanie kluczami dostępu

Konto usługi Azure Storage zawiera dwa klucze konta. Aby pobrać klucze, użyj polecenie [Get-AzStorageAccountKey](/powershell/module/az.Storage/Get-azStorageAccountKey). Ten przykład pobiera pierwszy klucz. Aby pobrać inne, użyj `Value[1]` zamiast `Value[0]`.

```powershell
$storageAccountKey = `
    (Get-AzStorageAccountKey `
    -ResourceGroupName $resourceGroup `
    -Name $storageAccountName).Value[0]
```

Aby ponownie wygenerować klucz, użyj polecenie [New-AzStorageAccountKey](/powershell/module/az.Storage/New-azStorageAccountKey).

```powershell
New-AzStorageAccountKey -ResourceGroupName $resourceGroup `
  -Name $storageAccountName `
  -KeyName key1
```

Aby ponownie wygenerować drugi klucz, użyj `key2` jako nazwy klucza, a nie `key1`.

Wygeneruj ponownie jeden z kluczy, a następnie Odzyskaj go w celu wyświetlenia nowej wartości.

> [!NOTE]
> Należy zachować ostrożność planowania przed ponownym generowaniem klucza dla produkcyjnego konta magazynu. Ponowne generowanie jednego lub obu kluczy spowoduje unieważnienie dostępu dla każdej aplikacji przy użyciu klucza, który został ponownie wygenerowany. Aby uzyskać więcej informacji, zobacz [Zarządzanie kluczami dostępu do konta magazynu](storage-account-keys-manage.md).


### <a name="delete-a-storage-account"></a>Usuwanie konta magazynu

Aby usunąć konto magazynu, użyj polecenie [Remove-AzStorageAccount](/powershell/module/az.storage/Remove-azStorageAccount).

```powershell
Remove-AzStorageAccount -ResourceGroup $resourceGroup -AccountName $storageAccountName
```

> [!IMPORTANT]
> Po usunięciu konta magazynu zostaną również usunięte wszystkie zasoby przechowywane w ramach konta. W przypadku przypadkowego usunięcia konta skontaktuj się z pomocą techniczną natychmiast i Otwórz bilet, aby przywrócić konto magazynu. Odzyskiwanie danych nie jest gwarantowane, ale czasami działa. Nie należy tworzyć nowego konta magazynu o takiej samej nazwie jak stary, dopóki bilet pomocy technicznej nie zostanie rozwiązany.
>

### <a name="protect-your-storage-account-using-vnets-and-firewalls"></a>Ochrona konta magazynu za pomocą sieci wirtualnych i zapór

Domyślnie wszystkie konta magazynu są dostępne dla dowolnej sieci, która ma dostęp do Internetu. Jednak reguły sieci można skonfigurować tak, aby zezwalać na dostęp do konta magazynu tylko aplikacjom z określonych sieci wirtualnych. Aby uzyskać więcej informacji, zobacz [Konfigurowanie zapór usługi Azure Storage i sieci wirtualnych](storage-network-security.md).

W tym artykule pokazano, jak zarządzać tymi ustawieniami przy użyciu następujących poleceń cmdlet programu PowerShell:
* [Add-AzStorageAccountNetworkRule](/powershell/module/az.Storage/Add-azStorageAccountNetworkRule)
* [Update-AzStorageAccountNetworkRuleSet](/powershell/module/az.storage/update-azstorageaccountnetworkruleset)
* [Remove-AzStorageAccountNetworkRule](https://docs.microsoft.com/powershell/module/az.storage/remove-azstorageaccountnetworkrule)

## <a name="use-storage-analytics"></a>Korzystanie z analizy magazynu

[Analityka magazynu platformy Azure](storage-analytics.md) składa się z [metryk analityka magazynu](/rest/api/storageservices/about-storage-analytics-metrics) i [rejestrowania analityka magazynu](/rest/api/storageservices/about-storage-analytics-logging).

**Metryki analityka magazynu** są używane do zbierania metryk dla kont usługi Azure Storage, których można użyć do monitorowania kondycji konta magazynu. Metryki można włączyć dla obiektów blob, plików, tabel i kolejek.

**Analityka magazynu rejestrowanie** odbywa się po stronie serwera i umożliwia rejestrowanie szczegółowych informacji dla żądań zakończonych powodzeniem i zakończonych niepowodzeniem na koncie magazynu. Te dzienniki umożliwiają wyświetlanie szczegółów operacji odczytu, zapisu i usuwania dla tabel, kolejek i obiektów blob, a także powodów nieudanych żądań. Rejestrowanie nie jest dostępne dla Azure Files.

Monitorowanie można skonfigurować przy użyciu [Azure Portal](https://portal.azure.com), programu PowerShell lub programowo przy użyciu biblioteki klienta usługi Storage.

> [!NOTE]
> Możesz włączyć analizę minutową przy użyciu programu PowerShell. Ta funkcja nie jest dostępna w portalu.
>

* Aby dowiedzieć się, jak włączyć i wyświetlić dane metryk magazynu za pomocą programu PowerShell, zobacz [metryki analizy magazynu](storage-analytics-metrics.md).

* Aby dowiedzieć się, jak włączyć i pobrać dane rejestrowania magazynu za pomocą programu PowerShell, zobacz [jak włączyć rejestrowanie magazynu przy użyciu programu PowerShell](/rest/api/storageservices/Enabling-Storage-Logging-and-Accessing-Log-Data) i [znaleźć dane dziennika rejestrowania magazynu](/rest/api/storageservices/Enabling-Storage-Logging-and-Accessing-Log-Data).

* Aby uzyskać szczegółowe informacje na temat używania metryk magazynu i rejestrowania magazynu w celu rozwiązywania problemów z magazynem, zobacz [monitorowanie, diagnozowanie i rozwiązywanie problemów Microsoft Azure Storage](storage-monitoring-diagnosing-troubleshooting.md).

## <a name="manage-the-data-in-the-storage-account"></a>Zarządzanie danymi na koncie magazynu

Teraz, gdy rozumiesz, jak zarządzać kontem magazynu za pomocą programu PowerShell, możesz skorzystać z poniższych artykułów, aby dowiedzieć się, jak uzyskać dostęp do obiektów danych na koncie magazynu.

* [Jak zarządzać obiektami BLOB za pomocą programu PowerShell](../blobs/storage-how-to-use-blobs-powershell.md)
* [Jak zarządzać plikami za pomocą programu PowerShell](../files/storage-how-to-use-files-powershell.md)
* [Jak zarządzać kolejkami przy użyciu programu PowerShell](../queues/storage-powershell-how-to-use-queues.md)
* [Wykonywanie operacji usługi Azure Table Storage za pomocą programu PowerShell](../../storage/tables/table-storage-how-to-use-powershell.md)

Azure Cosmos DB interfejs API tabel udostępnia funkcje Premium dla magazynu tabel, takie jak gotowe Global Distribution, odczyty i zapisy o małym opóźnieniu, Automatyczne indeksowanie pomocnicze i dedykowana przepływność.

* Aby uzyskać więcej informacji, zobacz [Azure Cosmos DB interfejs API tabel](../../cosmos-db/table-introduction.md).

## <a name="independent-cloud-deployments-of-azure"></a>Niezależne wdrożenia w chmurze platformy Azure

Większość osób korzysta z chmury publicznej platformy Azure na potrzeby globalnego wdrożenia platformy Azure. Istnieją także niezależne wdrożenia Microsoft Azure z przyczyn suwerenności i tak dalej. Te niezależne wdrożenia są określane jako "środowiska". Dostępne są następujące środowiska:

* [Azure Government Cloud](https://azure.microsoft.com/features/gov/)
* [Azure — Chiny, Chmura firmy 21Vianet obsługiwana przez firmę 21Vianet w Chinach](http://www.windowsazure.cn/)
* [Chmura w języku niemieckim platformy Azure](../../germany/germany-welcome.md)

Informacje o sposobach uzyskiwania dostępu do tych chmur i ich magazynu za pomocą programu PowerShell można znaleźć [w temacie Zarządzanie magazynem w chmurach niezależnych platformy Azure przy użyciu programu PowerShell](storage-powershell-independent-clouds.md).

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli utworzono nową grupę zasobów i konto magazynu dla tego ćwiczenia, yous może usunąć wszystkie utworzone zasoby, usuwając grupę zasobów. Spowoduje to również usunięcie wszystkich zasobów znajdujących się w grupie. W tym przypadku usuwa utworzone konto magazynu i grupę zasobów.

```powershell
Remove-AzResourceGroup -Name $resourceGroup
```
## <a name="next-steps"></a>Następne kroki

Ten artykuł zawiera opis typowych operacji za pomocą poleceń cmdlet płaszczyzny zarządzania do zarządzania kontami magazynu. W tym samouczku omówiono:

> [!div class="checklist"]
> * Wyświetlanie listy kont magazynu
> * Pobierz odwołanie do istniejącego konta magazynu
> * Tworzenie konta magazynu
> * Ustawianie właściwości konta magazynu
> * Pobieranie i ponowne generowanie kluczy dostępu
> * Ochrona dostępu do konta magazynu
> * Włącz analityka magazynu

W tym artykule przedstawiono również odwołania do kilku innych artykułów, takich jak zarządzanie obiektami danych, jak włączyć analityka magazynu i jak uzyskać dostęp do chmur niezależnych platformy Azure, takich jak chmura Chińska, Chmura niemiecka i chmura rządowa. Poniżej przedstawiono kilka pokrewnych artykułów i zasobów dotyczących odwołań:

* [Polecenia cmdlet programu PowerShell płaszczyzny kontroli usługi Azure Storage](/powershell/module/az.storage/)
* [Polecenia cmdlet programu PowerShell dotyczące płaszczyzny danych usługi Azure Storage](/powershell/module/azure.storage/)
* [Dokumentacja programu Windows PowerShell](/powershell/scripting/overview)
