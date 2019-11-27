---
title: Zainstaluj wolumin emptyDir do grupy kontenerów
description: Dowiedz się, jak zainstalować wolumin emptyDir, aby udostępnić dane między kontenerami w grupie kontenerów w Azure Container Instances
ms.topic: article
ms.date: 02/08/2018
ms.openlocfilehash: 0440bcc490b766c12b2117d2453557707df2a1c4
ms.sourcegitcommit: 85e7fccf814269c9816b540e4539645ddc153e6e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/26/2019
ms.locfileid: "74533229"
---
# <a name="mount-an-emptydir-volume-in-azure-container-instances"></a>Zainstaluj wolumin emptyDir w Azure Container Instances

Dowiedz się, jak zainstalować wolumin *emptyDir* , aby udostępnić dane między kontenerami w grupie kontenerów w Azure Container Instances.

> [!NOTE]
> Instalowanie woluminu *emptyDir* jest obecnie ograniczone do kontenerów systemu Linux. Gdy pracujemy nad przełączeniem wszystkich funkcji do kontenerów systemu Windows, w [przeglądzie](container-instances-overview.md#linux-and-windows-containers)można znaleźć bieżące różnice między platformami.

## <a name="emptydir-volume"></a>wolumin emptyDir

Wolumin *emptyDir* udostępnia zapisywalny katalog dla każdego kontenera w grupie kontenerów. Kontenery w grupie mogą odczytywać i zapisywać te same pliki w woluminie i mogą być instalowane przy użyciu tych samych lub różnych ścieżek w każdym kontenerze.

Przykład użycia dla woluminu *emptyDir* :

* Miejsce na pliki tymczasowe
* Tworzenie punktów kontrolnych podczas długotrwałych zadań
* Przechowuj dane pobierane przez kontener przyczepek i obsługiwane przez kontener aplikacji

Dane w woluminie *emptyDir* są utrwalane przez awarie kontenera. Jednak kontenery, które są ponownie uruchamiane, nie są gwarantowane, aby zachować dane na woluminie *emptyDir* .

## <a name="mount-an-emptydir-volume"></a>Instalowanie woluminu emptyDir

Aby zainstalować wolumin emptyDir w wystąpieniu kontenera, należy wdrożyć przy użyciu [szablonu Azure Resource Manager](/azure/templates/microsoft.containerinstance/containergroups).

Najpierw Wypełnij tablicę `volumes` w sekcji `properties` grupy kontenerów szablonu. Następnie dla każdego kontenera w grupie kontenerów, w której chcesz zainstalować wolumin *emptyDir* , Wypełnij tablicę `volumeMounts` w sekcji `properties` definicji kontenera.

Na przykład poniższy szablon Menedżer zasobów tworzy grupę kontenerów składającą się z dwóch kontenerów, z których każdy instaluje wolumin *emptyDir* :

<!-- https://github.com/Azure/azure-docs-json-samples/blob/master/container-instances/aci-deploy-volume-emptydir.json -->
[!code-json[volume-emptydir](~/azure-docs-json-samples/container-instances/aci-deploy-volume-emptydir.json)]

Aby zobaczyć przykład wdrożenia wystąpienia kontenera z szablonem Azure Resource Manager, zobacz [wdrażanie grup wielokontenerowych w Azure Container Instances](container-instances-multi-container-group.md).

## <a name="next-steps"></a>Następne kroki

Dowiedz się, jak zainstalować inne typy woluminów w Azure Container Instances:

* [Instalowanie udziału plików platformy Azure w usłudze Azure Container Instances](container-instances-volume-azure-files.md)
* [Zainstaluj wolumin gitRepo w Azure Container Instances](container-instances-volume-gitrepo.md)
* [Zainstaluj wolumin tajny w Azure Container Instances](container-instances-volume-secret.md)