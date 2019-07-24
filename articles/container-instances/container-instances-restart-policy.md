---
title: Używanie zasad ponownego uruchamiania z zadaniami kontenera w Azure Container Instances
description: Dowiedz się, w jaki sposób używać Azure Container Instances do wykonywania zadań, które są wykonywane do ukończenia, na przykład w ramach zadań kompilowania, testowania lub renderowania obrazu.
services: container-instances
author: dlepow
manager: gwallace
ms.service: container-instances
ms.topic: article
ms.date: 04/15/2019
ms.author: danlep
ms.openlocfilehash: 4fe5d9a20249a17030e0ccfa34f6a4f183be0d82
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/18/2019
ms.locfileid: "68325678"
---
# <a name="run-containerized-tasks-with-restart-policies"></a>Uruchamianie zadań kontenera z zasadami ponownego uruchamiania

Łatwość i szybkość wdrażania kontenerów w Azure Container Instances zapewnia atrakcyjną platformę do wykonywania zadań wykonywanych w czasie wykonywania, takich jak kompilowanie, testowanie i renderowanie obrazów w wystąpieniu kontenera.

Za pomocą konfigurowalnych zasad ponownego uruchamiania można określić, że kontenery są zatrzymane po zakończeniu procesów. Ponieważ wystąpienia kontenera są rozliczane w drugim, opłaty są naliczane tylko za zasoby obliczeniowe używane podczas wykonywania zadania.

Przykłady przedstawione w tym artykule korzystają z interfejsu wiersza polecenia platformy Azure. Musisz mieć [zainstalowany][azure-cli-install]interfejs wiersza polecenia platformy Azure w wersji 2.0.21 lub nowszej lub użyć interfejsu wiersza polecenia w [Azure Cloud Shell](../cloud-shell/overview.md).

## <a name="container-restart-policy"></a>Zasady ponownego uruchamiania kontenera

Podczas tworzenia [grupy kontenerów](container-instances-container-groups.md) w Azure Container Instances można określić jedno z trzech ustawień zasad ponownego uruchomienia.

| Zasady ponownego uruchamiania   | Opis |
| ---------------- | :---------- |
| `Always` | Kontenery w grupie kontenerów są zawsze ponownie uruchamiane. Jest to ustawienie **domyślne** stosowane, gdy nie określono zasad ponownego uruchamiania podczas tworzenia kontenera. |
| `Never` | Kontenery w grupie kontenerów nigdy nie są ponownie uruchamiane. Kontenery są uruchamiane najwyżej raz. |
| `OnFailure` | Kontenery w grupie kontenerów są uruchamiane ponownie tylko wtedy, gdy proces wykonywany w kontenerze zakończy się niepowodzeniem (gdy kończy się niezerowym kodem zakończenia). Kontenery są uruchamiane co najmniej jeden raz. |

## <a name="specify-a-restart-policy"></a>Określ zasady ponownego uruchamiania

Sposób określania zasad ponownego uruchamiania zależy od sposobu tworzenia wystąpień kontenera, takich jak w przypadku interfejsu wiersza polecenia platformy Azure, poleceń cmdlet Azure PowerShell lub Azure Portal. W interfejsie wiersza polecenia platformy Azure `--restart-policy` określ parametr podczas wywoływania polecenia [AZ Container Create][az-container-create].

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer \
    --image mycontainerimage \
    --restart-policy OnFailure
```

## <a name="run-to-completion-example"></a>Przykład przebiegu do ukończenia

Aby wyświetlić zasady ponownego uruchamiania w działaniu, Utwórz wystąpienie kontenera z obrazu Microsoft [ACI-WORDCOUNT][aci-wordcount-image] i określ `OnFailure` zasady ponownego uruchamiania. Ten przykładowy kontener uruchamia skrypt języka Python, który domyślnie analizuje tekst [Hamlet](http://shakespeare.mit.edu/hamlet/full.html)Szekspira, zapisuje 10 najpopularniejszych słów do stdout, a następnie kończy pracę.

Uruchom przykładowy kontener za pomocą następującego polecenia [AZ Container Create][az-container-create] :

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer \
    --image mcr.microsoft.com/azuredocs/aci-wordcount:latest \
    --restart-policy OnFailure
```

Azure Container Instances uruchamia kontener, a następnie go zatrzyma, gdy jego aplikacja (lub skrypt, w tym przypadku) zakończy działanie. Gdy Azure Container Instances zatrzyma kontener, którego zasady ponownego `Never` uruchamiania `OnFailure`to lub, stan kontenera to **zakończony**. Stan kontenera można sprawdzić za pomocą polecenia [AZ Container show][az-container-show] :

```azurecli-interactive
az container show --resource-group myResourceGroup --name mycontainer --query containers[0].instanceView.currentState.state
```

Przykładowe dane wyjściowe:

```bash
"Terminated"
```

Po zakończeniu wyświetlania przykładowego stanu kontenera można zobaczyć dane wyjściowe zadania, wyświetlając dzienniki kontenerów. Uruchom polecenie [AZ Container Logs][az-container-logs] , aby wyświetlić dane wyjściowe skryptu:

```azurecli-interactive
az container logs --resource-group myResourceGroup --name mycontainer
```

Dane wyjściowe:

```bash
[('the', 990),
 ('and', 702),
 ('of', 628),
 ('to', 610),
 ('I', 544),
 ('you', 495),
 ('a', 453),
 ('my', 441),
 ('in', 399),
 ('HAMLET', 386)]
```

Ten przykład pokazuje dane wyjściowe wysyłane przez skrypt do STDOUT. Zadania kontenerów mogą jednak zapisywać dane wyjściowe do magazynu trwałego na potrzeby późniejszego pobierania. Na przykład do [udziału plików platformy Azure](container-instances-mounting-azure-files-volume.md).

## <a name="next-steps"></a>Następne kroki

Scenariusze oparte na zadaniach, takie jak przetwarzanie wsadowe dużego zestawu danych z kilkoma kontenerami, mogą korzystać z niestandardowych [zmiennych środowiskowych](container-instances-environment-variables.md) lub [wierszy poleceń](container-instances-start-command.md) w czasie wykonywania.

Aby uzyskać szczegółowe informacje na temat utrwalania danych wyjściowych kontenerów, które są uruchamiane w celu ukończenia, zobacz [Instalowanie udziału plików platformy Azure za pomocą Azure Container Instances](container-instances-mounting-azure-files-volume.md).

<!-- LINKS - External -->
[aci-wordcount-image]: https://hub.docker.com/_/microsoft-azuredocs-aci-wordcount

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container?view=azure-cli-latest#az-container-create
[az-container-logs]: /cli/azure/container?view=azure-cli-latest#az-container-logs
[az-container-show]: /cli/azure/container?view=azure-cli-latest#az-container-show
[azure-cli-install]: /cli/azure/install-azure-cli
