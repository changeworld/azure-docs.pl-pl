---
title: Aktualizuj grupę kontenerów
description: Dowiedz się, jak zaktualizować uruchomione kontenery w grupach kontenerów wystąpień kontenerów usługi Azure Container.
ms.topic: article
ms.date: 09/03/2019
ms.openlocfilehash: f57ebcf050b5563b45f10af57c1721338df88ff9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74533301"
---
# <a name="update-containers-in-azure-container-instances"></a>Aktualizowanie kontenerów w usłudze Azure Container Instances

Podczas normalnego działania wystąpień kontenera może okazać się konieczne zaktualizowanie uruchomionych kontenerów w [grupie kontenerów](container-instances-container-groups.md). Na przykład można zaktualizować wersję obrazu, zmienić nazwę DNS, zaktualizować zmienne środowiskowe lub odświeżyć stan kontenera, którego aplikacja uległa awarii.

> [!NOTE]
> Nie można zaktualizować zakończonych lub usuniętych grup kontenerów. Po zakończeniu grupy kontenerów (jest w stanie udane lub nieudane) lub został usunięty, grupa musi być wdrożony jako nowy.

## <a name="update-a-container-group"></a>Aktualizowanie grupy kontenerów

Zaktualizuj kontenery w uruchomionej grupie kontenerów, ponownie rozmieszczając istniejącą grupę z co najmniej jedną zmodyfikowaną właściwością. Po zaktualizowaniu grupy kontenerów wszystkie uruchomione kontenery w grupie są ponownie uruchamiane w miejscu, zwykle na tym samym podstawowym hoście kontenera.

Ponownie rozmieszcz istniejącą grupę kontenerów, wydając polecenie create (lub użyj portalu Azure) i określ nazwę istniejącej grupy. Zmodyfikuj co najmniej jedną prawidłową właściwość grupy podczas wystawiania polecenia create, aby wyzwolić ponowne wdrożenie, i pozostaw pozostałe właściwości bez zmian (lub nadal używasz wartości domyślnych). Nie wszystkie właściwości grupy kontenerów są prawidłowe dla ponownego rozmieszczenia. Zobacz [Właściwości, które wymagają usunięcia](#properties-that-require-container-delete) dla listy nieobsługiwałych właściwości.

Poniższy przykład interfejsu wiersza polecenia platformy Azure aktualizuje grupę kontenerów z nową etykietą nazwy DNS. Ponieważ właściwość etykiety nazwy DNS grupy jest właściwość, która może być aktualizowana, grupa kontenerów jest ponownie rozmieszczona, a jej kontenery ponownie.

Początkowe wdrożenie z etykietą nazwy DNS *myapplication-staging:*

```azurecli-interactive
# Create container group
az container create --resource-group myResourceGroup --name mycontainer \
    --image nginx:alpine --dns-name-label myapplication-staging
```

Zaktualizuj grupę kontenerów o nową etykietę nazwy DNS, *myapplication*i pozostaw pozostałe właściwości bez zmian:

```azurecli-interactive
# Update DNS name label (restarts container), leave other properties unchanged
az container create --resource-group myResourceGroup --name mycontainer \
    --image nginx:alpine --dns-name-label myapplication
```

## <a name="update-benefits"></a>Aktualizowanie korzyści

Podstawową zaletą aktualizowania istniejącej grupy kontenerów jest szybsze wdrażanie. Po ponownej demisji istniejącej grupy kontenerów, jego warstwy obrazu kontenera są pobierane z tych buforowanych przez poprzednie wdrożenie. Zamiast ściągać wszystkie warstwy obrazów prosto z rejestru, jak to ma miejsce w przypadku nowych wdrożeń, tylko zmodyfikowane warstwy (jeśli istnieją) są pobierane.

Aplikacje oparte na większych obrazach kontenerów, takich jak Windows Server Core, mogą znacznie poprawić szybkość wdrażania podczas aktualizacji zamiast usuwania i wdrażania nowych.

## <a name="limitations"></a>Ograniczenia

Nie wszystkie właściwości grupy kontenerów obsługują aktualizacje. Aby zmienić niektóre właściwości grupy kontenerów, należy najpierw usunąć, a następnie ponownie wdrożyć grupę. Aby uzyskać szczegółowe informacje, zobacz [Właściwości, które wymagają usunięcia kontenera](#properties-that-require-container-delete).

Wszystkie kontenery w grupie kontenerów są ponownie uruchamiane po zaktualizowaniu grupy kontenerów. Nie można wykonać aktualizacji lub ponownego uruchomienia w miejscu określonego kontenera w grupie wielu kontenerów.

Adres IP kontenera zazwyczaj nie zmienia się między aktualizacjami, ale nie ma gwarancji, że pozostanie taki sam. Tak długo, jak grupa kontenerów jest wdrażana na tym samym hoście źródłowym, grupa kontenerów zachowuje swój adres IP. Mimo że rzadko i podczas wystąpienia kontenera platformy Azure dokłada wszelkich starań, aby ponownie wdrożyć do tego samego hosta, istnieją pewne zdarzenia wewnętrzne platformy Azure, które mogą powodować ponowne wdrożenie do innego hosta. Aby rozwiązać ten problem, należy zawsze używać etykiety nazwy DNS dla wystąpień kontenera.

Nie można zaktualizować zakończonych lub usuniętych grup kontenerów. Po zatrzymaniu grupy kontenerów (jest w stanie *Zakończone)* lub został usunięty, grupa jest wdrażany jako nowy.

## <a name="properties-that-require-container-delete"></a>Właściwości wymagające usunięcia kontenera

Jak wspomniano wcześniej, nie wszystkie właściwości grupy kontenerów mogą być aktualizowane. Na przykład, aby zmienić porty lub ponownie uruchomić zasady kontenera, należy najpierw usunąć grupę kontenerów, a następnie utworzyć ją ponownie.

Te właściwości wymagają usunięcia grupy kontenerów przed ponowne wdrożenie:

* Typ systemu operacyjnego
* Procesor CPU
* Memory (Pamięć)
* Zasady ponownego uruchamiania
* Porty

Po usunięciu grupy kontenerów i ponownym utworzeniu jej nie jest ona "ponownie rozmieszczona", ale została utworzona jako nowa. Wszystkie warstwy obrazu są pobierane świeże z rejestru, a nie z tych buforowanych przez poprzednie wdrożenie. Adres IP kontenera może również ulec zmianie z powodu wdrożenia na innym podstawowym hoście.

## <a name="next-steps"></a>Następne kroki

Wymienione kilka razy w tym artykule jest **grupa kontenerów**. Każdy kontener w wystąpieniach kontenera platformy Azure jest wdrażany w grupie kontenerów, a grupy kontenerów mogą zawierać więcej niż jeden kontener.

[Grupy kontenerów w usłudze Azure Container Instances](container-instances-container-groups.md)

[Wdrażanie grupy z wieloma kontenerami](container-instances-multi-container-group.md)

[Ręczne zatrzymywać lub uruchamiać kontenery w wystąpieniach kontenerów platformy Azure](container-instances-stop-start.md)

<!-- LINKS - External -->

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container?view=azure-cli-latest#az-container-create
[azure-cli-install]: /cli/azure/install-azure-cli
