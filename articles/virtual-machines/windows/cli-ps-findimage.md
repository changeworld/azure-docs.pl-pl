---
title: Wybieranie obrazów maszyn wirtualnych Windows na platformie Azure | Dokumentacja firmy Microsoft
description: Użyj programu Azure PowerShell, aby określić wydawcy, oferty, jednostki SKU i wersji dla obrazów maszyn wirtualnych w portalu Marketplace.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 188b8974-fabd-4cd3-b7dc-559cbb86b98a
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 01/25/2019
ms.author: cynthn
ms.openlocfilehash: 893b71d3a1cc6ece8272cb1a372302ff384003dd
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "64693766"
---
# <a name="find-windows-vm-images-in-the-azure-marketplace-with-azure-powershell"></a>Znajdowanie obrazów maszyn wirtualnych Windows w witrynie Azure Marketplace przy użyciu programu Azure PowerShell

W tym artykule opisano, jak znajdowanie obrazów maszyn wirtualnych w witrynie Azure Marketplace przy użyciu programu Azure PowerShell. Następnie można określić obrazu z witryny Marketplace, gdy programowo utworzyć Maszynę wirtualną przy użyciu programu PowerShell, szablony usługi Resource Manager lub innych narzędzi.

Można również przeglądać dostępne obrazy i ofert przy użyciu [portalu Azure Marketplace](https://azuremarketplace.microsoft.com/) storefront, [witryny Azure portal](https://portal.azure.com), lub [wiersza polecenia platformy Azure](../linux/cli-ps-findimage.md). 

[!INCLUDE [updated-for-az.md](../../../includes/updated-for-az.md)]

[!INCLUDE [virtual-machines-common-image-terms](../../../includes/virtual-machines-common-image-terms.md)]

## <a name="table-of-commonly-used-windows-images"></a>Tabela najczęściej używanych obrazów Windows

W poniższej tabeli przedstawiono podzbioru dostępnych jednostek SKU dla wskazanych wydawców i ofert.

| Wydawca | Oferta | SKU |
|:--- |:--- |:--- |
| MicrosoftWindowsServer |WindowsServer |2019-Datacenter |
| MicrosoftWindowsServer |WindowsServer |2019-Datacenter-Core |
| MicrosoftWindowsServer |WindowsServer |2019 centrum danych za pomocą kontenerów |
| MicrosoftWindowsServer |WindowsServer |2016-Datacenter |
| MicrosoftWindowsServer |WindowsServer |2016-Datacenter-Server-Core |
| MicrosoftWindowsServer |WindowsServer |2016 centrum danych za pomocą kontenerów |
| MicrosoftWindowsServer |WindowsServer |2012-R2-Datacenter |
| MicrosoftWindowsServer |WindowsServer |2012-Datacenter |
| MicrosoftDynamicsNAV |DynamicsNAV |2017 |
| MicrosoftSharePoint |MicrosoftSharePointServer |2019 |
| MicrosoftSQLServer |SQL2019-WS2016 |Enterprise |
| MicrosoftRServer |RServer-WS2016 |Enterprise |

## <a name="navigate-the-images"></a>Nawigować po obrazach

Jednym sposobem, aby znaleźć obrazu w lokalizacji jest uruchomienie [Get AzVMImagePublisher](https://docs.microsoft.com/powershell/module/az.compute/get-azvmimagepublisher), [Get AzVMImageOffer](https://docs.microsoft.com/powershell/module/az.compute/get-azvmimageoffer), i [Get AzVMImageSku](https://docs.microsoft.com/powershell/module/az.compute/get-azvmimagesku) poleceń cmdlet w kolejności:

1. Wyświetl listę wydawców obrazów.
2. Dla danego wydawcy wyświetl listę ofert.
3. Dla danej oferty wyświetl listę wersji SKU.

Dla wybranej jednostki SKU, uruchom [Get AzVMImage](https://docs.microsoft.com/powershell/module/az.compute/get-azvmimage) Aby wyświetlić listę wersji do wdrożenia.

1. Wyświetl listę wydawców:

    ```powershell
    $locName="<Azure location, such as West US>"
    Get-AzVMImagePublisher -Location $locName | Select PublisherName
    ```

2. Wprowadź nazwę wybranego wydawcy i oferty listy:

    ```powershell
    $pubName="<publisher>"
    Get-AzVMImageOffer -Location $locName -PublisherName $pubName | Select Offer
    ```

3. Wprowadź nazwę wybranej oferty i wyświetlić listę jednostek SKU:

    ```powershell
    $offerName="<offer>"
    Get-AzVMImageSku -Location $locName -PublisherName $pubName -Offer $offerName | Select Skus
    ```

4. Wprowadź nazwę wybranej jednostki SKU i Pobierz wersję obrazu:

    ```powershell
    $skuName="<SKU>"
    Get-AzVMImage -Location $locName -PublisherName $pubName -Offer $offerName -Sku $skuName | Select Version
    ```
    
Z danych wyjściowych `Get-AzVMImage` polecenia, można wybrać obraz wersji, aby wdrożyć nową maszynę wirtualną.

Poniższy kod przedstawia pełny kolejność poleceń i ich dane wyjściowe:

```powershell
$locName="West US"
Get-AzVMImagePublisher -Location $locName | Select PublisherName
```

Częściowe dane wyjściowe:

```
PublisherName
-------------
...
abiquo
accedian
accellion
accessdata-group
accops
Acronis
Acronis.Backup
actian-corp
actian_matrix
actifio
activeeon
adgs
advantech
advantech-webaccess
advantys
...
```

Aby uzyskać *MicrosoftWindowsServer* wydawcy:

```powershell
$pubName="MicrosoftWindowsServer"
Get-AzVMImageOffer -Location $locName -PublisherName $pubName | Select Offer
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
Get-AzVMImageSku -Location $locName -PublisherName $pubName -Offer $offerName | Select Skus
```

Częściowe dane wyjściowe:

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
2019-Datacenter
2019-Datacenter-Core
2019-Datacenter-Core-smalldisk
2019-Datacenter-Core-with-Containers
...
```

Następnie dla *2019-Datacenter* jednostki SKU:

```powershell
$skuName="2019-Datacenter"
Get-AzVMImage -Location $locName -PublisherName $pubName -Offer $offerName -Sku $skuName | Select Version
```

Teraz można połączyć wybranego wydawcy, oferty, jednostki SKU i wersji, które znajdują się w URN (wartości rozdzielone:). Przekaż ten URN z `--image` parametru podczas tworzenia maszyny Wirtualnej z [New-AzVM](https://docs.microsoft.com/powershell/module/az.compute/new-azvm) polecenia cmdlet. Można opcjonalnie zastąpić numer wersji w URN "najnowszej"wersji, aby uzyskać najnowszą wersję obrazu.

Jeśli wdrożysz maszynę Wirtualną przy użyciu szablonu usługi Resource Manager, a następnie parametry obrazu będzie ustawić indywidualnie w `imageReference` właściwości. Zobacz [dokumentację szablonu](/azure/templates/microsoft.compute/virtualmachines).

[!INCLUDE [virtual-machines-common-marketplace-plan](../../../includes/virtual-machines-common-marketplace-plan.md)]

### <a name="view-plan-properties"></a>Wyświetl właściwości planu

Aby wyświetlić informacje o planu zakupu z obrazu, uruchom `Get-AzVMImage` polecenia cmdlet. Jeśli `PurchasePlan` właściwość w danych wyjściowych jest `null`, obraz, który ma warunki muszą zaakceptować przed przystąpieniem do wdrożenia programowego.  

Na przykład *systemu Windows Server 2016 Datacenter* obraz nie ma dodatkowe postanowienia, więc `PurchasePlan` informacje są `null`:

```powershell
$version = "2016.127.20170406"
Get-AzVMImage -Location $locName -PublisherName $pubName -Offer $offerName -Skus $skuName -Version $version
```

Dane wyjściowe:

```
Id               : /Subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/Providers/Microsoft.Compute/Locations/westus/Publishers/MicrosoftWindowsServer/ArtifactTypes/VMImage/Offers/WindowsServer/Skus/2016-Datacenter/Versions/2019.0.20190115
Location         : westus
PublisherName    : MicrosoftWindowsServer
Offer            : WindowsServer
Skus             : 2019-Datacenter
Version          : 2019.0.20190115
FilterExpression :
Name             : 2019.0.20190115
OSDiskImage      : {
                     "operatingSystem": "Windows"
                   }
PurchasePlan     : null
DataDiskImages   : []

```

W poniższym przykładzie pokazano polecenie podobne do *maszyna wirtualna do nauki o danych — Windows 2016* obrazu, który ma `PurchasePlan` właściwości: `name`, `product`, i `publisher`. Niektóre obrazy również mieć `promotion code` właściwości. Aby wdrożyć ten obraz, zobacz następujące sekcje, aby zaakceptować warunki i włączyć wdrożenia programowe.

```powershell
Get-AzVMImage -Location "westus" -PublisherName "microsoft-ads" -Offer "windows-data-science-vm" -Skus "windows2016" -Version "0.2.02"
```

Dane wyjściowe:

```
Id               : /Subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/Providers/Microsoft.Compute/Locations/westus/Publishers/microsoft-ads/ArtifactTypes/VMImage/Offers/windows-data-science-vm/Skus/windows2016/Versions/19.01.14
Location         : westus
PublisherName    : microsoft-ads
Offer            : windows-data-science-vm
Skus             : windows2016
Version          : 19.01.14
FilterExpression :
Name             : 19.01.14
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

Aby wyświetlić postanowienia licencyjne, użyj [Get AzMarketplaceterms](https://docs.microsoft.com/powershell/module/az.marketplaceordering/get-azmarketplaceterms) polecenia cmdlet i przekaż zakupu planu parametrów. Dane wyjściowe zawiera również link do ze wszystkimi postanowieniami dotyczącymi obrazu z witryny Marketplace i pokazuje, czy wcześniej zaakceptowane warunki. Należy używać małych liter w wartości parametrów.

```powershell
Get-AzMarketplaceterms -Publisher "microsoft-ads" -Product "windows-data-science-vm" -Name "windows2016"
```

Dane wyjściowe:

```
Publisher         : microsoft-ads
Product           : windows-data-science-vm
Plan              : windows2016
LicenseTextLink   : https://storelegalterms.blob.core.windows.net/legalterms/3E5ED_legalterms_MICROSOFT%253a2DADS%253a24WINDOWS%253a2DDATA%253a2DSCIENCE%253a2DVM%253a24WINDOWS2016%253a24OC5SKMQOXSED66BBSNTF4XRCS4XLOHP7QMPV54DQU7JCBZWYFP35IDPOWTUKXUC7ZAG7W6ZMDD6NHWNKUIVSYBZUTZ245F44SU5AD7Q.txt
PrivacyPolicyLink : https://www.microsoft.com/EN-US/privacystatement/OnlineServices/Default.aspx
Signature         : 2UMWH6PHSAIM4U22HXPXW25AL2NHUJ7Y7GRV27EBL6SUIDURGMYG6IIDO3P47FFIBBDFHZHSQTR7PNK6VIIRYJRQ3WXSE6BTNUNENXA
Accepted          : False
Signdate          : 1/25/2019 7:43:00 PM
```

Użyj [AzMarketplaceterms zestaw](https://docs.microsoft.com/powershell/module/az.marketplaceordering/set-azmarketplaceterms) polecenia cmdlet, aby zaakceptować lub odrzucić warunki. Musisz zaakceptować warunki raz na subskrypcję dla obrazu. Należy używać małych liter w wartości parametrów. 

```powershell
$agreementTerms=Get-AzMarketplaceterms -Publisher "microsoft-ads" -Product "windows-data-science-vm" -Name "windows2016"

Set-AzMarketplaceTerms -Publisher "microsoft-ads" -Product "windows-data-science-vm" -Name "windows2016" -Terms $agreementTerms -Accept
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

Po zaakceptowaniu warunków dla obrazu, możesz wdrożyć Maszynę wirtualną w tej subskrypcji. Jak pokazano w poniższym fragmencie kodu, należy użyć [AzVMPlan zestaw](https://docs.microsoft.com/powershell/module/az.compute/set-azvmplan) polecenia cmdlet można ustawić informacji o planie witryny Marketplace dla obiektu maszyny Wirtualnej. Aby uzyskać kompletny skrypt utworzyć ustawienia sieci dla maszyny Wirtualnej i ukończyć wdrażanie, zobacz [przykłady skryptów programu PowerShell](powershell-samples.md).

```powershell
...

$vmConfig = New-AzVMConfig -VMName "myVM" -VMSize Standard_D1

# Set the Marketplace plan information

$publisherName = "microsoft-ads"

$productName = "windows-data-science-vm"

$planName = "windows2016"

$vmConfig = Set-AzVMPlan -VM $vmConfig -Publisher $publisherName -Product $productName -Name $planName

$cred=Get-Credential

$vmConfig = Set-AzVMOperatingSystem -Windows -VM $vmConfig -ComputerName "myVM" -Credential $cred

# Set the Marketplace image

$offerName = "windows-data-science-vm"

$skuName = "windows2016"

$version = "19.01.14"

$vmConfig = Set-AzVMSourceImage -VM $vmConfig -PublisherName $publisherName -Offer $offerName -Skus $skuName -Version $version
...
```
Będzie następnie przekazać konfiguracji maszyny Wirtualnej wraz z obiektami konfiguracji sieci, aby `New-AzVM` polecenia cmdlet.

## <a name="next-steps"></a>Kolejne kroki

Aby utworzyć maszynę wirtualną, szybko `New-AzVM` polecenia cmdlet przy użyciu obrazu podstawowego informacji, zobacz [Utwórz maszynę wirtualną Windows przy użyciu programu PowerShell](quick-create-powershell.md).


Przykładowy skrypt programu PowerShell, aby zobaczyć [tworzenie w pełni skonfigurowanej maszyny wirtualnej](../scripts/virtual-machines-windows-powershell-sample-create-vm.md).


