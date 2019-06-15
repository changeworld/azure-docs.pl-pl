---
title: Zainstalować woluminu emptyDir w usłudze Azure Container Instances
description: Dowiedz się, jak można zainstalować woluminu emptyDir udostępnianie danych między kontenerów w grupie kontenerów w usłudze Azure Container Instances
services: container-instances
author: dlepow
ms.service: container-instances
ms.topic: article
ms.date: 02/08/2018
ms.author: danlep
ms.openlocfilehash: 98a72123a05fa7d8dc16be7ddb787f2a2cf7e4d1
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60563124"
---
# <a name="mount-an-emptydir-volume-in-azure-container-instances"></a>Zainstalować woluminu emptyDir w usłudze Azure Container Instances

Dowiedz się, jak zainstalować *emptyDir* wolumin, aby udostępniać dane między kontenerów w grupie kontenerów w usłudze Azure Container Instances.

> [!NOTE]
> Instalowanie *emptyDir* wolumin jest obecnie ograniczone do kontenerów systemu Linux. Podczas gdy pracujemy, aby udostępnić wszystkie funkcje na potrzeby kontenerów systemu Windows, bieżące różnice dotyczące platform możesz znaleźć w temacie [Limity przydziałów i dostępność regionów dla usługi Azure Container Instances](container-instances-quotas.md).

## <a name="emptydir-volume"></a>wolumin emptyDir

*EmptyDir* wolumin zapewnia katalogów zapisu dostępne dla każdego kontenera w grupie kontenerów. Kontenery w grupie mogą odczytywać i zapisywać te same pliki w woluminie i mogą być instalowane, używając tych samych lub różnych ścieżek w każdym kontenerze.

Niektóre przykłady użycia usługi *emptyDir* woluminu:

* Miejsce na pliki tymczasowe
* Tworzenie punktów kontrolnych podczas długotrwałych zadań
* Store data pobierany przez kontenerem przyczepki i obsługiwane przez kontener aplikacji

Dane w *emptyDir* woluminu jest trwały za pośrednictwem awarie kontenera. Kontenery, które zostaną ponownie uruchomione, jednak nie ma gwarancji utrwalanie danych w *emptyDir* woluminu.

## <a name="mount-an-emptydir-volume"></a>Zainstalować woluminu emptyDir

Można zainstalować woluminu emptyDir w wystąpieniu kontenera, należy wdrożyć przy użyciu [szablonu usługi Azure Resource Manager](/azure/templates/microsoft.containerinstance/containergroups).

Najpierw należy wypełnić `volumes` tablicy w grupie kontenerów `properties` części szablonu. Następnie dla każdego kontenera w grupie kontenerów, w którym chcesz zainstalować *emptyDir* woluminu, wypełnij `volumeMounts` tablicy w `properties` sekcja definicji kontenera.

Na przykład następujący szablon usługi Resource Manager tworzy grupę kontenerów składające się z dwóch kontenerów, każda z których instaluje *emptyDir* woluminu:

<!-- https://github.com/Azure/azure-docs-json-samples/blob/master/container-instances/aci-deploy-volume-emptydir.json -->
[!code-json[volume-emptydir](~/azure-docs-json-samples/container-instances/aci-deploy-volume-emptydir.json)]

Aby zapoznać się z przykładem wdrażaniem wystąpienia kontenera za pomocą szablonu usługi Azure Resource Manager, zobacz [wdrażanie grup wielu kontenerów w usłudze Azure Container Instances](container-instances-multi-container-group.md).

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się, jak zainstalować inne typy woluminu w usłudze Azure Container Instances:

* [Instalowanie udziału plików platformy Azure w usłudze Azure Container Instances](container-instances-volume-azure-files.md)
* [Zainstalować wolumin gitRepo w wystąpień kontenera platformy Azure](container-instances-volume-gitrepo.md)
* [Zainstaluj wolumin tajny w usłudze Azure Container Instances](container-instances-volume-secret.md)