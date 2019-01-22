---
title: Wybieranie obrazów maszyn wirtualnych Windows na platformie Azure | Dokumentacja firmy Microsoft
description: Użyj programu Azure PowerShell, aby określić wydawcy, oferty, jednostki SKU i wersji dla obrazów maszyn wirtualnych w portalu Marketplace.
services: virtual-machines-windows
documentationcenter: ''
author: dlepow
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 188b8974-fabd-4cd3-b7dc-559cbb86b98a
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 10/08/2018
ms.author: danlep
ms.openlocfilehash: ff4ccdf28be9d28798fff0e9f66bbb2c860166b7
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/22/2019
ms.locfileid: "54424544"
---
# <a name="find-windows-vm-images-in-the-azure-marketplace-with-azure-powershell"></a>Znajdowanie obrazów maszyn wirtualnych Windows w witrynie Azure Marketplace przy użyciu programu Azure PowerShell

W tym artykule opisano, jak znajdowanie obrazów maszyn wirtualnych w witrynie Azure Marketplace przy użyciu programu Azure PowerShell. Następnie można określić obrazu z witryny Marketplace, gdy programowo utworzyć Maszynę wirtualną przy użyciu programu PowerShell, szablony usługi Resource Manager lub innych narzędzi.

Można również przeglądać dostępne obrazy i ofert przy użyciu [portalu Azure Marketplace](https://azuremarketplace.microsoft.com/) storefront, [witryny Azure portal](https://portal.azure.com), lub [wiersza polecenia platformy Azure](../linux/cli-ps-findimage.md). 

Upewnij się, że zostały zainstalowane i skonfigurowane najnowsze [modułu Azure PowerShell](/powershell/azure/azurerm/install-azurerm-ps).

[!INCLUDE [virtual-machines-common-image-terms](../../../includes/virtual-machines-common-image-terms.md)]

## <a name="table-of-commonly-used-windows-images"></a>Tabela najczęściej używanych obrazów Windows
| Wydawca | Oferta | SKU |
|:--- |:--- |:--- |:--- |
| MicrosoftWindowsServer |WindowsServer |2016-Datacenter |
| MicrosoftWindowsServer |WindowsServer |2016-Datacenter-Server-Core |
| MicrosoftWindowsServer |WindowsServer |2016 centrum danych za pomocą kontenerów |
| MicrosoftWindowsServer |WindowsServer |2012-R2-Datacenter |
| MicrosoftWindowsServer |WindowsServer |2012-Datacenter |
| MicrosoftWindowsServer |WindowsServer |2008-R2-SP1 |
| MicrosoftDynamicsNAV |DynamicsNAV |2017 |
| MicrosoftSharePoint |MicrosoftSharePointServer |2016 |
| MicrosoftSQLServer |SQL2017-WS2016 |Enterprise |
| MicrosoftRServer |RServer-WS2016 |Enterprise |

## <a name="navigate-the-images"></a>Nawigować po obrazach

Jednym sposobem, aby znaleźć obrazu w lokalizacji jest uruchomienie [Get-AzureRMVMImagePublisher](/powershell/module/azurerm.compute/get-azurermvmimagepublisher), [Get-AzureRMVMImageOffer](/powershell/module/azurerm.compute/get-azurermvmimageoffer), i [Get-AzureRMVMImageSku](/powershell/module/azurerm.compute/get-azurermvmimagesku) poleceń cmdlet w kolejności:

1. Wyświetl listę wydawców obrazów.
2. Dla danego wydawcy wyświetl listę ofert.
3. Dla danej oferty wyświetl listę wersji SKU.

Dla wybranej jednostki SKU, uruchom [Get AzureRMVMImage](/powershell/module/azurerm.compute/get-azurermvmimage) Aby wyświetlić listę wersji do wdrożenia.

1. Wyświetl listę wydawców:

    ```powershell
    $locName="<Azure location, such as West US>"
    Get-AzureRMVMImagePublisher -Location $locName | Select PublisherName
    ```

2. Wprowadź nazwę wybranego wydawcy i oferty listy:

    ```powershell
    $pubName="<publisher>"
    Get-AzureRMVMImageOffer -Location $locName -Publisher $pubName | Select Offer
    ```

3. Wprowadź nazwę wybranej oferty i wyświetlić listę jednostek SKU:

    ```powershell
    $offerName="<offer>"
    Get-AzureRMVMImageSku -Location $locName -Publisher $pubName -Offer $offerName | Select Skus
    ```
    
4. Wprowadź nazwę wybranej jednostki SKU i Pobierz wersję obrazu:

    ```powershell
    $skuName="<SKU>"
    Get-AzureRMVMImage -Location $locName -Publisher $pubName -Offer $offerName -Sku $skuName | Select Version
    ```
    
Z danych wyjściowych `Get-AzureRMVMImage` polecenia, można wybrać obraz wersji, aby wdrożyć nową maszynę wirtualną.

Poniższy kod przedstawia pełny kolejność poleceń i ich dane wyjściowe:

```powershell
$locName="West US"
Get-AzureRMVMImagePublisher -Location $locName | Select PublisherName
```

Częściowe dane wyjściowe:

```
PublisherName
-------------
...
a10networks
aiscaler-cache-control-ddos-and-url-rewriting-
alertlogic
AlertLogic.Extension
Barracuda.Azure.ConnectivityAgent
barracudanetworks
basho
boxless
bssw
Canonical
...
```

Aby uzyskać *MicrosoftWindowsServer* wydawcy:

```powershell
$pubName="MicrosoftWindowsServer"
Get-AzureRMVMImageOffer -Location $locName -Publisher $pubName | Select Offer
```

Dane wyjściowe:

```
Offer
-----
Windows-HUB
WindowsServer
WindowsServerSemiAnnual
```

Aby uzyskać *WindowsServer* oferty:

```powershell
$offerName="WindowsServer"
Get-AzureRMVMImageSku -Location $locName -Publisher $pubName -Offer $offerName | Select Skus
```

Dane wyjściowe:

```
Skus
----
2008-R2-SP1
2008-R2-SP1-smalldisk
2012-Datacenter
2012-Datacenter-smalldisk
2012-R2-Datacenter
2012-R2-Datacenter-smalldisk
2016-Datacenter
2016-Datacenter-Server-Core
2016-Datacenter-Server-Core-smalldisk
2016-Datacenter-smalldisk
2016-Datacenter-with-Containers
2016-Datacenter-with-RDSH
```

Następnie dla *2016 Datacenter* jednostki SKU:

```powershell
$skuName="2016-Datacenter"
Get-AzureRMVMImage -Location $locName -Publisher $pubName -Offer $offerName -Sku $skuName | Select Version
```

Teraz można połączyć wybranego wydawcy, oferty, jednostki SKU i wersji, które znajdują się w URN (wartości rozdzielone:). Przekaż ten URN z `--image` parametru podczas tworzenia maszyny Wirtualnej z [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm) polecenia cmdlet. Można opcjonalnie zastąpić numer wersji w URN "najnowszej"wersji, aby uzyskać najnowszą wersję obrazu.

Jeśli wdrożysz maszynę Wirtualną przy użyciu szablonu usługi Resource Manager, a następnie parametry obrazu będzie ustawić indywidualnie w `imageReference` właściwości. Zobacz [dokumentację szablonu](/azure/templates/microsoft.compute/virtualmachines).

[!INCLUDE [virtual-machines-common-marketplace-plan](../../../includes/virtual-machines-common-marketplace-plan.md)]

### <a name="view-plan-properties"></a>Wyświetl właściwości planu

Aby wyświetlić informacje o planu zakupu z obrazu, uruchom `Get-AzureRMVMImage` polecenia cmdlet. Jeśli `PurchasePlan` właściwość w danych wyjściowych jest `null`, obraz, który ma warunki muszą zaakceptować przed przystąpieniem do wdrożenia programowego.  

Na przykład *systemu Windows Server 2016 Datacenter* obraz nie ma dodatkowe postanowienia, więc `PurchasePlan` informacje są `null`:

```powershell
$version = "2016.127.20170406"
Get-AzureRMVMImage -Location $locName -Publisher $pubName -Offer $offerName -Skus $skuName -Version $version
```

Dane wyjściowe:

```
Id               : /Subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/Providers/Microsoft.Compute/Locations/westus/Publishers/MicrosoftWindowsServer/ArtifactTypes/VMImage/Offers/WindowsServer/Skus/2016-Datacenter/
                   Versions/2016.127.20170406
Location         : westus
PublisherName    : MicrosoftWindowsServer
Offer            : WindowsServer
Skus             : 2016-Datacenter
Version          : 2016.127.20170406
FilterExpression :
Name             : 2016.127.20170406
OSDiskImage      : {
                     "operatingSystem": "Windows"
                   }
PurchasePlan     : null
DataDiskImages   : []

```

W poniższym przykładzie pokazano polecenie podobne do *maszyna wirtualna do nauki o danych — Windows 2016* obrazu, który ma `PurchasePlan` właściwości: `name`, `product`, i `publisher`. Niektóre obrazy również mieć `promotion code` właściwości. Aby wdrożyć ten obraz, zobacz następujące sekcje, aby zaakceptować warunki i włączyć wdrożenia programowe.

```powershell
Get-AzureRMVMImage -Location "westus" -Publisher "microsoft-ads" -Offer "windows-data-science-vm" -Skus "windows2016" -Version "0.2.02"
```

Dane wyjściowe:

```
Id               : /Subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/Providers/Microsoft.Compute/Locations/westus/Publishers/microsoft-ads/ArtifactTypes/VMIma
                   ge/Offers/windows-data-science-vm/Skus/windows2016/Versions/0.2.02
Location         : westus
PublisherName    : microsoft-ads
Offer            : windows-data-science-vm
Skus             : windows2016
Version          : 0.2.02
FilterExpression :
Name             : 0.2.02
OSDiskImage      : {
                     "operatingSystem": "Windows"
                   }
PurchasePlan     : {
                     "publisher": "microsoft-ads",
                     "name": "windows2016",
                     "product": "windows-data-science-vm"
                   }
DataDiskImages   : []

```

### <a name="accept-the-terms"></a>Zaakceptuj warunki

Aby wyświetlić postanowienia licencyjne, użyj [Get AzureRmMarketplaceterms](/powershell/module/azurerm.marketplaceordering/get-azurermmarketplaceterms) polecenia cmdlet i przekaż zakupu planu parametrów. Dane wyjściowe zawiera również link do ze wszystkimi postanowieniami dotyczącymi obrazu z witryny Marketplace i pokazuje, czy wcześniej zaakceptowane warunki. Należy używać małych liter w wartości parametrów.

```powershell
Get-AzureRmMarketplaceterms -Publisher "microsoft-ads" -Product "windows-data-science-vm" -Name "windows2016"
```

Dane wyjściowe:

```
Publisher         : microsoft-ads
Product           : windows-data-science-vm
Plan              : windows2016
LicenseTextLink   : https://storelegalterms.blob.core.windows.net/legalterms/3E5ED_legalterms_MICROSOFT%253a2DADS%253a24WINDOWS%253a2DDATA%253a2DSCIENCE%253a2DV
                    M%253a24WINDOWS2016%253a24OC5SKMQOXSED66BBSNTF4XRCS4XLOHP7QMPV54DQU7JCBZWYFP35IDPOWTUKXUC7ZAG7W6ZMDD6NHWNKUIVSYBZUTZ245F44SU5AD7Q.txt
PrivacyPolicyLink : https://www.microsoft.com/EN-US/privacystatement/OnlineServices/Default.aspx
Signature         : 2UMWH6PHSAIM4U22HXPXW25AL2NHUJ7Y7GRV27EBL6SUIDURGMYG6IIDO3P47FFIBBDFHZHSQTR7PNK6VIIRYJRQ3WXSE6BTNUNENXA
Accepted          : False
Signdate          : 2/23/2018 7:43:00 PM
```

Użyj [AzureRmMarketplaceterms zestaw](/powershell/module/azurerm.marketplaceordering/set-azurermmarketplaceterms) polecenia cmdlet, aby zaakceptować lub odrzucić warunki. Musisz zaakceptować warunki raz na subskrypcję dla obrazu. Należy używać małych liter w wartości parametrów. 

```powershell
$agreementTerms=Get-AzureRmMarketplaceterms -Publisher "microsoft-ads" -Product "windows-data-science-vm" -Name "windows2016"

Set-AzureRmMarketplaceTerms -Publisher "microsoft-ads" -Product "windows-data-science-vm" -Name "windows2016" -Terms $agreementTerms -Accept
```

Dane wyjściowe:

```
Publisher         : microsoft-ads
Product           : windows-data-science-vm
Plan              : windows2016
LicenseTextLink   : https://storelegalterms.blob.core.windows.net/legalterms/3E5ED_legalterms_MICROSOFT%253a2DADS%253a24WINDOWS%253a2DDATA%253a2DSCIENCE%253a2DV
                    M%253a24WINDOWS2016%253a24OC5SKMQOXSED66BBSNTF4XRCS4XLOHP7QMPV54DQU7JCBZWYFP35IDPOWTUKXUC7ZAG7W6ZMDD6NHWNKUIVSYBZUTZ245F44SU5AD7Q.txt
PrivacyPolicyLink : https://www.microsoft.com/EN-US/privacystatement/OnlineServices/Default.aspx
Signature         : XXXXXXK3MNJ5SROEG2BYDA2YGECU33GXTD3UFPLPC4BAVKAUL3PDYL3KBKBLG4ZCDJZVNSA7KJWTGMDSYDD6KRLV3LV274DLBXXXXXX
Accepted          : True
Signdate          : 2/23/2018 7:49:31 PM
```

### <a name="deploy-using-purchase-plan-parameters"></a>Wdrażanie przy użyciu parametrów planu zakupu

Po zaakceptowaniu warunków dla obrazu, możesz wdrożyć Maszynę wirtualną w tej subskrypcji. Jak pokazano w poniższym fragmencie kodu, należy użyć [AzureRmVMPlan zestaw](/powershell/module/azurerm.compute/set-azurermvmplan) polecenia cmdlet można ustawić informacji o planie witryny Marketplace dla obiektu maszyny Wirtualnej. Aby uzyskać kompletny skrypt utworzyć ustawienia sieci dla maszyny Wirtualnej i ukończyć wdrażanie, zobacz [przykłady skryptów programu PowerShell](powershell-samples.md).

```powershell
...

$vmConfig = New-AzureRmVMConfig -VMName "myVM" -VMSize Standard_D1

# Set the Marketplace plan information

$publisherName = "microsoft-ads"

$productName = "windows-data-science-vm"

$planName = "windows2016"

$vmConfig = Set-AzureRmVMPlan -VM $vmConfig -Publisher $publisherName -Product $productName -Name $planName

$cred=Get-Credential

$vmConfig = Set-AzureRmVMOperatingSystem -Windows -VM $vmConfig -ComputerName "myVM" -Credential $cred

# Set the Marketplace image

$offerName = "windows-data-science-vm"

$skuName = "windows2016"

$version = "0.2.02"

$vmConfig = Set-AzureRmVMSourceImage -VM $vmConfig -PublisherName $publisherName -Offer $offerName -Skus $skuName -Version $version
...
```
Będzie następnie przekazać konfiguracji maszyny Wirtualnej wraz z obiektami konfiguracji sieci, aby `New-AzureRmVM` polecenia cmdlet.

## <a name="next-steps"></a>Kolejne kroki
Aby utworzyć maszynę wirtualną, szybko `New-AzureRmVM` polecenia cmdlet przy użyciu obrazu podstawowego informacji, zobacz [Utwórz maszynę wirtualną Windows przy użyciu programu PowerShell](quick-create-powershell.md).

Przykładowy skrypt programu PowerShell, aby zobaczyć [tworzenie w pełni skonfigurowanej maszyny wirtualnej](../scripts/virtual-machines-windows-powershell-sample-create-vm.md).


