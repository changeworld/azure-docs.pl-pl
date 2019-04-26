---
title: Tworzenie udostępnionego obrazów maszyn wirtualnych przy użyciu programu Azure PowerShell | Dokumentacja firmy Microsoft
description: Dowiedz się, jak utworzyć obraz udostępnionej maszyny wirtualnej na platformie Azure przy użyciu programu Azure PowerShell
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 12/11/2018
ms.author: cynthn
ms.custom: ''
ms.openlocfilehash: 6ea98792e6a1b7fef32e3be1ddfbb29743f50c6a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60250756"
---
# <a name="preview-create-a-shared-image-gallery-with-azure-powershell"></a>Wersja zapoznawcza: Utwórz galerię udostępnionego obrazu przy użyciu programu Azure PowerShell 

A [galerii obrazów współdzielona](shared-image-galleries.md) upraszcza obraz niestandardowy do udostępniania całej organizacji. Obrazy niestandardowe są podobne do obrazów z platformy handlowej, ale tworzy się je samodzielnie. Obrazy niestandardowe może służyć do uruchamiania wdrożenia zadania, takie jak wstępnego ładowania aplikacji, konfiguracje aplikacji i innych konfiguracji systemu operacyjnego. 

Galeria obrazów współdzielona umożliwia udostępnianie niestandardowych obrazów maszyn wirtualnych z innymi osobami w organizacji, w ramach lub w wielu regionach, w ramach dzierżawy usługi AAD. Wybierz obrazy, które chcesz udostępnić, regiony mają być dostępne w nich i, którym chcesz udostępnić je za pomocą. Możesz utworzyć wiele galerii, dzięki czemu można grupować logicznie udostępnianych obrazów. 

Galeria jest zasobem najwyższego poziomu, który zapewnia kontrolę dostępu pełnej opartej na rolach (RBAC). Obrazy mogą być poddany kontroli wersji, a użytkownik może replikować każdej wersji obrazu z innym zestawem regiony platformy Azure. Galeria działa tylko z zarządzanych obrazów.

Funkcja galerii obrazów współdzielona ma wiele typów zasobów. Firma Microsoft będzie przy użyciu lub kompilowania w tym artykule:

| Resource | Opis|
|----------|------------|
| **Zarządzany obraz** | Jest to podstawowy obraz, które mogą być używane autonomicznie lub użyty do utworzenia **wersję obrazu** w galerii obrazów. Obrazy zarządzane są tworzone na podstawie uogólnionego maszyn wirtualnych. Zarządzany obraz to specjalny typ wirtualnego dysku twardego, który można utworzyć wiele maszyn wirtualnych i może teraz służyć do tworzenia wersji udostępnionego obrazu. |
| **Galeria obrazów** | W portalu Azure Marketplace, takich jak **galerii obrazów** to repozytorium do zarządzania i udostępniania obrazów, ale możesz kontrolować, kto ma dostęp. |
| **Definicję obrazu** | Obrazy w galerii są zdefiniowane i zawierają informacje dotyczące obrazu i wymagania dotyczące korzystania z niego wewnętrznie. Dotyczy to również, czy obraz jest Windows lub Linux, informacje o wersji i wymagań dotyczących minimalnej i maksymalnej pamięci. Jest definicja typu obrazu. |
| **Wersja obrazu** | **Wersję obrazu** , które jest używane do utworzenia maszyny Wirtualnej, podczas korzystania z galerii. Może mieć wiele wersji obrazu, zgodnie z potrzebami w danym środowisku. Gdy używasz, takie jak zarządzany obraz **wersję obrazu** tworzenie maszyny Wirtualnej, wersja obrazu jest używany do tworzenia nowych dysków dla maszyny Wirtualnej. Wersje obrazów można wielokrotnie. |

[!INCLUDE [updated-for-az.md](../../../includes/updated-for-az.md)]

## <a name="before-you-begin"></a>Przed rozpoczęciem

Aby ukończyć przykład, w tym artykule, konieczne jest posiadanie istniejącego obrazu zarządzanego. Możesz wykonać [samouczka: Tworzenie niestandardowego obrazu maszyny wirtualnej portalu Azure przy użyciu programu Azure PowerShell](tutorial-custom-images.md) aby je utworzyć, jeśli to konieczne. Podczas pracy nad tym artykule, Zamień grupę zasobów i maszynę Wirtualną przypadkach nazwy.

[!INCLUDE [virtual-machines-common-shared-images-ps](../../../includes/virtual-machines-common-shared-images-powershell.md)]
 
## <a name="create-vms-from-an-image"></a>Tworzenie maszyn wirtualnych z obrazu

Po zakończeniu wersję obrazu, można utworzyć jeden lub więcej nowych maszyn wirtualnych. Za pomocą parametru uproszczonego zestawu dla [New-AzVM](https://docs.microsoft.com/powershell/module/az.compute/new-azvm) polecenia cmdlet, wystarczy podać identyfikator obrazu wersję obrazu. 

W tym przykładzie utworzono maszynę Wirtualną o nazwie *myVMfromImage*w *myResourceGroup* w *wschodnie stany USA* centrum danych.

```azurepowershell-interactive
New-AzVm `
   -ResourceGroupName "myResourceGroup" `
   -Name "myVMfromImage" `
   -Image $imageVersion.Id `
   -Location "South Central US" `
   -VirtualNetworkName "myImageVnet" `
   -SubnetName "myImageSubnet" `
   -SecurityGroupName "myImageNSG" `
   -PublicIpAddressName "myImagePIP" `
   -OpenPorts 3389
```

[!INCLUDE [virtual-machines-common-gallery-list-ps](../../../includes/virtual-machines-common-gallery-list-ps.md)]

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Gdy grupa zasobów, maszyna wirtualna i wszystkie pokrewne zasoby nie będą już potrzebne, można je usunąć za pomocą polecenia cmdlet [Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup):

```azurepowershell-interactive
Remove-AzResourceGroup -Name myGalleryRG
```

## <a name="next-steps"></a>Kolejne kroki

Można również utworzyć zasób galerii obrazów udostępnione za pomocą szablonów. Dostępnych jest kilka szablonów szybkiego startu platformy Azure: 

- [Tworzenie udostępnionego galerii obrazów](https://azure.microsoft.com/resources/templates/101-sig-create/)
- [Utwórz definicję obrazu w udostępnionym galerii obrazów](https://azure.microsoft.com/resources/templates/101-sig-image-definition-create/)
- [Utwórz wersję obrazu w udostępnionym galerii obrazów](https://azure.microsoft.com/resources/templates/101-sig-image-version-create/)
- [Utwórz Maszynę wirtualną z obrazu wersji](https://azure.microsoft.com/resources/templates/101-vm-from-sig/)

Aby uzyskać więcej informacji na temat udostępnione, galerie obrazów zobacz [Przegląd](shared-image-galleries.md). Jeśli napotkasz problemy, zobacz [Rozwiązywanie problemów udostępnione galerie obrazów](troubleshooting-shared-images.md).

