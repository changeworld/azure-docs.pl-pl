---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 12/10/2018
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: 8770aaeff3e0d7b2d6a39f596aafebf15ed48b23
ms.sourcegitcommit: 943af92555ba640288464c11d84e01da948db5c0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/09/2019
ms.locfileid: "55985003"
---
## <a name="launch-azure-cloud-shell"></a>Uruchamianie usługi Azure Cloud Shell

Usługa Azure Cloud Shell to bezpłatna interaktywna powłoka, której możesz używać do wykonywania kroków opisanych w tym artykule. Udostępnia ona wstępnie zainstalowane i najczęściej używane narzędzia platformy Azure, które są skonfigurowane do użycia na koncie. 

Aby otworzyć usługę Cloud Shell, wybierz pozycję **Wypróbuj** w prawym górnym rogu bloku kodu. Możesz również uruchomić usługę Cloud Shell w oddzielnej karcie przeglądarki, przechodząc do strony [https://shell.azure.com/powershell](https://shell.azure.com/powershell). Wybierz przycisk **Kopiuj**, aby skopiować bloki kodu, wklej je do usługi Cloud Shell, a następnie naciśnij klawisz Enter, aby je uruchomić.


## <a name="preview-register-the-feature"></a>Wersja zapoznawcza: Zarejestruj funkcję

Udostępnione galerie obrazów jest w wersji zapoznawczej, ale trzeba zarejestrować tę funkcję, przed jego użyciem. Aby zarejestrować funkcji udostępnione, galerie obrazów:

```azurepowershell-interactive
Register-AzProviderFeature `
   -FeatureName GalleryPreview `
   -ProviderNamespace Microsoft.Compute
Register-AzResourceProvider -ProviderNamespace Microsoft.Compute
```

## <a name="get-the-managed-image"></a>Pobieranie obrazu zarządzanego

Można wyświetlić listę obrazów, które są dostępne w grupie zasobów przy użyciu [Get AzImage](https://docs.microsoft.com/powershell/module/az.compute/get-azimage). Jeśli znasz już nazwę obrazu i grupie zasobów, które znajduje się w, można użyć `Get-AzImage` ponownie, aby pobrać obiekt obrazu i zapisz go w zmiennej, do użycia w przyszłości. W tym przykładzie pobiera obraz o nazwie *myImage* z grupy zasobów "myResourceGroup", a następnie przypisuje go do zmiennej *$managedImage*. 

```azurepowershell-interactive
$managedImage = Get-AzImage `
   -ImageName myImage `
   -ResourceGroupName myResourceGroup
```

## <a name="create-an-image-gallery"></a>Tworzenie galerii obrazów 

Galeria obrazów jest podstawowy zasób umożliwiający włączenie udostępniania obrazów. Galeria nazwy muszą być unikatowe w ramach Twojej subskrypcji. Tworzenie galerii obrazów przy użyciu [New AzGallery](https://docs.microsoft.com/powershell/module/az.compute/new-azgallery). Poniższy przykład tworzy Galeria o nazwie *myGallery* w *myGalleryRG* grupy zasobów.

```azurepowershell-interactive
$resourceGroup = New-AzResourceGroup `
   -Name 'myGalleryRG' `
   -Location 'West Central US'  
$gallery = New-AzGallery `
   -GalleryName 'myGallery' `
   -ResourceGroupName $resourceGroup.ResourceGroupName `
   -Location $resourceGroup.Location `
   -Description 'Shared Image Gallery for my organization'  
```
   
## <a name="create-an-image-definition"></a>Utwórz definicję obrazu 

Tworzenie galerii obrazów definicji przy użyciu [New AzGalleryImageDefinition](https://docs.microsoft.com/powershell/module/az.compute/new-azgalleryimageversion). W tym przykładzie nosi nazwę obrazu z galerii *myGalleryImage*.

```azurepowershell-interactive
$galleryImage = New-AzGalleryImageDefinition `
   -GalleryName $gallery.Name `
   -ResourceGroupName $resourceGroup.ResourceGroupName `
   -Location $gallery.Location `
   -Name 'myImageDefinition' `
   -OsState generalized `
   -OsType Windows `
   -Publisher 'myPublisher' `
   -Offer 'myOffer' `
   -Sku 'mySKU'
```

W kolejnej wersji, będziesz mieć możliwość użycia osobiście zdefiniowanych **-wydawcy**, **-oferują** i **- Sku** wartości, aby znaleźć i określić definicję obrazu, a następnie utwórz Maszynę wirtualną za pomocą najnowszej wersji obrazu z pasujących definicji obrazu. Na przykład poniżej przedstawiono trzy definicje, które obrazu i ich wartości:

|Definicja obrazu|Wydawca|Oferta|SKU|
|---|---|---|---|
|myImage1|myPublisher|myOffer|mySku|
|myImage2|myPublisher|standardOffer|mySku|
|myImage3|Testowanie|standardOffer|testSku|

Wszystkie trzy z nich ma unikatowe zbiory wartości. W kolejnej wersji można połączyć te wartości w celu żądania najnowszą wersję określonego obrazu. 

```powershell
# The following should set the source image as myImage1 from the table above
$vmConfig = Set-AzVMSourceImage `
   -VM $vmConfig `
   -PublisherName myPublisher `
   -Offer myOffer `
   -Skus mySku 
```

Jest to podobne do jak obecnie określić je pod kątem [obrazów portalu Azure Marketplace](../articles/virtual-machines/windows/cli-ps-findimage.md) utworzyć maszynę Wirtualną. Pamiętając o tym każda definicja obrazu musi mieć unikatowy zestaw tych wartości. Może mieć wersji obrazu, które współużytkują jeden lub dwa, ale nie wszystkie trzy wartości. 

##<a name="create-an-image-version"></a>Utwórz wersję obrazu

Utwórz wersję obrazu na podstawie obrazu zarządzanego przy użyciu [New AzGalleryImageVersion](https://docs.microsoft.com/powershell/module/az.compute/new-azgalleryimageversion) . W tym przykładzie jest wersja obrazu *1.0.0* i są replikowane do obu *zachodnio-środkowe stany USA* i *południowo-środkowe stany USA* centrów danych.


```azurepowershell-interactive
$region1 = @{Name='South Central US';ReplicaCount=1}
$region2 = @{Name='West Central US';ReplicaCount=2}
$targetRegions = @($region1,$region2)
$job = $imageVersion = New-AzGalleryImageVersion `
   -GalleryImageDefinitionName $galleryImage.Name `
   -GalleryImageVersionName '1.0.0' `
   -GalleryName $gallery.Name `
   -ResourceGroupName $resourceGroup.ResourceGroupName `
   -Location $resourceGroup.Location `
   -TargetRegion $targetRegions  `
   -Source $managedImage.Id.ToString() `
   -PublishingProfileEndOfLifeDate '2020-01-01' `
   -asJob 
```

Replikować obraz do wszystkich regionów docelowych, dlatego utworzyliśmy zadania, dzięki czemu firma Microsoft może śledzić postęp może potrwać. Aby wyświetlić postęp zadania, wpisz `$job.State`.

```azurepowershell-interactive
$job.State
```

