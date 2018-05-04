---
title: Wybierz obrazy maszyny Wirtualnej systemu Windows na platformie Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak używać programu Azure PowerShell w celu określenia wydawcy, oferty, jednostki SKU i wersji dla obrazów maszyn wirtualnych w witrynie Marketplace.
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
ms.date: 02/28/2018
ms.author: danlep
ms.openlocfilehash: 885ee10bc63b65d936f5b433a18c4435b2503720
ms.sourcegitcommit: 6e43006c88d5e1b9461e65a73b8888340077e8a2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/01/2018
---
# <a name="how-to-find-windows-vm-images-in-the-azure-marketplace-with-azure-powershell"></a>Jak znaleźć obrazów maszyn wirtualnych systemu Windows w portalu Azure Marketplace przy użyciu programu Azure PowerShell

W tym artykule opisano sposób użycia programu Azure PowerShell można znaleźć obrazów maszyn wirtualnych w portalu Azure Marketplace. Te informacje służą do obrazu z witryny Marketplace można określić podczas tworzenia maszyny Wirtualnej programowo przy użyciu programu PowerShell, szablony usługi Resource Manager lub innych narzędzi.

Upewnij się, że zainstalowane i skonfigurowane najnowszej [modułu Azure PowerShell](/powershell/azure/install-azurerm-ps).

[!INCLUDE [virtual-machines-common-image-terms](../../../includes/virtual-machines-common-image-terms.md)]

## <a name="table-of-commonly-used-windows-images"></a>Tabela często używane obrazów systemu Windows
| Wydawca | Oferta | SKU |
|:--- |:--- |:--- |:--- |
| MicrosoftWindowsServer |WindowsServer |Centrum danych 2016 |
| MicrosoftWindowsServer |WindowsServer |2016-Datacenter-Server-Core |
| MicrosoftWindowsServer |WindowsServer |2016 centrum danych z kontenerów |
| MicrosoftWindowsServer |WindowsServer |2016-Nano-Server |
| MicrosoftWindowsServer |WindowsServer |2012-R2-Datacenter |
| MicrosoftWindowsServer |WindowsServer |2008 R2 SP1 |
| MicrosoftDynamicsNAV |DynamicsNAV |2017 |
| MicrosoftSharePoint |MicrosoftSharePointServer |2016 |
| MicrosoftSQLServer |SQL2014SP2-WS2012R2 |Enterprise |
| MicrosoftWindowsServerHPCPack |WindowsServerHPCPack |2012R2 |
| MicrosoftWindowsServerEssentials |WindowsServerEssentials |WindowsServerEssentials |

## <a name="navigate-the-images"></a>Przejdź obrazów

Innym sposobem znajdowania obrazu w lokalizacji jest uruchomienie [Get-AzureRMVMImagePublisher](/powershell/module/azurerm.compute/get-azurermvmimagepublisher), [Get-AzureRMVMImageOffer](/powershell/module/azurerm.compute/get-azurermvmimageoffer), i [Get-AzureRMVMImageSku](/powershell/module/azurerm.compute/get-azurermvmimagesku) polecenia cmdlet w sekwencji. Przy użyciu następujących poleceń można określić te wartości:

1. Wyświetl listę wydawców obrazów.
2. Dla danego wydawcy wyświetl listę ofert.
3. Dla danej oferty wyświetl listę wersji SKU.

Następnie dla wybranej jednostki SKU, uruchom [Get-AzureRMVMImage](/powershell/module/azurerm.compute/get-azurermvmimage) na liście wersji do wdrożenia.

Najpierw wyświetl listę wydawców za pomocą następujących poleceń:

```powershell
$locName="<Azure location, such as West US>"
Get-AzureRMVMImagePublisher -Location $locName | Select PublisherName
```

Wprowadź nazwę wybranego wydawcy i uruchom następujące polecenia:

```powershell
$pubName="<publisher>"
Get-AzureRMVMImageOffer -Location $locName -Publisher $pubName | Select Offer
```

Wprowadź nazwę wybranej oferty i uruchom następujące polecenia:

```powershell
$offerName="<offer>"
Get-AzureRMVMImageSku -Location $locName -Publisher $pubName -Offer $offerName | Select Skus
```

Wypełnij nazwę wybranej jednostki SKU i uruchom następujące polecenia:

```powershell
$skuName="<SKU>"
Get-AzureRMVMImage -Location $locName -Publisher $pubName -Offer $offerName -Sku $skuName | Select Version
```

Z danych wyjściowych `Get-AzureRMVMImage` polecenia, można wybrać obraz wersji wdrażania nowej maszyny wirtualnej.

W następujących poleceniach pokazano pełny przykład:

```powershell
$locName="West US"
Get-AzureRMVMImagePublisher -Location $locName | Select PublisherName

```

Dane wyjściowe:

```
PublisherName
-------------
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

Aby uzyskać *Windows Server* oferują:

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
2016-Nano-Server
```

Następnie dla *2016 Datacenter* SKU:

```powershell
$skuName="2016-Datacenter"
Get-AzureRMVMImage -Location $locName -Publisher $pubName -Offer $offerName -Sku $skuName | Select Version
```

Teraz można połączyć wybranego wydawcy, oferty, jednostki SKU i wersji, które znajdują się w identyfikatorem URN (wartości oddzielonych:). Przekaż ten URN z `--image` parametru podczas tworzenia maszyny Wirtualnej z [AzureRmVM nowy](/powershell/module/azurerm.compute/new-azurermvm) polecenia cmdlet. Należy pamiętać, że można opcjonalnie zastąpić numeru wersji w nazwy URN "r". Ta wersja jest zawsze najnowszą wersję obrazu. Można również użyć nazwy URN z [AzureRMVMSourceImage zestaw](/powershell/module/azurerm.compute/set-azurermvmsourceimage) polecenia cmdlet programu PowerShell. 

Należy wdrożyć maszynę Wirtualną za pomocą szablonu usługi Resource Manager, należy ustawić parametry obrazu indywidualnie w `imageReference` właściwości. Zobacz [odwołania do szablonu](/azure/templates/microsoft.compute/virtualmachines).

[!INCLUDE [virtual-machines-common-marketplace-plan](../../../includes/virtual-machines-common-marketplace-plan.md)]


### <a name="view-plan-properties"></a>Wyświetl właściwości planu

Aby wyświetlić informacji o planie zakupu obrazu, uruchom `Get-AzureRMVMImage` polecenia cmdlet. Jeśli `PurchasePlan` właściwość w danych wyjściowych nie jest `null`, obraz ma warunki musisz zaakceptować przed wdrażaniem programowym.  

Na przykład obrazu systemu Windows Server 2016 w centrum danych nie ma dodatkowych postanowień, ponieważ `PurchasePlan` informacje są `null`:

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

Uruchom polecenie podobne dla danych nauki maszyny wirtualnej — obrazu systemu Windows 2016 zawiera następujące `PurchasePlan` właściwości: `name`, `product`, i `publisher`. (Niektóre obrazy również mieć `promotion code` właściwości.) Aby wdrożyć ten obraz, zobacz następujące sekcje, aby zaakceptować warunki i włączyć wdrożenia programowe.

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

Aby wyświetlić postanowienia licencyjne, użyj [Get AzureRmMarketplaceterms](/powershell/module/azurerm.marketplaceordering/get-azurermmarketplaceterms) plan parametry polecenia cmdlet i przekaż zakupu. Dane wyjściowe Link do warunków dla obrazu z witryny Marketplace i pokazuje, czy wcześniej zaakceptowane postanowienia. Na przykład:

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

Użyj [AzureRmMarketplaceterms zestaw](/powershell/module/azurerm.marketplaceordering/set-azurermmarketplaceterms) polecenia cmdlet, aby zaakceptować lub odrzucić warunki. Musisz zaakceptować postanowienia raz w subskrypcji dla obrazu. Na przykład:

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
Signature         : VNMTRJK3MNJ5SROEG2BYDA2YGECU33GXTD3UFPLPC4BAVKAUL3PDYL3KBKBLG4ZCDJZVNSA7KJWTGMDSYDD6KRLV3LV274DLBJSS4GQ
Accepted          : True
Signdate          : 2/23/2018 7:49:31 PM
```

### <a name="deploy-using-purchase-plan-parameters"></a>Wdrażanie przy użyciu parametrów planu zakupów
Po zaakceptowaniu warunków obrazu, można wdrożyć maszyny Wirtualnej w ramach subskrypcji. Jak pokazano na poniższy fragment kodu, użyj [AzureRmVMPlan zestaw](/powershell/module/azurerm.compute/set-azurermvmplan) polecenia cmdlet można ustawić informacji o planie Marketplace dla obiekt maszyny Wirtualnej. Aby uzyskać pełną skrypt w celu skonfigurowania ustawień sieci dla maszyny Wirtualnej i wdrażania, zobacz [przykłady skryptów PowerShell](powershell-samples.md).

```powershell
...
$vmConfig = New-AzureRmVMConfig -VMName "myVM" -VMSize Standard_D1

# Set the Marketplace plan information
$vmConfig = Set-AzureRmVMPlan -VM $vmConfig -Publisher "imagePlanPublisher" -Product "imagePlanProduct" -Name "imagePlanName"

$cred=Get-Credential

$vmConfig = Set-AzureRmVMOperatingSystem -Windows -VM $vmConfig -ComputerName "myVM" -Credential $cred

# Set the Marketplace image
$vmConfig = Set-AzureRmVMSourceImage -VM $vmConfig -PublisherName "imagePublisher" -Offer "imageOffer" -Skus "imageSku" -Version "imageVersion"
...
```
Przekazuj konfiguracji maszyny Wirtualnej wraz z obiektami konfiguracji sieci, aby `New-AzureRmVM` polecenia cmdlet.

## <a name="next-steps"></a>Kolejne kroki
Aby utworzyć maszynę wirtualną, szybko z `New-AzureRmVM` przy użyciu obrazu podstawowych informacji, zobacz [Utwórz maszynę wirtualną z systemem Windows przy użyciu programu PowerShell](quick-create-powershell.md).

Przykładowy skrypt programu PowerShell, aby zobaczyć [Utwórz maszynę wirtualną w pełni skonfigurowany](../scripts/virtual-machines-windows-powershell-sample-create-vm.md).


