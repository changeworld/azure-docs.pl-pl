---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 10/26/2018
ms.author: tamram
ms.openlocfilehash: 31ef8577a2304091fc4df1b394555c4b30fcf96e
ms.sourcegitcommit: 48592dd2827c6f6f05455c56e8f600882adb80dc
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/26/2018
ms.locfileid: "50166445"
---
## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Zaloguj się do subskrypcji platformy Azure za pomocą polecenia `Connect-AzureRmAccount` i postępuj zgodnie z instrukcjami wyświetlanymi na ekranie.

```powershell
Connect-AzureRmAccount
```

Jeśli nie wiesz, której lokalizacji użyć, możesz wyświetlić listę dostępnych lokalizacji. Po wyświetleniu listy znajdź lokalizację, której chcesz użyć. W tym przykładzie użyto lokalizacji **eastus**. Zapisz ją w zmiennej i używaj tej zmiennej, dzięki czemu będzie można zmieniać lokalizację w jednym miejscu.

```powershell
Get-AzureRmLocation | select Location 
$location = "eastus"
```

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Utwórz grupę zasobów platformy Azure za pomocą polecenia [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup). Grupa zasobów to logiczny kontener przeznaczony do wdrażania zasobów platformy Azure i zarządzania nimi. 

```powershell
$resourceGroup = "myResourceGroup"
New-AzureRmResourceGroup -Name $resourceGroup -Location $location 
```

## <a name="create-a-storage-account"></a>Tworzenie konta magazynu

Utwórz standardowe konto magazynu ogólnego przeznaczenia z replikacją LRS za pomocą polecenia [New-AzureRmStorageAccount](/powershell/module/azurerm.storage/New-AzureRmStorageAccount), a następnie pobierz kontekst konta magazynu, który definiuje konto magazynu do użycia. Wykonując działania względem konta magazynu, możesz odwoływać się do kontekstu, zamiast wielokrotnie podawać poświadczenia. W tym przykładzie tworzone jest konto magazynu o nazwie *mystorageaccount* z magazynem lokalnie nadmiarowym (LRS, locally redundant storage) i szyfrowaniem obiektów blob (domyślnie włączone).

```powershell
$storageAccount = New-AzureRmStorageAccount -ResourceGroupName $resourceGroup `
  -Name "mystorageaccount" `
  -Location $location `
  -SkuName Standard_LRS `
  -Kind Storage

$ctx = $storageAccount.Context
```
