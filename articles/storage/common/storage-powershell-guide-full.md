---
title: Używanie programu Azure PowerShell z usługą Azure Storage | Dokumentacja firmy Microsoft
description: Dowiedz się, jak używać poleceń cmdlet programu Azure PowerShell dla usługi Azure Storage.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 08/16/2018
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: 082033cebc68fc97f7cff9ce80eb02acbbf5f4b0
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/06/2019
ms.locfileid: "65145882"
---
# <a name="using-azure-powershell-with-azure-storage"></a>Używanie programu Azure PowerShell z usługą Azure Storage

Program Azure PowerShell służy do tworzenia i zarządzania zasobami platformy Azure, w wierszu polecenia programu PowerShell lub skryptów. Dla usługi Azure Storage tych poleceń cmdlet można podzielić na dwie kategorie — na płaszczyźnie kontroli i płaszczyzny danych. Polecenia cmdlet płaszczyzny kontroli umożliwiają zarządzanie kontem magazynu — Tworzenie konta magazynu, ustaw właściwości, usuwania kont magazynu, rotowanie kluczy dostępu do i tak dalej. Polecenia cmdlet płaszczyzny danych umożliwiają zarządzanie danymi zapisanymi *w* konta magazynu. Na przykład przekazywania obiektów blob, tworzenia udziałów plików i dodawanie wiadomości do kolejki.

W tym artykule omówiono typowe operacje przy użyciu polecenia cmdlet płaszczyzny zarządzania, aby zarządzać kontami magazynu. Omawiane kwestie:

> [!div class="checklist"]
> * Wyświetlanie listy kont magazynu
> * Pobierz odwołanie do istniejącego konta magazynu
> * Tworzenie konta magazynu
> * Właściwości konta magazynu
> * Pobieranie lub ponownie wygenerować klucze dostępu
> * Ochrona dostępu do konta magazynu
> * Włącz analityka magazynu

W tym artykule linki do kilka artykułów programu PowerShell dla magazynu, takich jak sposobu włączania i dostęp do usługi Storage Analytics, jak za pomocą poleceń cmdlet płaszczyzny danych i jak uzyskać dostęp w chmurach niezależnie od platformy Azure albo chmura, chmura niemiecka i dla instytucji rządowych W chmurze.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

To ćwiczenie wymaga modułu Azure PowerShell Az wersji 0,7 lub nowszej. Uruchom polecenie `Get-Module -ListAvailable Az`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie modułu Azure PowerShell](/powershell/azure/install-Az-ps).

Na potrzeby tego ćwiczenia można wpisywać polecenia do regularnego okna programu PowerShell, możesz też [Windows PowerShell zintegrowane Scripting Environment (ISE)](/powershell/scripting/getting-started/fundamental/windows-powershell-integrated-scripting-environment--ise-) wpisz poleceń w edytorze, a następnie przetestować co najmniej jedno polecenie w czasie, co Możesz przejść za pomocą przykładów. Można wyróżnić wiersze, które chcesz wykonać i kliknij przycisk Uruchom wybrane tylko uruchamiania tych poleceń.

Aby uzyskać więcej informacji na temat kont magazynu, zobacz [wprowadzenie do usługi Storage](storage-introduction.md) i [kontach magazynu Azure o](storage-create-storage-account.md).

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Zaloguj się do subskrypcji platformy Azure za pomocą polecenia `Connect-AzAccount` i postępuj zgodnie z instrukcjami wyświetlanymi na ekranie.

```powershell
Connect-AzAccount
```

## <a name="list-the-storage-accounts-in-the-subscription"></a>Wyświetlanie listy kont magazynu w ramach subskrypcji

Uruchom [Get AzStorageAccount](/powershell/module/az.storage/Get-azStorageAccount) polecenie cmdlet do pobierania listy kont magazynu w bieżącej subskrypcji.

```powershell
Get-AzStorageAccount | Select StorageAccountName, Location
```

## <a name="get-a-reference-to-a-storage-account"></a>Pobierz odwołanie do konta magazynu

Następnie należy odwołanie do konta magazynu. Możesz utworzyć nowe konto magazynu lub odwołać się do istniejącego konta magazynu. W tej części opisano obie metody.

### <a name="use-an-existing-storage-account"></a>Użyj istniejącego konta magazynu

Aby pobrać istniejące konto magazynu, należy nazwę grupy zasobów i nazwę konta magazynu. Ustaw zmienne dla tych dwóch pól, a następnie użyj [Get AzStorageAccount](/powershell/module/az.storage/Get-azStorageAccount) polecenia cmdlet.

```powershell
$resourceGroup = "myexistingresourcegroup"
$storageAccountName = "myexistingstorageaccount"

$storageAccount = Get-AzStorageAccount -ResourceGroupName $resourceGroup `
  -Name $storageAccountName
```

Masz teraz $storageAccount, która wskazuje na istniejące konto magazynu.

### <a name="create-a-storage-account"></a>Tworzenie konta magazynu

Poniższy skrypt pokazuje, jak utworzyć konto magazynu ogólnego przeznaczenia przy użyciu [New AzStorageAccount](/powershell/module/az.storage/New-azStorageAccount). Po utworzeniu konta, pobrać jego kontekst, który może służyć w kolejnych poleceniach zamiast określania uwierzytelniania za pomocą każdego wywołania.

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

*   [Get-AzLocation](/powershell/module/az.resources/get-azlocation) — pobiera listę prawidłowych lokalizacji. W przykładzie użyto `eastus` dla lokalizacji.

*   [Nowe AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) — tworzy nową grupę zasobów. Grupa zasobów to logiczny kontener, w której zasoby platformy Azure są wdrażania i zarządzania nimi. Nasza nosi nazwę `teststoragerg`.

*   [Nowe AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount) — tworzy konto magazynu. W przykładzie użyto `testpshstorage`.

Nazwa jednostki SKU wskazuje typ replikacji dla konta magazynu, takich jak magazyn LRS (magazyn lokalnie nadmiarowy). Aby uzyskać więcej informacji na temat replikacji, zobacz [replikacja usługi Azure Storage](storage-redundancy.md).

> [!IMPORTANT]
> Nazwa konta magazynu musi być unikatowa w obrębie platformy Azure i muszą być małymi literami. Konwencje nazewnictwa i ograniczenia, zobacz [nazewnictwo i odwoływanie się do kontenerów, obiektów blob i metadanych](/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata).
>

Masz teraz nowe konto magazynu i odwołanie do niej.

## <a name="manage-the-storage-account"></a>Zarządzanie kontem magazynu

Teraz, gdy odwołanie do nowego konta magazynu lub istniejące konto magazynu, w poniższej sekcji pokazano niektóre polecenia, które umożliwia zarządzanie kontem magazynu.

### <a name="storage-account-properties"></a>Właściwości konta magazynu

Aby zmienić ustawienia konta magazynu, użyj [AzStorageAccount zestaw](/powershell/module/az.storage/set-azstorageaccount). Chociaż nie można zmienić lokalizacji konta magazynu lub grupy zasobów, w którym się znajduje, możesz zmienić wiele innych właściwości. Poniższa lista zawiera niektóre właściwości, które można zmienić za pomocą programu PowerShell.

* **Domeny niestandardowej** przypisane do konta magazynu.

* **Tagi** przypisane do konta magazynu. Tagi są często używane do kategoryzowania zasobów na potrzeby rozliczeń.

* **Jednostki SKU** jest ustawienie replikacji dla konta magazynu, takich jak magazyn LRS, aby uzyskać magazyn lokalnie nadmiarowy. Na przykład można zmienić warstwy standardowej\_LRS na warstwę standardowa\_GRS lub Standard\_RAGRS. Należy pamiętać, że nie można zmienić Standard\_ZRS lub Premium\_LRS na inne jednostki SKU lub zmienić te inne jednostki SKU.

* **Warstwy dostępu** dla kont usługi Blob storage. Wartość w przypadku warstwy dostępu jest równa **gorąca** lub **chłodna**, i umożliwia zminimalizowanie kosztów, wybierając warstwę dostępu, która wyrównuje z jak używać konta magazynu. Aby uzyskać więcej informacji, zobacz [gorąca, chłodna i archiwalna magazynu](../blobs/storage-blob-storage-tiers.md).

* Zezwalaj tylko na ruch HTTPS.

### <a name="manage-the-access-keys"></a>Zarządzanie kluczami dostępu do

Konto usługi Azure Storage jest dostarczany z dwóch kluczy konta. Aby pobrać klucze, użyj [Get AzStorageAccountKey](/powershell/module/az.Storage/Get-azStorageAccountKey). W tym przykładzie pobiera pierwszy klucz. Aby pobrać, druga, użyj `Value[1]` zamiast `Value[0]`.

```powershell
$storageAccountKey = `
    (Get-AzStorageAccountKey `
    -ResourceGroupName $resourceGroup `
    -Name $storageAccountName).Value[0]
```

Aby ponownie wygenerować klucz, należy użyć [New AzStorageAccountKey](/powershell/module/az.Storage/New-azStorageAccountKey).

```powershell
New-AzStorageAccountKey -ResourceGroupName $resourceGroup `
  -Name $storageAccountName `
  -KeyName key1
```

Aby ponownie wygenerować klucz innych, należy użyć `key2` jako nazwę klucza, zamiast `key1`.

Ponowne generowanie jednego z kluczy, a następnie pobrać ją ponownie, aby zobaczyć nową wartość.

> [!NOTE]
> Należy to wykonać starannego planowania przed Trwa ponowne generowanie klucza konta magazynu w środowisku produkcyjnym. Ponowne generowanie kluczy jeden lub oba spowoduje unieważnienie dostępu dla każdej aplikacji za pomocą klucza, który został ponownie wygenerowany. Aby uzyskać więcej informacji, zobacz temat [Klucze dostępu](storage-account-manage.md#access-keys).


### <a name="delete-a-storage-account"></a>Usuwanie konta magazynu

Aby usunąć konto magazynu, użyj [AzStorageAccount Usuń](/powershell/module/az.storage/Remove-azStorageAccount).

```powershell
Remove-AzStorageAccount -ResourceGroup $resourceGroup -AccountName $storageAccountName
```

> [!IMPORTANT]
> Podczas usuwania konta magazynu, wszystkie zasoby przechowywanych na koncie są również usunięte. Jeśli przypadkowo usuniesz konto, z działem pomocy technicznej natychmiast, a następnie otwórz bilet, można przywrócić konta magazynu. Odzyskiwanie danych nie ma żadnej gwarancji, ale czasami działa. Nie należy tworzyć nowe konto magazynu z taką samą nazwę jak stary, dopóki bilet pomocy technicznej zostanie rozwiązany.
>

### <a name="protect-your-storage-account-using-vnets-and-firewalls"></a>Chroń swoje konto magazynu przy użyciu sieci wirtualnych i zapór

Domyślnie wszystkie konta magazynu są dostępne dla każdej sieci, który ma dostęp do Internetu. Można jednak skonfigurować reguły sieci, aby zezwalać tylko na aplikacje z określonej sieci wirtualnych na dostęp do konta magazynu. Aby uzyskać więcej informacji, zobacz [Konfigurowanie usługi Azure Storage zapory i sieci wirtualne](storage-network-security.md).

Artykuł pokazuje, jak zarządzać tymi ustawieniami za pomocą następujących poleceń cmdlet programu PowerShell:
* [Add-AzStorageAccountNetworkRule](/powershell/module/az.Storage/Add-azStorageAccountNetworkRule)
* [Update-AzStorageAccountNetworkRuleSet](/powershell/module/az.storage/update-azstorageaccountnetworkruleset)
* [Remove-AzStorageAccountNetworkRule](https://docs.microsoft.com/powershell/module/az.storage/remove-azstorageaccountnetworkrule)

## <a name="use-storage-analytics"></a>Korzystanie z analizy magazynu  

[Analizy usługi Azure Storage](storage-analytics.md) składa się z [Storage Analytics Metrics](/rest/api/storageservices/about-storage-analytics-metrics) i [Storage Analytics rejestrowania](/rest/api/storageservices/about-storage-analytics-logging).

**Storage Analytics Metrics** służy do zbierania metryk dla kont usługi Azure storage, które służy do monitorowania kondycji na koncie magazynu. Metryki można włączyć dla obiektów blob, pliki, tabele i kolejki.

**Storage Analytics rejestrowania** się dzieje po stronie serwera i umożliwia zarejestrowanie szczegółów dotyczących zarówno udane, jak i nieudane żądania do swojego konta magazynu. Te dzienniki przedstawiają szczegółowe informacje o odczytu, zapisu i usuwania operacji dotyczących tabel, kolejek i obiektów blob, a także przyczyny nieudanych żądań. Rejestrowanie nie jest dostępna dla usługi Azure Files.

Możesz skonfigurować monitorowanie za pomocą [witryny Azure portal](https://portal.azure.com), programu PowerShell lub programowo przy użyciu biblioteki klienta usługi storage.

> [!NOTE]
> Można włączyć minuty analytics przy użyciu programu PowerShell. Ta funkcja nie jest dostępne w portalu.
>

* Aby dowiedzieć się, jak włączanie i wyświetlanie danych metryk usługi Storage przy użyciu programu PowerShell, zobacz [Storage analytics metrics](storage-analytics-metrics.md).

* Aby dowiedzieć się, jak włączyć i pobrać rejestrowania magazynu danych przy użyciu programu PowerShell, zobacz [jak włączyć rejestrowanie magazynu, przy użyciu programu PowerShell](/rest/api/storageservices/Enabling-Storage-Logging-and-Accessing-Log-Data) i [znajdowanie rejestrowania magazynu danych dziennika](/rest/api/storageservices/Enabling-Storage-Logging-and-Accessing-Log-Data).

* Aby uzyskać szczegółowe informacje na temat używania metryki magazynu i rejestrowania magazynu rozwiązywać problemy związane z magazynowaniem, zobacz [monitorowanie, diagnozowanie i rozwiązywanie problemów z usługi Microsoft Azure Storage](storage-monitoring-diagnosing-troubleshooting.md).

## <a name="manage-the-data-in-the-storage-account"></a>Zarządzanie danymi w ramach konta magazynu

Gdy już wiesz jak zarządzać kontem magazynu za pomocą programu PowerShell służy następujące artykuły na temat sposobu dostępu do obiektów danych na koncie magazynu.

* [Jak zarządzać obiektami blob za pomocą programu PowerShell](../blobs/storage-how-to-use-blobs-powershell.md)
* [Jak zarządzać plikami za pomocą programu PowerShell](../files/storage-how-to-use-files-powershell.md)
* [Jak zarządzać kolejki przy użyciu programu PowerShell](../queues/storage-powershell-how-to-use-queues.md)
* [Wykonywanie operacji magazynu tabel Azure przy użyciu programu PowerShell](../../storage/tables/table-storage-how-to-use-powershell.md)

Interfejs API usługi Azure Cosmos DB Table zapewnia funkcje premium usługi table storage, takie jak gotowa do użycia funkcja dystrybucji globalnej, odczytów z małymi opóźnieniami i zapisy, automatyczne indeksowanie pomocnicze i dedykowanej przepływności.

* Aby uzyskać więcej informacji, zobacz [interfejsu API tabeli usługi Azure Cosmos DB](../../cosmos-db/table-introduction.md).

## <a name="independent-cloud-deployments-of-azure"></a>Wdrożenia chmury niezależnie od platformy Azure

Większość osób na użytek chmurze publicznej platformy Azure ich globalnego wdrażania platformy Azure. Istnieją również pewne niezależne wdrożenia systemu Microsoft Azure, aby niezależności i tak dalej. Niezależne wdrożenia są określane jako "środowisk". Poniżej przedstawiono dostępnych środowisk:

* [Chmura platformy Azure dla instytucji rządowych](https://azure.microsoft.com/features/gov/)
* [Platformy Azure China 21Vianet w chmurze obsługiwana przez firmę 21Vianet w Chinach](http://www.windowsazure.cn/)
* [Usługa Azure niemieckiej wersji chmury](../../germany/germany-welcome.md)

Aby uzyskać informacje o tym, jak uzyskać dostęp do tych chmur i ich magazynu przy użyciu programu PowerShell, zobacz [Managing Storage w niezależnych chmury platformy Azure przy użyciu programu PowerShell](storage-powershell-independent-clouds.md).

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli utworzono nową grupę zasobów i konto magazynu na potrzeby tego ćwiczenia yous można usunąć wszystkie zasoby utworzone przez usunięcie grupy zasobów. Spowoduje to również usunięcie wszystkich zasobów znajdujących się w grupie. W tym przypadku powoduje usunięcie konta magazynu i samej grupy zasobów.

```powershell
Remove-AzResourceGroup -Name $resourceGroup
```
## <a name="next-steps"></a>Kolejne kroki

W tym artykule omówiono typowe operacje przy użyciu polecenia cmdlet płaszczyzny zarządzania, aby zarządzać kontami magazynu. W tym samouczku omówiono:

> [!div class="checklist"]
> * Wyświetlanie listy kont magazynu
> * Pobierz odwołanie do istniejącego konta magazynu
> * Tworzenie konta magazynu
> * Właściwości konta magazynu
> * Pobieranie lub ponownie wygenerować klucze dostępu
> * Ochrona dostępu do konta magazynu
> * Włącz analityka magazynu

W tym artykule również podane odwołania do kilku innych artykułów, takich jak sposób zarządzania obiektów danych, jak włączyć Storage Analytics i sposób dostępu chmury niezależnie od platformy Azure albo chmura, chmura niemiecka i chmury dla instytucji rządowych. Poniżej przedstawiono niektóre więcej powiązanych artykuły i zasoby dla odwołania:

* [Usługa Azure Storage kontroli płaszczyzny poleceń cmdlet programu PowerShell](/powershell/module/az.storage/)
* [Polecenia cmdlet programu PowerShell na płaszczyźnie danych usługi Azure Storage](/powershell/module/azure.storage/)
* [Dokumentacja programu Windows PowerShell](https://msdn.microsoft.com/library/ms714469.aspx)
