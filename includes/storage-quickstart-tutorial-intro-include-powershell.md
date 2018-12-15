---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 10/26/2018
ms.author: tamram
ms.openlocfilehash: aeefb63a283c473a98639ead1aa6640d88409125
ms.sourcegitcommit: eb9dd01614b8e95ebc06139c72fa563b25dc6d13
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/12/2018
ms.locfileid: "53318134"
---
## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Zaloguj się do subskrypcji platformy Azure za pomocą polecenia `Connect-AzureRmAccount` i postępuj zgodnie z instrukcjami wyświetlanymi na ekranie.

```powershell
Connect-AzureRmAccount
```

Jeśli nie wiesz, której lokalizacji użyć, możesz wyświetlić listę dostępnych lokalizacji. Wyświetl listę lokalizacji przy użyciu poniższego przykładu kodu i znajdź lokalizację, której chcesz użyć. W tym przykładzie użyto lokalizacji **eastus**. Zapisz lokalizację w zmiennej i używaj tej zmiennej, aby zmieniać lokalizację w jednym miejscu.

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

Utwórz standardowe konto magazynu ogólnego przeznaczenia z replikacją LRS za pomocą polecenia [New-AzureRmStorageAccount](/powershell/module/azurerm.storage/New-AzureRmStorageAccount). Następnie pobierz kontekst konta magazynu, który definiuje konto magazynu do użycia. Wykonując działania względem konta magazynu, odwołuj się do kontekstu, zamiast wielokrotnie przekazywać poświadczenia. Użyj poniższego przykładu, aby utworzyć konto magazynu o nazwie *mystorageaccount* z magazynem lokalnie nadmiarowym (LRS) i szyfrowaniem obiektów blob (włączonym domyślnie).

```powershell
$storageAccount = New-AzureRmStorageAccount -ResourceGroupName $resourceGroup `
  -Name "mystorageaccount" `
  -SkuName Standard_LRS `
  -Location $location `

$ctx = $storageAccount.Context
```
