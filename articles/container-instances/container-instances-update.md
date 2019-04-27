---
title: Aktualizuj kontenerów w usłudze Azure Container Instances
description: Dowiedz się, jak zaktualizować uruchomione kontenery w grupach kontenera usługi Azure Container Instances.
services: container-instances
author: dlepow
ms.service: container-instances
ms.topic: article
ms.date: 08/01/2018
ms.author: danlep
ms.openlocfilehash: 2df6a2724cbdcd6bbb6c6ca6636256b7e399da8e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60686895"
---
# <a name="update-containers-in-azure-container-instances"></a>Aktualizuj kontenerów w usłudze Azure Container Instances

Podczas normalnej pracy wystąpienia kontenera może okazać się konieczne zaktualizowanie kontenerów w grupie kontenerów. Na przykład może chcieć zaktualizować wersję obrazu, zmiany nazwy DNS, zaktualizuj zmienne środowiskowe lub Odśwież stan kontenera, w których aplikacja uległa awarii.

## <a name="update-a-container-group"></a>Zaktualizuj grupę kontenerów

Ponowne wdrażanie istniejącej grupy z co najmniej jedną właściwość zmodyfikowane, aby zaktualizować kontenerów w grupie kontenerów. Podczas aktualizowania kontenera grupy wszystkich uruchomionych kontenerów w grupie zostaną ponownie uruchomione w miejscu.

Wdróż ponownie istniejącej grupy kontenerów przez wydanie polecenia create (lub użyj witryny Azure portal), a następnie określ nazwę istniejącej grupy. Po wykonaniu polecenia create wyzwalanie ponownego wdrożenia komputera, należy zmodyfikować co najmniej jedną prawidłową właściwością grupy. Nie wszystkie właściwości grupy kontenerów są prawidłowe dla ponownego wdrażania. Zobacz [właściwości, które wymagają usunięcia](#properties-that-require-container-delete) listę nieobsługiwane właściwości.

Poniższy przykład wiersza polecenia platformy Azure zostaje zaktualizowana o nowe etykiety nazwy DNS grupy kontenerów. Ponieważ właściwość etykiety nazwy DNS grupy został zmodyfikowany, grupy kontenerów jest ponownie wdrażana i ponownie uruchomić jego kontenerów.

Wstępne wdrożenie przy użyciu etykiety nazwy DNS *przemieszczania MojaAplikacja*:

```azurecli-interactive
# Create container group
az container create --resource-group myResourceGroup --name mycontainer \
    --image nginx:alpine --dns-name-label myapplication-staging
```

Zaktualizuj grupę kontenerów przy użyciu nowej etykiety nazwy DNS *myapplication*:

```azurecli-interactive
# Update container group (restarts container)
az container create --resource-group myResourceGroup --name mycontainer \
    --image nginx:alpine --dns-name-label myapplication
```

## <a name="update-benefits"></a>Korzyści aktualizację

Główną Korzyścią płynącą aktualizowania istniejącej grupy kontenerów jest szybsze wdrażanie. Podczas ponownego wdrażania istniejącej grupy kontenera jego warstw obrazu kontenera są ściągane z te buforowane przez poprzedniego wdrożenia. Zamiast wszystkich warstw obrazu ściąganie świeże z rejestru, jak odbywa się za pomocą nowych wdrożeń, są pobierane tylko modyfikacji warstwy (jeśli istnieje).

Aplikacje oparte na większych obrazów kontenerów, takich jak Windows Server Core można zobaczyć znacznej poprawy szybkości wdrażania, gdy aktualizujesz zamiast usuwania i wdrożyć nowy.

## <a name="limitations"></a>Ograniczenia

Nie wszystkie właściwości grupy kontenerów obsługuje aktualizacje. Aby zmienić niektóre właściwości grupy kontenerów, należy najpierw usunąć, a następnie Wdróż ponownie grupę. Aby uzyskać więcej informacji, zobacz [Usuń właściwości, które wymagają kontenera](#properties-that-require-container-delete).

Wszystkie kontenery w grupie kontenerów zostaną ponownie uruchomione po zaktualizowaniu grupy kontenerów. Nie można wykonać aktualizacji ani ponownego uruchomienia w miejscu określonym kontenerze w grupy wielu kontenerów.

Adres IP kontenera zwykle nie zmienią się między aktualizacjami, ale go nie ma gwarancji pozostają takie same. Tak długo, jak grupy kontenerów jest wdrażana do tego samego podstawowego hosta, grupy kontenerów zachowuje swój adres IP. Sporadycznie, a gdy usługi Azure Container Instances dokłada wszelkich starań, aby przeprowadzić ponowne wdrożenie na tym samym hoście, istnieją niektóre Azure wewnętrznego zdarzenia, które może spowodować ponowne wdrożenie na innym hoście. Aby rozwiązać ten problem, należy zawsze używać etykiety nazwy DNS dla usługi container instances.

Nie można zaktualizować kontenera zakończone lub usuniętych grup. Po zatrzymaniu ma grupy kontenerów (znajduje się w *zwolniony* stanu) lub zostało usunięte, w grupie jest wdrażany jako nowy.

## <a name="properties-that-require-container-delete"></a>Usuwanie właściwości, które wymagają kontenera

Jak wspomniano wcześniej, nie wszystkie właściwości grupy kontenerów może zostać zaktualizowana. Na przykład zmienić portów, lub uruchomić ponownie zasad kontenera, użytkownik musi najpierw usuń grupę kontenerów, a następnie utworzyć ją ponownie.

Właściwości te wymagają usunięcia grupy kontenera przed ponownego wdrażania:

* Typ systemu operacyjnego
* Procesor CPU
* Memory (Pamięć)
* Zasada ponownego uruchamiania
* Porty

Gdy Usuń grupę kontenerów i utworzyć ją ponownie, ten ma nie "ponownego wdrożenia", ale tworzenia nowych. Wszystkie warstwy obrazu pochodzą od nowa z rejestru, a nie z buforowanego przez poprzedniego wdrożenia. Adres IP kontenera również mogą ulec zmianie ze względu na wdrażane na różnych odpowiedniego hosta.

## <a name="next-steps"></a>Kolejne kroki

Wymienione kilka razy, w tym artykule jest **grupy kontenerów**. Każdy kontener w usłudze Azure Container Instances jest wdrażana w grupie kontenerów i grup kontenerów może zawierać więcej niż jednego kontenera.

[Grupy kontenerów w usłudze Azure Container Instances](container-instances-container-groups.md)

[Wdrażanie grupy wielu kontenerów](container-instances-multi-container-group.md)

<!-- LINKS - External -->

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container?view=azure-cli-latest#az-container-create
[az-container-logs]: /cli/azure/container?view=azure-cli-latest#az-container-logs
[az-container-show]: /cli/azure/container?view=azure-cli-latest#az-container-show
[azure-cli-install]: /cli/azure/install-azure-cli
