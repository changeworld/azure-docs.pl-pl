---
title: Aktualizowanie kontenerów w Azure Container Instances
description: Dowiedz się, jak aktualizować uruchomione kontenery w grupach kontenerów Azure Container Instances.
services: container-instances
author: dlepow
manager: gwallace
ms.service: container-instances
ms.topic: article
ms.date: 08/01/2018
ms.author: danlep
ms.openlocfilehash: d555ba6b8c2b32fc6ec56d6c51dda9626b6f0cb0
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/18/2019
ms.locfileid: "68325543"
---
# <a name="update-containers-in-azure-container-instances"></a>Aktualizowanie kontenerów w Azure Container Instances

Podczas normalnego działania wystąpień kontenerów może być konieczne zaktualizowanie kontenerów w grupie kontenerów. Na przykład możesz chcieć zaktualizować wersję obrazu, zmienić nazwę DNS, zaktualizować zmienne środowiskowe lub odświeżyć stan kontenera, którego aplikacja uległa awarii.

## <a name="update-a-container-group"></a>Aktualizowanie grupy kontenerów

Aktualizowanie kontenerów w grupie kontenerów przez ponowne wdrożenie istniejącej grupy z co najmniej jedną zmodyfikowaną właściwością. Podczas aktualizowania grupy kontenerów wszystkie uruchomione kontenery w grupie są ponownie uruchamiane w miejscu.

Wdróż ponownie istniejącą grupę kontenerów, wydając polecenie Create (lub użyj Azure Portal) i określ nazwę istniejącej grupy. Należy zmodyfikować co najmniej jedną prawidłową Właściwość grupy po wydaniu polecenia CREATE w celu wyzwolenia ponownego wdrożenia. Nie wszystkie właściwości grupy kontenerów są prawidłowe dla ponownego wdrożenia. Zobacz [właściwości, które wymagają usunięcia](#properties-that-require-container-delete) , aby wyświetlić listę nieobsługiwanych właściwości.

Poniższy przykład interfejsu wiersza polecenia platformy Azure aktualizuje grupę kontenerów za pomocą nowej etykiety nazwy DNS. Ze względu na to, że właściwość etykieta nazwy DNS grupy zostanie zmodyfikowana, Grupa kontenerów zostanie ponownie wdrożona, a jej kontenery zostaną uruchomione.

Początkowe wdrożenie z etykietą nazw DNS moja *aplikacja — przemieszczanie*:

```azurecli-interactive
# Create container group
az container create --resource-group myResourceGroup --name mycontainer \
    --image nginx:alpine --dns-name-label myapplication-staging
```

Zaktualizuj grupę kontenerów za pomocą nowej etykiety nazwy DNS, moja *aplikacja*:

```azurecli-interactive
# Update container group (restarts container)
az container create --resource-group myResourceGroup --name mycontainer \
    --image nginx:alpine --dns-name-label myapplication
```

## <a name="update-benefits"></a>Korzyści z aktualizacji

Główną zaletą aktualizowania istniejącej grupy kontenerów jest szybsze wdrażanie. Po ponownym wdrożeniu istniejącej grupy kontenerów warstwy obrazu kontenera są ściągane z pamięci podręcznej w poprzednim wdrożeniu. Zamiast ściągania wszystkich warstw obrazu z rejestru w taki sam sposób, jak w przypadku nowych wdrożeń, pobierane są tylko zmodyfikowane warstwy (jeśli istnieją).

Aplikacje oparte na większych obrazach kontenerów, takich jak Windows Server Core, mogą mieć znaczący wpływ na szybkość wdrażania podczas aktualizacji, a nie do usuwania i wdrażania nowych.

## <a name="limitations"></a>Ograniczenia

Nie wszystkie właściwości grupy kontenerów obsługują aktualizacje. Aby zmienić niektóre właściwości grupy kontenerów, należy najpierw usunąć, a następnie wdrożyć ponownie grupę. Aby uzyskać szczegółowe informacje, zobacz [właściwości, które wymagają usunięcia kontenera](#properties-that-require-container-delete).

Wszystkie kontenery w grupie kontenerów są uruchamiane ponownie po zaktualizowaniu grupy kontenerów. Nie można wykonać aktualizacji ani ponownego uruchomienia w miejscu dla określonego kontenera w grupie wielokontenerowej.

Adres IP kontenera zwykle nie zmienia się między aktualizacjami, ale nie ma gwarancji, że pozostaje taki sam. Dopóki Grupa kontenerów zostanie wdrożona na tym samym hoście podstawowym, Grupa kontenerów zachowuje swój adres IP. Chociaż rzadko, a podczas Azure Container Instances każdy nakład pracy jest wdrażany na tym samym hoście, istnieją pewne zdarzenia wewnętrzne platformy Azure, które mogą spowodować ponowne wdrożenie na innym hoście. Aby wyeliminować ten problem, należy zawsze używać etykiety nazwy DNS dla wystąpień kontenera.

Nie można zaktualizować grup kontenerów zakończonych lub usuniętych. Po zatrzymaniu grupy kontenerów (jest ona w  stanie przerwania) lub została usunięta, Grupa zostanie wdrożona jako nowa.

## <a name="properties-that-require-container-delete"></a>Właściwości wymagające usunięcia kontenera

Jak wspomniano wcześniej, nie można zaktualizować wszystkich właściwości grupy kontenerów. Na przykład aby zmienić porty lub zasady ponownego uruchamiania kontenera, należy najpierw usunąć grupę kontenerów, a następnie utworzyć ją ponownie.

Te właściwości wymagają usunięcia grupy kontenerów przed ponownem wdrożeniem:

* Typ systemu operacyjnego
* Procesor CPU
* Memory (Pamięć)
* Zasady ponownego uruchamiania
* Porty

Gdy usuniesz grupę kontenerów i utworzysz ją ponownie, nie jest to "redeployed", ale utworzona nowa. Wszystkie warstwy obrazu są ściągane z rejestru, a nie z pamięci podręcznej w poprzednim wdrożeniu. Adres IP kontenera może także ulec zmianie z powodu wdrożenia na innym hoście podstawowym.

## <a name="next-steps"></a>Kolejne kroki

Wspomniano kilka razy w tym artykule **Grupa kontenerów**. Każdy kontener w Azure Container Instances jest wdrażany w grupie kontenerów, a grupy kontenerów mogą zawierać więcej niż jeden kontener.

[Grupy kontenerów w usłudze Azure Container Instances](container-instances-container-groups.md)

[Wdróż grupę z obsługą kilku kontenerów](container-instances-multi-container-group.md)

<!-- LINKS - External -->

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container?view=azure-cli-latest#az-container-create
[az-container-logs]: /cli/azure/container?view=azure-cli-latest#az-container-logs
[az-container-show]: /cli/azure/container?view=azure-cli-latest#az-container-show
[azure-cli-install]: /cli/azure/install-azure-cli
