---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 04/25/2019
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: 82187b05a398c066f9da94c57cbe8a59a6ba3275
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/06/2019
ms.locfileid: "65191707"
---
## <a name="launch-azure-cloud-shell"></a>Uruchamianie usługi Azure Cloud Shell

Usługa Azure Cloud Shell to bezpłatna interaktywna powłoka, której możesz używać do wykonywania kroków opisanych w tym artykule. Udostępnia ona wstępnie zainstalowane i najczęściej używane narzędzia platformy Azure, które są skonfigurowane do użycia na koncie. 

Aby otworzyć usługę Cloud Shell, wybierz pozycję **Wypróbuj** w prawym górnym rogu bloku kodu. Możesz również uruchomić usługę Cloud Shell w oddzielnej karcie przeglądarki, przechodząc do strony [https://shell.azure.com/powershell](https://shell.azure.com/powershell). Wybierz przycisk **Kopiuj**, aby skopiować bloki kodu, wklej je do usługi Cloud Shell, a następnie naciśnij klawisz Enter, aby je uruchomić.


## <a name="get-the-managed-image"></a>Pobieranie obrazu zarządzanego

Można wyświetlić listę obrazów, które są dostępne w grupie zasobów przy użyciu [Get AzImage](https://docs.microsoft.com/powershell/module/az.compute/get-azimage). Jeśli znasz już nazwę obrazu i grupie zasobów, które znajduje się w, można użyć `Get-AzImage` ponownie, aby pobrać obiekt obrazu i zapisz go w zmiennej, do użycia w przyszłości. W tym przykładzie pobiera obraz o nazwie *myImage* z grupy zasobów "myResourceGroup", a następnie przypisuje go do zmiennej *$managedImage*. 

```azurepowershell-interactive
$managedImage = Get-AzImage `
   -ImageName myImage `
   -ResourceGroupName myResourceGroup
```

## <a name="create-an-image-gallery"></a>Tworzenie galerii obrazów 

Galeria obrazów jest podstawowy zasób umożliwiający włączenie udostępniania obrazów. Dozwolone znaki dla nazwy galerii są wielkie i małe litery, cyfry, kropki i okresów. Nazwa galerii nie może zawierać łączników. Galeria nazwy muszą być unikatowe w ramach Twojej subskrypcji. 

Tworzenie galerii obrazów przy użyciu [New AzGallery](https://docs.microsoft.com/powershell/module/az.compute/new-azgallery). Poniższy przykład tworzy Galeria o nazwie *myGallery* w *myGalleryRG* grupy zasobów.

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

Definicje obraz Utwórz logiczne grupowanie obrazów. Są one używane do zarządzania informacjami o wersji obrazu, które są tworzone w ramach ich. Obraz nazwy definicji może składać się z małe i wielkie litery, cyfry, kropki, łączniki i kropki. Aby uzyskać więcej informacji na temat wartości można określić definicję obrazu, zobacz [obrazu definicje](https://docs.microsoft.com/azure/virtual-machines/windows/shared-image-galleries#image-definitions).

Utworzyć definicję obrazu przy użyciu [New AzGalleryImageDefinition](https://docs.microsoft.com/powershell/module/az.compute/new-azgalleryimageversion). W tym przykładzie nosi nazwę obrazu z galerii *myGalleryImage*.

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


## <a name="create-an-image-version"></a>Utwórz wersję obrazu

Utwórz wersję obrazu na podstawie obrazu zarządzanego przy użyciu [New AzGalleryImageVersion](https://docs.microsoft.com/powershell/module/az.compute/new-azgalleryimageversion). 

Dozwolone znaki wersję obrazu są liczby i kropki. Numery muszą należeć do zakresu 32-bitową liczbę całkowitą. Format: *Brak elementu MajorVersion*. *MinorVersion*. *Poprawka*.

W tym przykładzie jest wersja obrazu *1.0.0* i są replikowane do obu *zachodnio-środkowe stany USA* i *południowo-środkowe stany USA* centrów danych. Wybierając regiony docelowe dla replikacji, należy pamiętać, że również należy dołączyć *źródła* regionie co miejsce docelowe dla replikacji.


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

> [!NOTE]
> Musisz czekać na wersję obrazu do całkowitego zakończenia są zbudowane i replikowane korzystać z tego samego obrazu zarządzanego, aby utworzyć inną wersję obrazu. 
>
> Można również przechowywać swoje wersję obrazu w [magazyn Strefowo nadmiarowy](https://docs.microsoft.com/azure/storage/common/storage-redundancy-zrs) , dodając `-StorageAccountType Standard_ZRS` po utworzeniu wersję obrazu.
>
