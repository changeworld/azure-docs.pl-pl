---
title: Zamów pustyDłowięż do grupy kontenerów
description: Dowiedz się, jak zainstalować wolumin emptyDir, aby udostępnić dane między kontenerami w grupie kontenerów w wystąpieniach kontenera platformy Azure
ms.topic: article
ms.date: 01/31/2020
ms.openlocfilehash: 64a3c83008f163167528a5e5987fe2316942d5bc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77117736"
---
# <a name="mount-an-emptydir-volume-in-azure-container-instances"></a>Instalowanie woluminu pustegodiru w wystąpieniach kontenera platformy Azure

Dowiedz się, jak zainstalować *wolumin emptyDir,* aby udostępnić dane między kontenerami w grupie kontenerów w wystąpieniach kontenera platformy Azure. Użyj *emptyDir* woluminów jako tymczasowych pamięci podręcznych dla konteneryzowanych obciążeń.

> [!NOTE]
> Montaż *woluminu emptyDir* jest obecnie ograniczony do kontenerów systemu Linux. Podczas gdy pracujemy nad wprowadzeniem wszystkich funkcji do kontenerów systemu Windows, możesz znaleźć aktualne różnice w platformie w [przeglądzie](container-instances-overview.md#linux-and-windows-containers).

## <a name="emptydir-volume"></a>wolumin emptyDir

Wolumin *emptyDir* udostępnia zapisywalny katalog dostępny dla każdego kontenera w grupie kontenerów. Kontenery w grupie można odczytywać i zapisywać te same pliki w woluminie i może być zainstalowany przy użyciu tych samych lub różnych ścieżek w każdym kontenerze.

W niektórych przykładach użyto *pustegodłościDir:*

* Przestrzeń do zarysowania
* Punkt kontrolny podczas długotrwałych zadań
* Przechowywanie danych pobranych przez kontener wózka bocznego i obsługiwanych przez kontener aplikacji

Dane w *woluminie emptyDir* jest utrwalony przez awarie kontenera. Kontenery, które są ponownie uruchomione, jednak nie są gwarantowane do utrwalenia danych w *emptyDir* woluminu. Jeśli zatrzymasz grupę kontenerów, *wolumin emptyDir* nie zostanie utrwalony.

Maksymalny rozmiar *woluminu linuxowego emptyDir* wynosi 50 GB.

## <a name="mount-an-emptydir-volume"></a>Montowanie pustego woluminuDir

Aby zainstalować wolumin emptyDir w wystąpieniu kontenera, można wdrożyć przy użyciu [szablonu usługi Azure Resource Manager,](/azure/templates/microsoft.containerinstance/containergroups) [pliku YAML](container-instances-reference-yaml.md)lub innych metod programowych w celu wdrożenia grupy kontenerów.

Najpierw wypełnij tablicę `volumes` w sekcji `properties` grupy kontenerów pliku. Następnie dla każdego kontenera w grupie kontenerów, w którym chcesz zainstalować *wolumin emptyDir,* wypełnij `volumeMounts` tablicę `properties` w sekcji definicji kontenera.

Na przykład następujący szablon Menedżera zasobów tworzy grupę kontenerów składającą się z dwóch kontenerów, z których każdy montuje *wolumin emptyDir:*

<!-- https://github.com/Azure/azure-docs-json-samples/blob/master/container-instances/aci-deploy-volume-emptydir.json -->
[!code-json[volume-emptydir](~/azure-docs-json-samples/container-instances/aci-deploy-volume-emptydir.json)]

Aby wyświetlić przykłady wdrażania grupy kontenerów, zobacz [Wdrażanie grupy wielu kontenerów przy użyciu szablonu Menedżera zasobów](container-instances-multi-container-group.md) i [wdrażanie grupy wielu kontenerów przy użyciu pliku YAML](container-instances-multi-container-yaml.md).

## <a name="next-steps"></a>Następne kroki

Dowiedz się, jak zainstalować inne typy woluminów w wystąpieniach kontenera platformy Azure:

* [Instalowanie udziału plików platformy Azure w usłudze Azure Container Instances](container-instances-volume-azure-files.md)
* [Instalowanie woluminu gitRepo w wystąpieniach kontenera platformy Azure](container-instances-volume-gitrepo.md)
* [Instalowanie woluminu tajnego w wystąpieniach kontenera platformy Azure](container-instances-volume-secret.md)