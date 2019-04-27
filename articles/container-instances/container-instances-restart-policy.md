---
title: Użyj ponownie zasad przy użyciu zadań konteneryzowanych w usłudze Azure Container Instances
description: Dowiedz się, jak używać usługi Azure Container Instances do wykonania zadania, które zostało ukończone, takie jak w kompilacji, testów lub zadań renderowania obrazu.
services: container-instances
author: dlepow
ms.service: container-instances
ms.topic: article
ms.date: 04/15/2019
ms.author: danlep
ms.openlocfilehash: 06872eefd0d500a22214109ad5055dd236b5a6ac
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60608130"
---
# <a name="run-containerized-tasks-with-restart-policies"></a>Uruchamianie zadań konteneryzowanych za pomocą zasady ponownego uruchamiania

Łatwe i szybkie wdrażanie kontenerów w usłudze Azure Container Instances zapewnia platformę atrakcyjnych wykonywania jednokrotnego uruchamiania zadań, takich jak tworzenie, testowanie i renderowania obrazów w wystąpieniu kontenera.

Za pomocą zasad można skonfigurować ponownego uruchomienia można określić, że kontenerów zostały zatrzymane po zakończeniu procesu. Ponieważ usługa container instances jest rozliczana za sekundę, opłaty są naliczane tylko za zasoby obliczeniowe używane po uruchomieniu kontenera wykonywania zadania.

Przykłady przedstawione w tym artykule wiersza polecenia platformy Azure. Konieczne jest posiadanie wiersza polecenia platformy Azure w wersji 2.0.21 lub nowszej [zainstalowane lokalnie][azure-cli-install], lub za pomocą interfejsu wiersza polecenia w [usługi Azure Cloud Shell](../cloud-shell/overview.md).

## <a name="container-restart-policy"></a>Zasady ponownego uruchamiania kontenera

Po utworzeniu [grupy kontenerów](container-instances-container-groups.md) w usłudze Azure Container Instances, możesz określić jedną z trzech ustawień zasady ponownego uruchamiania.

| Zasada ponownego uruchamiania   | Opis |
| ---------------- | :---------- |
| `Always` | Zawsze zostaną ponownie uruchomione kontenery w grupie kontenerów. Jest to **domyślne** ustawienia stosowane, gdy nie zasady ponownego uruchamiania jest określana podczas tworzenia kontenera. |
| `Never` | Nigdy nie zostaną ponownie uruchomione kontenery w grupie kontenerów. Kontenery są uruchamiane co najwyżej raz. |
| `OnFailure` | Kontenery w grupie kontenerów zostaną ponownie uruchomione tylko wtedy, gdy proces wykonywany w kontenerze zakończy się niepowodzeniem, (gdy zostaje zakończone z kodem zakończenia różny od zera). Kontenery są uruchamiane co najmniej raz. |

## <a name="specify-a-restart-policy"></a>Określ zasady ponownego uruchamiania

Jak określić zasady ponownego uruchamiania, zależy od tego, jak utworzyć wystąpienia kontenera, takie jak przy użyciu wiersza polecenia platformy Azure, w przypadku poleceń cmdlet programu Azure PowerShell lub w witrynie Azure portal. W interfejsie wiersza polecenia platformy Azure, określ `--restart-policy` parametru podczas wywoływania [utworzyć kontener az][az-container-create].

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer \
    --image mycontainerimage \
    --restart-policy OnFailure
```

## <a name="run-to-completion-example"></a>Uruchom do ukończenia przykładu

Aby wyświetlić zasady ponownego uruchamiania w akcji, Utwórz wystąpienie kontenera z firmy Microsoft [aci wordcount] [ aci-wordcount-image] obrazu, a następnie określ `OnFailure` zasady ponownego uruchamiania. Ten przykład kontener uruchamia skrypt w języku Python, który domyślnie analizuje tekst w Szekspir [osady](http://shakespeare.mit.edu/hamlet/full.html), zapisuje 10 najczęściej słowa do strumienia wyjściowego STDOUT, a następnie kończy działanie.

Uruchom kontener przykład następującym kodem [utworzyć kontener az] [ az-container-create] polecenia:

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer \
    --image mcr.microsoft.com/azuredocs/aci-wordcount:latest \
    --restart-policy OnFailure
```

Usługa Azure Container Instances uruchamia kontener, a następnie zatrzymuje, gdy kończy działanie swoich aplikacji (lub skrypt, w tym przypadku). Gdy usługi Azure Container Instances zatrzymuje kontener, którego zasady ponownego uruchamiania jest `Never` lub `OnFailure`, stan kontenera jest ustawiony na **zwolniony**. Możesz sprawdzić stan kontenera za pomocą [az container show] [ az-container-show] polecenia:

```azurecli-interactive
az container show --resource-group myResourceGroup --name mycontainer --query containers[0].instanceView.currentState.state
```

Przykładowe dane wyjściowe:

```bash
"Terminated"
```

Gdy stan kontenera przykład *zwolniony*, zobaczysz dane wyjściowe zadania, wyświetlając dzienniki kontenerów. Uruchom [dzienniki kontenerów az] [ az-container-logs] danych wyjściowych polecenia, aby wyświetlić skryptu:

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

Ten przykład przedstawia dane wyjściowe skryptu wysyłane do strumienia wyjściowego STDOUT. Konteneryzowane zadania podrzędne, jednak może być zamiast tego zapisują swoje dane wyjściowe do trwałego magazynu na potrzeby pobierania nowsze. Na przykład, aby [udziału plików platformy Azure](container-instances-mounting-azure-files-volume.md).

## <a name="next-steps"></a>Kolejne kroki

Oparta na zadaniach scenariuszach, na przykład wsadowo duży zestaw danych za pomocą kilku kontenerów mogą korzystać z niestandardowych [zmienne środowiskowe](container-instances-environment-variables.md) lub [wiersze polecenia](container-instances-start-command.md) w czasie wykonywania.

Aby uzyskać więcej informacji na temat sposobu utrwalanie danych wyjściowych z kontenerów, które zostało ukończone, zobacz [instalowania udziału plików platformy Azure przy użyciu usługi Azure Container Instances](container-instances-mounting-azure-files-volume.md).

<!-- LINKS - External -->
[aci-wordcount-image]: https://hub.docker.com/_/microsoft-azuredocs-aci-wordcount

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container?view=azure-cli-latest#az-container-create
[az-container-logs]: /cli/azure/container?view=azure-cli-latest#az-container-logs
[az-container-show]: /cli/azure/container?view=azure-cli-latest#az-container-show
[azure-cli-install]: /cli/azure/install-azure-cli
