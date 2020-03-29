---
title: Tworzenie zestawu skalowania na platformie Azure za pomocą udostępnionych obrazów maszyn wirtualnych
description: Dowiedz się, jak używać interfejsu wiersza polecenia platformy Azure do tworzenia udostępnionych obrazów maszyn wirtualnych do użycia do wdrażania zestawów skalowania maszyn wirtualnych na platformie Azure.
author: axayjo
tags: azure-resource-manager
ms.service: virtual-machine-scale-sets
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: akjosh
ms.reviewer: cynthn
ms.custom: ''
ms.openlocfilehash: 83b3d5c904a65b28482acf8b685c939493c8c03b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76276263"
---
# <a name="create-and-use-shared-images-for-virtual-machine-scale-sets-with-the-azure-cli-20"></a>Tworzenie i używanie udostępnionych obrazów dla zestawów skalowania maszyn wirtualnych za pomocą interfejsu wiersza polecenia platformy Azure 2.0

Podczas tworzenia zestawu skalowania należy wskazać obraz używany do wdrożenia wystąpień maszyn wirtualnych. [Udostępnione galerie obrazów](shared-image-galleries.md) znacznie upraszczają niestandardowe udostępnianie obrazów w całej organizacji. Obrazy niestandardowe są podobne do obrazów z platformy handlowej, ale tworzy się je samodzielnie. Obrazy niestandardowe mogą służyć do ładowania początkowego konfiguracji, na przykład do wstępnego ładowania aplikacji, konfiguracji aplikacji i innych konfiguracji systemu operacyjnego. Galeria obrazów udostępnionych umożliwia udostępnianie niestandardowych obrazów maszyn wirtualnych innym osobom w organizacji, w obrębie lub między regionami, w dzierżawie usługi AAD. Wybierz obrazy, które chcesz udostępnić, regiony, w których chcesz je udostępnić i komu chcesz je udostępnić. Można utworzyć wiele galerii, aby logicznie grupować udostępnione obrazy. Galeria jest zasobem najwyższego poziomu, który zapewnia pełną kontrolę dostępu opartą na rolach (RBAC). Obrazy mogą być wersjona i można wybrać do replikacji każdej wersji obrazu do innego zestawu regionów platformy Azure. Galeria działa tylko z zarządzanymi obrazami. 

>[!NOTE]
> W tym artykule otwór na temat procesu korzystania z uogólnionego obrazu zarządzanego. Tworzenie zestawu skalowania na podstawie obrazu wyspecjalizowanej maszyny wirtualnej nie jest obsługiwane.


[!INCLUDE [virtual-machines-common-shared-images-cli](../../includes/virtual-machines-common-shared-images-cli.md)]

## <a name="create-a-scale-set-from-the-custom-vm-image"></a>Tworzenie zestawu skalowania z niestandardowego obrazu maszyny wirtualnej
Utwórz zestaw [`az vmss create`](/cli/azure/vmss#az-vmss-create)skalowania za pomocą pliku . Zamiast obrazu platformy, takiego jak *UbuntuLTS* lub *CentOS*, podaj nazwę niestandardowego obrazu maszyny wirtualnej. W poniższym przykładzie pokazano tworzenie zestawu skalowania o nazwie *myScaleSet*, używającego niestandardowego obrazu o nazwie *myImage* utworzonego w poprzednim kroku:

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
Aby usunąć zestaw skalowania i dodatkowe zasoby, usuń grupę zasobów i wszystkie jej zasoby za pomocą [az group delete](/cli/azure/group). Parametr `--no-wait` zwraca kontrolę do wiersza polecenia bez oczekiwania na zakończenie operacji. Parametr `--yes` potwierdza, że chcesz usunąć zasoby bez wyświetlania dodatkowego monitu.

```azurecli-interactive
az group delete --name myResourceGroup --no-wait --yes
```


## <a name="next-steps"></a>Następne kroki

Zasób Shared Image Gallery można również utworzyć przy użyciu szablonów. Dostępnych jest kilka szablonów szybki start platformy Azure: 

- [Tworzenie galerii obrazów udostępnionych](https://azure.microsoft.com/resources/templates/101-sig-create/)
- [Tworzenie definicji obrazu w galerii obrazów udostępnionych](https://azure.microsoft.com/resources/templates/101-sig-image-definition-create/)
- [Tworzenie wersji obrazu w galerii obrazów udostępnionych](https://azure.microsoft.com/resources/templates/101-sig-image-version-create/)
- [Tworzenie maszyny Wirtualnej z wersji obrazu](https://azure.microsoft.com/resources/templates/101-vm-from-sig/)


Jeśli napotkasz jakiekolwiek problemy, możesz [rozwiązać problem z udostępnionymi galeriami obrazów](troubleshooting-shared-images.md).
