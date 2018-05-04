---
title: Zarządzanie magazynem w niezależnych Azure chmur przy użyciu programu Azure PowerShell | Dokumentacja firmy Microsoft
description: Zarządzanie magazynem w chmurze Chin, chmury dla instytucji rządowych i niemieckim chmurze przy użyciu programu Azure PowerShell
services: storage
documentationcenter: na
author: roygara
manager: jeconnoc
ms.assetid: ''
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/24/2017
ms.author: rogarana
ms.openlocfilehash: 3eecf8918e6628071e44cc588b1826df68a00f10
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2018
---
# <a name="managing-storage-in-the-azure-independent-clouds-using-powershell"></a>Zarządzanie magazynem w Azure niezależne chmur przy użyciu programu PowerShell

Większość użytkowników użyj chmury publicznej Azure globalne wdrożenia usługi Azure. Istnieją również niektórych niezależnie od wdrożenia programu Microsoft Azure ze względu na suwerenności i tak dalej. Te niezależne wdrożenia są określane jako "środowisk". Poniższa lista zawiera szczegóły dotyczące chmury niezależnie od aktualnie dostępna.

* [Chmury Azure dla instytucji rządowych](https://azure.microsoft.com/features/gov/)
* [Chmury Azure Chin przez 21Vianet w Chinach](http://www.windowsazure.cn/)
* [Niemiecki chmury Azure](../../germany/germany-welcome.md)

## <a name="using-an-independent-cloud"></a>Przy użyciu niezależne chmury 

Do użycia usługi Azure Storage w jednym z niezależnych chmur, połączenia z chmurą zamiast publicznej Azure. Aby użyć jednego z chmury niezależnych zamiast publicznej Azure:

* Należy określić *środowiska* do połączenia.
* Należy określić i użyć dostępnych regionów.
* Możesz użyć sufiksu właściwego punktu końcowego, który jest inny niż publicznej Azure.

Przykłady wymagają programu Azure PowerShell w wersji modułu 4.4.0 lub nowszym. W oknie programu PowerShell, uruchom `Get-Module -ListAvailable AzureRM` można znaleźć wersji. Jeśli nic nie jest wyświetlane, lub należy uaktualniania, zobacz [modułu instalacji programu Azure PowerShell](/powershell/azure/install-azurerm-ps). 

## <a name="log-in-to-azure"></a>Zaloguj się do platformy Azure.

Uruchom [Get-AzureEnvironment](/powershell/module/azure/Get-AzureRmEnvironment) polecenia cmdlet, aby wyświetlić dostępne środowisk Azure:
   
```powershell
Get-AzureRmEnvironment
```

Zaloguj się do swojego konta, które ma dostęp do chmury, do którego chcesz połączyć i ustaw środowiska. W tym przykładzie przedstawiono sposób logowania się do konta, które korzysta z chmury Azure dla instytucji rządowych.   

```powershell
Connect-AzureRmAccount –Environment AzureUSGovernment
```

Aby uzyskać dostęp do chmury w Chinach, należy użyć środowiska **AzureChinaCloud**. Aby uzyskać dostęp do chmury niemiecki, należy użyć **AzureGermanCloud**.

W tym momencie listy lokalizacje, aby utworzyć konto magazynu lub innego zasobu, należy można zbadać w lokalizacji dostępnej dla wybranej chmury za pomocą [Get-AzureRmLocation](/powershell/module/azurerm.resources/get-azurermlocation).

```powershell
Get-AzureRmLocation | select Location, DisplayName
```

W poniższej tabeli przedstawiono lokalizacje zwrócił niemieckim chmury.

|Lokalizacja | Nazwa wyświetlana |
|----|----|
| germanycentral | Niemcy Środkowe|
| germanynortheast | Niemcy Północno-Wschodnie | 


## <a name="endpoint-suffix"></a>Sufiks punktu końcowego

Sufiks punktu końcowego dla każdego z tych środowisk różni się od Azure publiczny punkt końcowy. Na przykład sufiks punktu końcowego obiektu blob Azure publiczny jest **blob.core.windows.net**. Chmury dla instytucji rządowych sufiks punktu końcowego obiektu blob jest **blob.core.usgovcloudapi.net**. 

### <a name="get-endpoint-using-get-azurermenvironment"></a>Pobierz punktu końcowego za pomocą Get AzureRMEnvironment 

Pobrać za pomocą sufiksu punktu końcowego [Get-AzureRMEnvironment](/powershell/module/azurerm.profile/get-azurermenvironment). Punkt końcowy jest *StorageEndpointSuffix* właściwości środowiska. Poniższe fragmenty kodu pokazano, jak to zrobić. Zwróć wszystkie te polecenia coś "core.cloudapp.net" lub "core.cloudapi.de" itd. Dołącz do usługi magazynu do dostępu do tej usługi. Na przykład "queue.core.cloudapi.de" będą uzyskiwać dostęp do kolejki usługi w chmurze niemiecki.

Następujący fragment kodu pobiera wszystkie środowiska i sufiksu punktu końcowego dla każdej z nich.

```powershell
Get-AzureRmEnvironment | select Name, StorageEndpointSuffix 
```

To polecenie zwraca następujące wyniki.

| Name (Nazwa)| StorageEndpointSuffix|
|----|----|
| AzureChinaCloud | core.chinacloudapi.cn|
| AzureCloud | core.windows.net |
| AzureGermanCloud | core.cloudapi.de|
| AzureUSGovernment | Core.usgovcloudapi.NET |

Aby pobrać wszystkie właściwości dla określonego środowiska, należy wywołać **Get-AzureRmEnvironment** i podaj nazwę chmury. Następujący fragment kodu zwraca listę właściwości; Wyszukaj **StorageEndpointSuffix** na liście. Poniższy przykład jest niemiecki chmury.

```powershell
Get-AzureRmEnvironment -Name AzureGermanCloud 
```

Wyniki są podobne do następujących:

|Nazwa właściwości|Wartość|
|----|----|
| Name (Nazwa) | AzureGermanCloud |
| EnableAdfsAuthentication | False |
| ActiveDirectoryServiceEndpointResourceI | http://management.core.cloudapi.de/ |
| GalleryURL | https://gallery.cloudapi.de/ |
| ManagementPortalUrl | https://portal.microsoftazure.de/ | 
| ServiceManagementUrl | https://manage.core.cloudapi.de/ |
| PublishSettingsFileUrl| https://manage.microsoftazure.de/publishsettings/index |
| ResourceManagerUrl | http://management.microsoftazure.de/ |
| SqlDatabaseDnsSuffix | .database.cloudapi.de |
| **StorageEndpointSuffix** | core.cloudapi.de |
| Przyciski ... | Przyciski ... | 

Do pobierania tylko właściwości sufiks punktu końcowego magazynu, należy pobrać określonej chmury i poproś o tylko jednej właściwości.

```powershell
$environment = Get-AzureRmEnvironment -Name AzureGermanCloud
Write-Host "Storage EndPoint Suffix = " $environment.StorageEndpointSuffix 
```

To polecenie zwróci następujące informacje.

```
Storage Endpoint Suffix = core.cloudapi.de
```

### <a name="get-endpoint-from-a-storage-account"></a>Pobierz punktu końcowego z konta magazynu

Można również sprawdzić właściwości konta magazynu do pobierania punktów końcowych. Może to być przydatne, jeśli korzystasz już z kontem magazynu za pomocą skryptu programu PowerShell; po prostu można pobrać punktu końcowego, które są potrzebne. 

```powershell
# Get a reference to the storage account.
$resourceGroup = "myexistingresourcegroup"
$storageAccountName = "myexistingstorageaccount"
$storageAccount = Get-AzureRmStorageAccount `
  -ResourceGroupName $resourceGroup `
  -Name $storageAccountName 
  # Output the endpoints.
Write-Host "blob endpoint = " $storageAccount.PrimaryEndPoints.Blob 
Write-Host "file endpoint = " $storageAccount.PrimaryEndPoints.File
Write-Host "queue endpoint = " $storageAccount.PrimaryEndPoints.Queue
Write-Host "table endpoint = " $storageAccount.PrimaryEndPoints.Table
```

Dla konta magazynu w chmurze dla instytucji rządowych to zwraca następujące czynności: 

```
blob endpoint = http://myexistingstorageaccount.blob.core.usgovcloudapi.net/
file endpoint = http://myexistingstorageaccount.file.core.usgovcloudapi.net/
queue endpoint = http://myexistingstorageaccount.queue.core.usgovcloudapi.net/
table endpoint = http://myexistingstorageaccount.table.core.usgovcloudapi.net/
```

## <a name="after-setting-the-environment"></a>Po ustawieniu środowiska

W tym miejscu przyszłości, można użyć tego samego środowiska PowerShell umożliwia zarządzanie kont magazynu i uzyskiwanie dostępu płaszczyzna danych, zgodnie z opisem w artykule [przy użyciu programu Azure PowerShell z usługą Azure Storage](storage-powershell-guide-full.md).

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli utworzono nową grupę zasobów i konto magazynu dla tego ćwiczenia, należy usunąć wszystkie zasoby przez usunięcie grupy zasobów. Spowoduje to również usunięcie wszystkich zasobów znajdujących się w grupie. W takim przypadku usuwa utworzono konto magazynu i grupy zasobów, do samej siebie.

```powershell
Remove-AzureRmResourceGroup -Name $resourceGroup
```

## <a name="next-steps"></a>Kolejne kroki

* [Utrwalanie identyfikatory logowania użytkownika między sesjami programu PowerShell](/powershell/azure/context-persistence)
* [Magazyn Azure dla instytucji rządowych](../../azure-government/documentation-government-services-storage.md)
* [Przewodnik dewelopera usługi Microsoft Azure dla instytucji rządowych](../../azure-government/documentation-government-developer-guide.md)
* [Uwagi dla deweloperów aplikacji Chin Azure](https://msdn.microsoft.com/library/azure/dn578439.aspx)
* [Niemcy Azure dokumentacji](../../germany/germany-welcome.md)
