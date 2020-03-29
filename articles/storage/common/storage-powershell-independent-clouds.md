---
title: Zarządzanie danymi w niezależnych chmurach platformy Azure za pomocą programu PowerShell
titleSuffix: Azure Storage
description: Zarządzanie magazynem w chmurze China Cloud, Government Cloud i niemieckiej chmurze przy użyciu programu Azure PowerShell.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 12/04/2019
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: 5fa515515c06466e121a5c0ee925fd4d14245363
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74895233"
---
# <a name="managing-storage-in-the-azure-independent-clouds-using-powershell"></a>Zarządzanie magazynem w niezależnych chmurach platformy Azure przy użyciu programu PowerShell

Większość osób korzysta z usługi Azure Public Cloud do globalnego wdrażania platformy Azure. Istnieje również kilka niezależnych wdrożeń platformy Microsoft Azure ze względu na suwerenność i tak dalej. Te niezależne wdrożenia są określane jako "środowiska". Poniższa lista zawiera szczegółowe informacje o niezależnych chmurach, które są obecnie dostępne.

* [Chmura dla instytucji rządowych platformy Azure](https://azure.microsoft.com/features/gov/)
* [Usługa Azure China 21Vianet Cloud obsługiwana przez firmę 21Vianet w Chinach](http://www.windowsazure.cn/)
* [Niemiecka chmura platformy Azure](../../germany/germany-welcome.md)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="using-an-independent-cloud"></a>Korzystanie z niezależnej chmury

Aby użyć usługi Azure Storage w jednej z niezależnych chmur, należy połączyć się z tą chmurą zamiast usługi Azure Public. Aby użyć jednej z niezależnych chmur, a nie usługi Azure Public:

* Należy określić *środowisko,* z którym ma się połączyć.
* Określasz i używasz dostępnych regionów.
* Używasz poprawnego sufiksu punktu końcowego, który różni się od usługi Azure Public.

Przykłady wymagają modułu Azure PowerShell Az w wersji 0.7 lub nowszej. W oknie programu PowerShell uruchom, `Get-Module -ListAvailable Az` aby znaleźć wersję. Jeśli nic nie jest wymienione lub musisz uaktualnić, zobacz [Instalowanie modułu programu Azure PowerShell](/powershell/azure/install-Az-ps).

## <a name="log-in-to-azure"></a>Zaloguj się do platformy Azure.

Uruchom polecenie cmdlet [Get-AzEnvironment,](/powershell/module/az.accounts/get-azenvironment) aby wyświetlić dostępne środowiska platformy Azure:

```powershell
Get-AzEnvironment
```

Zaloguj się na swoje konto, które ma dostęp do chmury, z którą chcesz się połączyć i ustawić środowisko. W tym przykładzie pokazano, jak zalogować się na konto korzystające z usługi Azure Government Cloud.   

```powershell
Connect-AzAccount –Environment AzureUSGovernment
```

Aby uzyskać dostęp do chmury China Cloud, użyj środowiska **AzureChinaCloud**. Aby uzyskać dostęp do niemieckiej chmury, użyj **usługi AzureGermanCloud**.

W tym momencie, jeśli potrzebujesz listy lokalizacji do utworzenia konta magazynu lub innego zasobu, możesz zbadać lokalizacje dostępne dla wybranej chmury za pomocą [get-AzLocation](/powershell/module/az.resources/get-azlocation).

```powershell
Get-AzLocation | select Location, DisplayName
```

W poniższej tabeli przedstawiono lokalizacje zwrócone dla niemieckiej chmury.

|Lokalizacja | Nazwa wyświetlana |
|----|----|
| `germanycentral` | Niemcy Środkowe|
| `germanynortheast` | Niemcy Północno-Wschodnie |


## <a name="endpoint-suffix"></a>Sufiks punktu końcowego

Sufiks punktu końcowego dla każdego z tych środowisk różni się od publicznego punktu końcowego platformy Azure. Na przykład sufiks punktu końcowego obiektu blob dla usługi Azure Public jest **blob.core.windows.net**. W przypadku chmury rządowej sufiks punktu końcowego obiektu blob jest **blob.core.usgovcloudapi.net**.

### <a name="get-endpoint-using-get-azenvironment"></a>Uzyskaj punkt końcowy przy użyciu get-azenvironment

Pobierz sufiks punktu końcowego przy użyciu [funkcji Get-AzEnvironment](/powershell/module/az.accounts/get-azenvironment). Punktem końcowym jest *StorageEndpointSuffix* właściwość środowiska.

Poniższe fragmenty kodu pokazują, jak pobrać sufiks punktu końcowego. Wszystkie te polecenia zwracają coś w stylu "core.cloudapp.net" lub "core.cloudapi.de", itp. Dołącz sufiks do usługi magazynu, aby uzyskać dostęp do tej usługi. Na przykład "queue.core.cloudapi.de" będzie uzyskiwać dostęp do usługi kolejki w niemieckiej chmurze.

Ten fragment kodu pobiera wszystkie środowiska i sufiks punktu końcowego dla każdego z nich.

```powershell
Get-AzEnvironment | select Name, StorageEndpointSuffix 
```

To polecenie zwraca następujące wyniki.

| Nazwa| MagazynEndpointSuffix|
|----|----|
| AzureChinaCloud | core.chinacloudapi.cn|
| AzureCloud | core.windows.net |
| AzureGermanCloud | core.cloudapi.de|
| AzureUSGovernment | core.usgovcloudapi.net |

Aby pobrać wszystkie właściwości dla określonego środowiska, należy wywołać **Get-AzEnvironment** i określić nazwę chmury. Ten fragment kodu zwraca listę właściwości; **poszukaj storageendpointsuffix** na liście. Poniższy przykład dotyczy niemieckiej chmury.

```powershell
Get-AzEnvironment -Name AzureGermanCloud
```

Wyniki są podobne do następujących wartości:

|Nazwa właściwości|Wartość|
|----|----|
| Nazwa | `AzureGermanCloud` |
| EnableAdfsAuthentication | `False` |
| Usługa ActiveDirectoryServiceEndpointResourceI | `http://management.core.cloudapi.de/` |
| GaleriaURL | `https://gallery.cloudapi.de/` |
| ZarządzaniePortalurl | `https://portal.microsoftazure.de/` |
| ServiceManagementUrl | `https://manage.core.cloudapi.de/` |
| PublishSettingsFileUrl| `https://manage.microsoftazure.de/publishsettings/index` |
| ResourceManagerUrl | `http://management.microsoftazure.de/` |
| SqlDatabaseDnsSuksześć | `.database.cloudapi.de` |
| **MagazynEndpointSuffix** | `core.cloudapi.de` |
| ... | ... |
Aby pobrać tylko właściwość sufiks punktu końcowego magazynu, pobrać określonej chmury i poprosić o tylko tej jednej właściwości.

```powershell
$environment = Get-AzEnvironment -Name AzureGermanCloud
Write-Host "Storage EndPoint Suffix = " $environment.StorageEndpointSuffix
```

To polecenie zwraca następujące informacje:

`Storage Endpoint Suffix = core.cloudapi.de`

### <a name="get-endpoint-from-a-storage-account"></a>Pobierz punkt końcowy z konta magazynu

Można również sprawdzić właściwości konta magazynu, aby pobrać punkty końcowe:

```powershell
# Get a reference to the storage account.
$resourceGroup = "myexistingresourcegroup"
$storageAccountName = "myexistingstorageaccount"
$storageAccount = Get-AzStorageAccount `
  -ResourceGroupName $resourceGroup `
  -Name $storageAccountName 
  # Output the endpoints.
Write-Host "blob endpoint = " $storageAccount.PrimaryEndPoints.Blob 
Write-Host "file endpoint = " $storageAccount.PrimaryEndPoints.File
Write-Host "queue endpoint = " $storageAccount.PrimaryEndPoints.Queue
Write-Host "table endpoint = " $storageAccount.PrimaryEndPoints.Table
```

W przypadku konta magazynu w chmurze dla instytucji rządowych to polecenie zwraca następujące dane wyjściowe:

```
blob endpoint = http://myexistingstorageaccount.blob.core.usgovcloudapi.net/
file endpoint = http://myexistingstorageaccount.file.core.usgovcloudapi.net/
queue endpoint = http://myexistingstorageaccount.queue.core.usgovcloudapi.net/
table endpoint = http://myexistingstorageaccount.table.core.usgovcloudapi.net/
```

## <a name="after-setting-the-environment"></a>Po ustawieniu środowiska

W tym miejscu można użyć tego samego programu PowerShell używanego do zarządzania kontami magazynu i dostępu do płaszczyzny danych, jak opisano w artykule [Korzystanie z programu Azure PowerShell z usługą Azure Storage.](storage-powershell-guide-full.md)

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli utworzono nową grupę zasobów i konto magazynu dla tego ćwiczenia, można usunąć oba zasoby, usuwając grupę zasobów. Usunięcie grupy zasobów spowoduje usunięcie wszystkich zasobów w tej grupie.

```powershell
Remove-AzResourceGroup -Name $resourceGroup
```

## <a name="next-steps"></a>Następne kroki

* [Zachowywanie danych logowania użytkownika między sesjami programu PowerShell](/powershell/azure/context-persistence)
* [Magazyn dla instytucji rządowych platformy Azure](../../azure-government/documentation-government-services-storage.md)
* [Przewodnik dla deweloperów platformy Microsoft Azure dla instytucji rządowych](../../azure-government/documentation-government-developer-guide.md)
* [Uwagi dla deweloperów dotyczące aplikacji Azure China 21Vianet](https://msdn.microsoft.com/library/azure/dn578439.aspx)
* [Dokumentacja usługi Azure Germany](../../germany/germany-welcome.md)
