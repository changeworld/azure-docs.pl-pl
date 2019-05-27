---
title: Użyj udostępnionego obrazu maszyny Wirtualnej w celu utworzenia zestawu skalowania w usłudze Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak używać interfejsu wiersza polecenia platformy Azure do tworzenia udostępnionych obrazów maszyn wirtualnych będzie używana do wdrażania zestawów skalowania maszyn wirtualnych na platformie Azure.
services: virtual-machine-scale-sets
documentationcenter: ''
author: axayjo
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/06/2019
ms.author: akjosh; cynthn
ms.custom: ''
ms.openlocfilehash: c002e7c107c4dcbcd7eeff9579fae6893483392e
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/06/2019
ms.locfileid: "66156162"
---
# <a name="create-and-use-shared-images-for-virtual-machine-scale-sets-with-the-azure-cli-20"></a>Tworzenie i używanie udostępnianych obrazów dla zestawów skalowania maszyn wirtualnych przy użyciu interfejsu wiersza polecenia platformy Azure w wersji 2.0

Podczas tworzenia zestawu skalowania należy wskazać obraz używany do wdrożenia wystąpień maszyn wirtualnych. [Udostępnione galerie obrazów](shared-image-galleries.md) znacząco upraszcza obraz niestandardowy do udostępniania całej organizacji. Obrazy niestandardowe są podobne do obrazów z platformy handlowej, ale tworzy się je samodzielnie. Obrazy niestandardowe mogą służyć do ładowania początkowego konfiguracji, na przykład do wstępnego ładowania aplikacji, konfiguracji aplikacji i innych konfiguracji systemu operacyjnego. Galeria obrazów współdzielona umożliwia udostępnianie niestandardowych obrazów maszyn wirtualnych z innymi osobami w organizacji, w ramach lub w wielu regionach, w ramach dzierżawy usługi AAD. Wybierz obrazy, które chcesz udostępnić, regiony mają być dostępne w nich i, którym chcesz udostępnić je za pomocą. Możesz utworzyć wiele galerii, dzięki czemu można grupować logicznie udostępnianych obrazów. Galeria jest zasobem najwyższego poziomu, który zapewnia kontrolę dostępu pełnej opartej na rolach (RBAC). Obrazy mogą być poddany kontroli wersji, a użytkownik może replikować każdej wersji obrazu z innym zestawem regiony platformy Azure. Galeria działa tylko z zarządzanych obrazów. 

>[!NOTE]
> W tym artykule opisano proces przy użyciu uogólnionego obrazu zarządzanego. Tworzenie zestawu skalowania na podstawie obrazu wyspecjalizowanej maszyny wirtualnej nie jest obsługiwane.


[!INCLUDE [virtual-machines-common-shared-images-cli](../../includes/virtual-machines-common-shared-images-cli.md)]

## <a name="create-a-scale-set-from-the-custom-vm-image"></a>Tworzenie zestawu skalowania z niestandardowego obrazu maszyny wirtualnej
Tworzenie zestawu skalowania z [ `az vmss create` ](/cli/azure/vmss#az-vmss-create). Zamiast obrazu platformy, takiego jak *UbuntuLTS* lub *CentOS*, podaj nazwę niestandardowego obrazu maszyny wirtualnej. W poniższym przykładzie pokazano tworzenie zestawu skalowania o nazwie *myScaleSet*, używającego niestandardowego obrazu o nazwie *myImage* utworzonego w poprzednim kroku:

```azurecli-interactive
az vmss create \
  -g myGalleryRG \
  -n myScaleSet \
  --image "/subscriptions/<subscription ID>/resourceGroups/myGalleryRG/providers/Microsoft.Compute/galleries/myGallery/images/myImageDefinition/versions/1.0.0" \
  --admin-username azureuser \
  --generate-ssh-keys
```

Utworzenie i skonfigurowanie wszystkich zasobów zestawu skalowania i maszyn wirtualnych trwa kilka minut.


[!INCLUDE [virtual-machines-common-gallery-list-cli](../../includes/virtual-machines-common-gallery-list-cli.md)]


## <a name="clean-up-resources"></a>Oczyszczanie zasobów
Aby pozbyć się zestawu skalowania i dodatkowych zasobów, usuń grupę zasobów wraz z całą zawartością za pomocą polecenia [az group delete](/cli/azure/group). Parametr `--no-wait` zwraca kontrolę do wiersza polecenia bez oczekiwania na zakończenie operacji. Parametr `--yes` potwierdza, że chcesz usunąć zasoby bez wyświetlania dodatkowego monitu.

```azurecli-interactive
az group delete --name myResourceGroup --no-wait --yes
```


## <a name="next-steps"></a>Kolejne kroki

Można również utworzyć zasób galerii obrazów udostępnione za pomocą szablonów. Dostępnych jest kilka szablonów szybkiego startu platformy Azure: 

- [Tworzenie udostępnionego galerii obrazów](https://azure.microsoft.com/resources/templates/101-sig-create/)
- [Utwórz definicję obrazu w udostępnionym galerii obrazów](https://azure.microsoft.com/resources/templates/101-sig-image-definition-create/)
- [Utwórz wersję obrazu w udostępnionym galerii obrazów](https://azure.microsoft.com/resources/templates/101-sig-image-version-create/)
- [Utwórz Maszynę wirtualną z obrazu wersji](https://azure.microsoft.com/resources/templates/101-vm-from-sig/)


Jeśli napotkasz problemy, możesz to zrobić [Rozwiązywanie problemów z galerii obrazów udostępnionych](troubleshooting-shared-images.md).
