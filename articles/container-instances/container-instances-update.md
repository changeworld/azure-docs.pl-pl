---
title: Aktualizuj grupę kontenerów
description: Dowiedz się, jak aktualizować uruchomione kontenery w grupach kontenerów Azure Container Instances.
ms.topic: article
ms.date: 09/03/2019
ms.openlocfilehash: f57ebcf050b5563b45f10af57c1721338df88ff9
ms.sourcegitcommit: 85e7fccf814269c9816b540e4539645ddc153e6e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/26/2019
ms.locfileid: "74533301"
---
# <a name="update-containers-in-azure-container-instances"></a>Aktualizowanie kontenerów w Azure Container Instances

Podczas normalnego działania wystąpień kontenera może być konieczne zaktualizowanie uruchomionych kontenerów w [grupie kontenerów](container-instances-container-groups.md). Na przykład możesz chcieć zaktualizować wersję obrazu, zmienić nazwę DNS, zaktualizować zmienne środowiskowe lub odświeżyć stan kontenera, którego aplikacja uległa awarii.

> [!NOTE]
> Nie można zaktualizować grup kontenerów zakończonych lub usuniętych. Po zakończeniu grupy kontenerów (w stanie powodzenie lub niepowodzenie) lub została usunięta, należy wdrożyć grupę jako nową.

## <a name="update-a-container-group"></a>Aktualizowanie grupy kontenerów

Zaktualizuj kontenery w uruchomionej grupie kontenerów przez ponowne wdrożenie istniejącej grupy z co najmniej jedną zmodyfikowaną właściwością. Podczas aktualizowania grupy kontenerów wszystkie uruchomione kontenery w grupie są uruchamiane ponownie w miejscu, zazwyczaj na tym samym hoście kontenera.

Wdróż ponownie istniejącą grupę kontenerów, wydając polecenie Create (lub użyj Azure Portal) i określ nazwę istniejącej grupy. Należy zmodyfikować co najmniej jedną prawidłową Właściwość grupy po wydaniu polecenia CREATE, aby wyzwolić ponowne wdrożenie, i pozostawić pozostałe właściwości bez zmian (lub nadal korzystać z wartości domyślnych). Nie wszystkie właściwości grupy kontenerów są prawidłowe dla ponownego wdrożenia. Zobacz [właściwości, które wymagają usunięcia](#properties-that-require-container-delete) , aby wyświetlić listę nieobsługiwanych właściwości.

Poniższy przykład interfejsu wiersza polecenia platformy Azure aktualizuje grupę kontenerów za pomocą nowej etykiety nazwy DNS. Ponieważ właściwość etykieta nazwy DNS grupy jest taka, którą można zaktualizować, Grupa kontenerów jest ponownie wdrażana, a jej kontenery zostały uruchomione od początku.

Początkowe wdrożenie z etykietą nazw DNS moja *aplikacja — przemieszczanie*:

```azurecli-interactive
# Create container group
az container create --resource-group myResourceGroup --name mycontainer \
    --image nginx:alpine --dns-name-label myapplication-staging
```

Zaktualizuj grupę kontenerów za pomocą nowej etykiety nazwy DNS, *aplikacji*i pozostaw pozostałe właściwości bez zmian:

```azurecli-interactive
# Update DNS name label (restarts container), leave other properties unchanged
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

Nie można zaktualizować grup kontenerów zakończonych lub usuniętych. Po zatrzymaniu grupy kontenerów (jest ona w stanie *przerwania* ) lub została usunięta, Grupa zostanie wdrożona jako nowa.

## <a name="properties-that-require-container-delete"></a>Właściwości wymagające usunięcia kontenera

Jak wspomniano wcześniej, nie można zaktualizować wszystkich właściwości grupy kontenerów. Na przykład aby zmienić porty lub zasady ponownego uruchamiania kontenera, należy najpierw usunąć grupę kontenerów, a następnie utworzyć ją ponownie.

Te właściwości wymagają usunięcia grupy kontenerów przed ponownem wdrożeniem:

* Typ systemu operacyjnego
* Procesor CPU
* Pamięć
* Zasady ponownego uruchamiania
* Porty

Gdy usuniesz grupę kontenerów i utworzysz ją ponownie, nie jest to "redeployed", ale utworzona nowa. Wszystkie warstwy obrazu są ściągane z rejestru, a nie z pamięci podręcznej w poprzednim wdrożeniu. Adres IP kontenera może także ulec zmianie z powodu wdrożenia na innym hoście podstawowym.

## <a name="next-steps"></a>Następne kroki

Wspomniano kilka razy w tym artykule **Grupa kontenerów**. Każdy kontener w Azure Container Instances jest wdrażany w grupie kontenerów, a grupy kontenerów mogą zawierać więcej niż jeden kontener.

[Grupy kontenerów w usłudze Azure Container Instances](container-instances-container-groups.md)

[Wdróż grupę z obsługą kilku kontenerów](container-instances-multi-container-group.md)

[Ręcznie Zatrzymaj lub Uruchom kontenery w Azure Container Instances](container-instances-stop-start.md)

<!-- LINKS - External -->

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container?view=azure-cli-latest#az-container-create
[azure-cli-install]: /cli/azure/install-azure-cli
