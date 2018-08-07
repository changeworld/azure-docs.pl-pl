---
title: Zarządzanie magazynem w platformy Azure niezależnie od chmur przy użyciu programu Azure PowerShell | Dokumentacja firmy Microsoft
description: Zarządzanie magazynem w chmurze w Chinach, chmury dla instytucji rządowych i niemieckiej wersji chmury przy użyciu programu Azure PowerShell
services: storage
author: roygara
ms.service: storage
ms.topic: article
ms.date: 10/24/2017
ms.author: rogarana
ms.component: common
ms.openlocfilehash: f3f827958c0c5d71846d8f0d4184ef88b7f0a932
ms.sourcegitcommit: 9819e9782be4a943534829d5b77cf60dea4290a2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/06/2018
ms.locfileid: "39527407"
---
# <a name="managing-storage-in-the-azure-independent-clouds-using-powershell"></a>Zarządzanie magazynem w niezależnych chmury platformy Azure przy użyciu programu PowerShell

Większość osób na użytek chmurze publicznej platformy Azure ich globalnego wdrażania platformy Azure. Istnieją również pewne niezależne wdrożenia systemu Microsoft Azure, aby niezależności i tak dalej. Niezależne wdrożenia są określane jako "środowisk". Poniżej przedstawiono szczegółową listę chmury niezależne, obecnie dostępna.

* [Chmura platformy Azure dla instytucji rządowych](https://azure.microsoft.com/features/gov/)
* [Obsługiwana przez firmę 21Vianet w Chinach chmury platformy Azure (Chiny)](http://www.windowsazure.cn/)
* [Usługa Azure niemieckiej wersji chmury](../../germany/germany-welcome.md)

## <a name="using-an-independent-cloud"></a>Przy użyciu chmury niezależne 

Używać usługi Azure Storage w jednym z chmury niezależne, połączenie z tą chmurą zamiast publicznej Azure. Aby użyć jednego z chmury niezależne zamiast publicznej platformy Azure:

* Należy określić *środowiska* którym chcesz się połączyć.
* Możesz określić i używać dostępnych regionów.
* Możesz użyć sufiksu właściwego punktu końcowego, który jest inny niż publicznej Azure.

Przykłady wymagają programu Azure PowerShell module wersję 4.4.0 lub nowszej. W oknie programu PowerShell, uruchom `Get-Module -ListAvailable AzureRM` Aby znaleźć wersję. Jeśli nic nie jest wyświetlane, lub należy go uaktualnić, zobacz [Instalowanie modułu Azure PowerShell](/powershell/azure/install-azurerm-ps). 

## <a name="log-in-to-azure"></a>Zaloguj się do platformy Azure.

Uruchom [Get AzureEnvironment](/powershell/module/azure/Get-AzureRmEnvironment) polecenia cmdlet, aby wyświetlić dostępne środowiska platformy Azure:
   
```powershell
Get-AzureRmEnvironment
```

Zaloguj się do swojego konta, które ma dostęp do chmury, do którego chcesz połączyć, a następnie ustaw środowisko. Ten przykład pokazuje, jak zalogować się do konta, które korzysta z chmury dla instytucji rządowych platformy Azure.   

```powershell
Connect-AzureRmAccount –Environment AzureUSGovernment
```

Aby uzyskać dostęp do chmury w Chinach, należy użyć środowiska **AzureChinaCloud**. Aby uzyskać dostęp do chmury niemiecki, należy użyć **AzureGermanCloud**.

W tym momencie listę lokalizacji, aby utworzyć konto magazynu lub innego zasobu, należy można tworzyć zapytania lokalizacje dostępne dla wybranej chmury za pomocą [Get-AzureRmLocation](/powershell/module/azurerm.resources/get-azurermlocation).

```powershell
Get-AzureRmLocation | select Location, DisplayName
```

W poniższej tabeli przedstawiono lokalizacje zwrócony dla niemieckiej wersji chmury.

|Lokalizacja | Nazwa wyświetlana |
|----|----|
| germanycentral | Niemcy Środkowe|
| germanynortheast | Niemcy Północno-Wschodnie | 


## <a name="endpoint-suffix"></a>Sufiks punktu końcowego

Sufiks punktu końcowego dla każdego z tych środowisk różni się od punktu końcowego publicznego platformy Azure. Na przykład sufiks punktu końcowego obiektu blob dla publicznej platformy Azure jest **blob.core.windows.net**. W przypadku chmury dla instytucji rządowych jest sufiks punktu końcowego obiektu blob **blob.core.usgovcloudapi.net**. 

### <a name="get-endpoint-using-get-azurermenvironment"></a>Pobieranie punktu końcowego przy użyciu Get-AzureRMEnvironment 

Pobrać za pomocą sufiksu punktu końcowego [Get-AzureRMEnvironment](/powershell/module/azurerm.profile/get-azurermenvironment). Punkt końcowy jest *StorageEndpointSuffix* właściwości środowiska. Poniższe fragmenty kodu pokazują, jak to zrobić. Wszystkie te polecenia zwrócić coś takich jak "core.cloudapp.net" lub "core.cloudapi.de" itp. Dołącz podany kod do usługi storage, dostęp do tej usługi. Na przykład "queue.core.cloudapi.de" uzyskają dostęp do usługi kolejki, w chmurze niemiecki.

Ten fragment kodu pobiera wszystkie środowiska i sufiks punktu końcowego dla każdej z nich.

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

Aby pobrać wszystkie właściwości dla określonego środowiska, należy wywołać **Get-AzureRmEnvironment** i określ nazwę chmury. Ten fragment kodu zwraca listę właściwości; Wyszukaj **StorageEndpointSuffix** na liście. Poniższy przykład jest w chmurze niemiecki.

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

Aby pobrać tylko właściwości sufiks punktu końcowego magazynu, pobrać określonej chmury i poproś o tylko jednej właściwości.

```powershell
$environment = Get-AzureRmEnvironment -Name AzureGermanCloud
Write-Host "Storage EndPoint Suffix = " $environment.StorageEndpointSuffix 
```

Spowoduje to zwrócenie następujących informacji.

```
Storage Endpoint Suffix = core.cloudapi.de
```

### <a name="get-endpoint-from-a-storage-account"></a>Uzyskiwanie punktu końcowego konta magazynu

Można także sprawdzić właściwości konta magazynu można pobrać z punktami końcowymi. Może to być przydatne, jeśli już korzystasz z konta magazynu za pomocą skryptu programu PowerShell; może po prostu pobrać punktu końcowego, które są potrzebne. 

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

Konto magazynu w chmurze dla instytucji rządowych to zwraca następujące czynności: 

```
blob endpoint = http://myexistingstorageaccount.blob.core.usgovcloudapi.net/
file endpoint = http://myexistingstorageaccount.file.core.usgovcloudapi.net/
queue endpoint = http://myexistingstorageaccount.queue.core.usgovcloudapi.net/
table endpoint = http://myexistingstorageaccount.table.core.usgovcloudapi.net/
```

## <a name="after-setting-the-environment"></a>Po ustawieniu środowiska

W tym miejscu przyszłości, można użyć tego samego programu PowerShell, używane do zarządzania kontami magazynu i uzyskiwania dostępu do płaszczyzny danych, zgodnie z opisem w artykule [przy użyciu programu Azure PowerShell z usługą Azure Storage](storage-powershell-guide-full.md).

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli utworzono nową grupę zasobów i konto magazynu na potrzeby tego ćwiczenia, można usunąć wszystkie zasoby, usuwając grupę zasobów. Spowoduje to również usunięcie wszystkich zasobów znajdujących się w grupie. W tym przypadku powoduje usunięcie konta magazynu i samej grupy zasobów.

```powershell
Remove-AzureRmResourceGroup -Name $resourceGroup
```

## <a name="next-steps"></a>Kolejne kroki

* [Zachowywanie danych logowania użytkownika między sesjami programu PowerShell](/powershell/azure/context-persistence)
* [Usługa Azure storage dla instytucji rządowych](../../azure-government/documentation-government-services-storage.md)
* [Microsoft Azure dla instytucji rządowych — przewodnik dla deweloperów](../../azure-government/documentation-government-developer-guide.md)
* [Uwagi dla deweloperów dla aplikacji platformy Azure (Chiny)](https://msdn.microsoft.com/library/azure/dn578439.aspx)
* [Dokumentacja usługi Azure (Niemcy)](../../germany/germany-welcome.md)
