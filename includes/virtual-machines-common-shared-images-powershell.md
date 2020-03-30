---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 05/21/2019
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: bae66078a1bcb1d80f0798b1d501598fa785fb80
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "66241195"
---
## <a name="launch-azure-cloud-shell"></a>Uruchamianie usługi Azure Cloud Shell

Usługa Azure Cloud Shell to bezpłatna interaktywna powłoka, której możesz używać do wykonywania kroków opisanych w tym artykule. Udostępnia ona wstępnie zainstalowane i najczęściej używane narzędzia platformy Azure, które są skonfigurowane do użycia na koncie. 

Aby otworzyć usługę Cloud Shell, wybierz pozycję **Wypróbuj** w prawym górnym rogu bloku kodu. Możesz również uruchomić usługę Cloud Shell w [https://shell.azure.com/powershell](https://shell.azure.com/powershell)osobnej karcie przeglądarki, przechodząc do . Wybierz przycisk **Kopiuj**, aby skopiować bloki kodu, wklej je do usługi Cloud Shell, a następnie naciśnij klawisz Enter, aby je uruchomić.


## <a name="get-the-managed-image"></a>Pobierz obraz zarządzany

Listę obrazów dostępnych w grupie zasobów można wyświetlić za pomocą programu [Get-AzImage](https://docs.microsoft.com/powershell/module/az.compute/get-azimage). Po poznaniu nazwy obrazu i grupy zasobów, w `Get-AzImage` której się znajduje, można użyć ponownie, aby uzyskać obiekt obrazu i zapisać go w zmiennej do użycia później. W tym przykładzie pobiera obraz o nazwie *myImage* z grupy zasobów "myResourceGroup" i przypisuje go do zmiennej *$managedImage*. 

```azurepowershell-interactive
$managedImage = Get-AzImage `
   -ImageName myImage `
   -ResourceGroupName myResourceGroup
```

## <a name="create-an-image-gallery"></a>Tworzenie galerii obrazów 

Galeria obrazów jest podstawowym zasobem używanym do włączania udostępniania obrazów. Dozwolone znaki nazwy galerii to wielkie lub małe litery, cyfry, kropki i kropki. Nazwa galerii nie może zawierać kresek. Nazwy galerii muszą być unikatowe w ramach subskrypcji. 

Utwórz galerię obrazów przy użyciu [programu New-AzGallery](https://docs.microsoft.com/powershell/module/az.compute/new-azgallery). Poniższy przykład tworzy galerię o nazwie *myGallery* w grupie zasobów *myGalleryRG.*

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
   
## <a name="create-an-image-definition"></a>Tworzenie definicji obrazu 

Definicje obrazów tworzą logiczne grupowanie obrazów. Są one używane do zarządzania informacjami o wersjach obrazów, które są tworzone w nich. Nazwy definicji obrazów mogą być składane z wielkich lub małych liter, cyfr, kropek, kresek i kropek. Aby uzyskać więcej informacji na temat wartości, które można określić dla definicji obrazu, zobacz [Definicje obrazów](https://docs.microsoft.com/azure/virtual-machines/windows/shared-image-galleries#image-definitions).

Utwórz definicję obrazu przy użyciu [funkcji New-AzGalleryImageDefinition](https://docs.microsoft.com/powershell/module/az.compute/new-azgalleryimageversion). W tym przykładzie obraz galerii nosi nazwę *myGalleryImage*.

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


## <a name="create-an-image-version"></a>Tworzenie wersji obrazu

Utwórz wersję obrazu z zarządzanego obrazu za pomocą [programu New-AzGalleryImageVersion](https://docs.microsoft.com/powershell/module/az.compute/new-azgalleryimageversion). 

Dozwolone znaki dla wersji obrazu to liczby i kropki. Liczby muszą mieszczeć się w zakresie 32-bitowej liczby całkowitej. Format: *MajorVersion*. *MinorVersion*. *Poprawka*.

W tym przykładzie wersja obrazu jest *1.0.0* i jest replikowana zarówno do *centrów* danych West Central US i *Południowo-Środkowej USA.* Wybierając regiony docelowe dla replikacji, należy pamiętać, że należy również uwzględnić region *źródłowy* jako miejsce docelowe replikacji.


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

Może upłynąć trochę czasu, aby zreplikować obraz do wszystkich regionów docelowych, więc utworzyliśmy zadanie, dzięki czemu możemy śledzić postęp. Aby wyświetlić postęp zadania, `$job.State`wpisz .

```azurepowershell-interactive
$job.State
```

> [!NOTE]
> Musisz poczekać, aż wersja obrazu całkowicie zakończyć budowa i replikowanie, zanim będzie można użyć tego samego obrazu zarządzanego do utworzenia innej wersji obrazu. 
>
> Wersję obrazu można również zapisać w magazynie `-StorageAccountType Standard_ZRS` [strefowym nadmiarowym,](https://docs.microsoft.com/azure/storage/common/storage-redundancy-zrs) dodając ją podczas tworzenia wersji obrazu.
>


## <a name="share-the-gallery"></a>Udostępnianie galerii

Zalecamy udostępnianie dostępu na poziomie galerii obrazów. Użyj adresu e-mail i polecenia cmdlet [Get-AzADUser,](/powershell/module/az.resources/get-azaduser) aby uzyskać identyfikator obiektu dla użytkownika, a następnie użyj [new-AzRoleAssignment,](/powershell/module/Az.Resources/New-AzRoleAssignment) aby dać im dostęp do galerii. Zastąp alinne_montes@contoso.com przykładowy adres e-mail w tym przykładzie własnymi informacjami.

```azurepowershell-interactive
# Get the object ID for the user
$user = Get-AzADUser -StartsWith alinne_montes@contoso.com
# Grant access to the user for our gallery
New-AzRoleAssignment `
   -ObjectId $user.Id `
   -RoleDefinitionName Reader `
   -ResourceName $gallery.Name `
   -ResourceType Microsoft.Compute/galleries `
   -ResourceGroupName $resourceGroup.ResourceGroupName
```