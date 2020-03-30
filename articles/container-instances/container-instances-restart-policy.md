---
title: Zasady ponownego uruchamiania dla zadań po uruchomieniu
description: Dowiedz się, jak używać wystąpień kontenera platformy Azure do wykonywania zadań uruchamianych do ukończenia, takich jak zadania kompilacji, testowania lub renderowania obrazów.
ms.topic: article
ms.date: 04/15/2019
ms.openlocfilehash: 8ef4ef228038242f53abc8041470f7f596ab1157
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80131494"
---
# <a name="run-containerized-tasks-with-restart-policies"></a>Uruchamianie zadań konteneryzowanych z zasadami ponownego uruchamiania

Łatwość i szybkość wdrażania kontenerów w usłudze Azure Container Instances zapewnia atrakcyjną platformę do wykonywania jednorazowych zadań, takich jak kompilacje, testy i renderowanie obrazów w wystąpieniu kontenera.

Dzięki możliwym do skonfigurowania zasadom ponownego uruchamiania możesz określić, aby Twoje kontenery zatrzymały się po zakończeniu wykonywania procesów. Ponieważ opłaty za wystąpienia kontenerów są naliczane za sekundę, rozliczeniu podlegają tylko zasoby obliczeniowe używane, kiedy działa kontener wykonujący zadanie.

Przykłady przedstawione w tym artykule używają interfejsu wiersza polecenia platformy Azure. Musisz mieć [zainstalowany lokalny][azure-cli-install]system interfejsu wiersza polecenia platformy Azure w wersji 2.0.21 lub więcej lub użyć interfejsu wiersza polecenia w [usłudze Azure Cloud Shell.](../cloud-shell/overview.md)

## <a name="container-restart-policy"></a>Zasady ponownego uruchamiania kontenera

Podczas tworzenia [grupy kontenerów](container-instances-container-groups.md) w instancjach kontenera platformy Azure można określić jedno z trzech ustawień zasad ponownego uruchamiania.

| Zasady ponownego uruchamiania   | Opis |
| ---------------- | :---------- |
| `Always` | Kontenery w grupie kontenerów są zawsze uruchamiane ponownie. Jest to **domyślne** ustawienie stosowane, gdy podczas tworzenia kontenera nie zostanie określona żadna zasada ponownego uruchamiania. |
| `Never` | Kontenery w grupie kontenerów nigdy nie są uruchamiane ponownie. Kontenery są uruchamiane co najwyżej raz. |
| `OnFailure` | Kontenery w grupie kontenerów są uruchamiane ponownie tylko wtedy, gdy proces wykonywany w kontenerze zakończy się niepowodzeniem (zostanie przerwany z kodem zakończenia różnym od zera). Kontenery są uruchamiane co najmniej raz. |

## <a name="specify-a-restart-policy"></a>Określanie zasad ponownego uruchamiania

Sposób określania zasad ponownego uruchamiania zależy od sposobu tworzenia wystąpień kontenera, takich jak interfejsu wiersza polecenia platformy Azure, poleceń cmdlet programu Azure PowerShell lub w witrynie Azure portal. W wierszu polecenia `--restart-policy` platformy Azure określ parametr podczas wywoływania [tworzenia kontenera az][az-container-create].

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer \
    --image mycontainerimage \
    --restart-policy OnFailure
```

## <a name="run-to-completion-example"></a>Przykład uruchamiania do uzupełniania

Aby wyświetlić zasady ponownego uruchamiania w działaniu, utwórz wystąpienie kontenera z obrazu [Microsoft aci-wordcount][aci-wordcount-image] i określ zasady ponownego uruchamiania. `OnFailure` W tym przykładzie kontener uruchamia skrypt Języka Python, który domyślnie analizuje tekst [Hamleta](http://shakespeare.mit.edu/hamlet/full.html)Szekspira, zapisuje 10 najczęściej występujących słów do STDOUT, a następnie kończy pracę.

Uruchom przykładowy kontener za pomocą następującego polecenia [tworzenia kontenera az:][az-container-create]

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer \
    --image mcr.microsoft.com/azuredocs/aci-wordcount:latest \
    --restart-policy OnFailure
```

Usługa Azure Container Instances uruchamia kontener, a następnie zatrzymuje go, gdy znajdująca się w nim aplikacja (lub, jak w tym przypadku, skrypt) kończy działanie. Gdy wystąpienia kontenera platformy Azure zatrzymuje `Never` `OnFailure`kontener, którego zasady ponownego uruchamiania jest lub , stan kontenera jest ustawiona na **Zakończone**. Stan kontenera można sprawdzić za pomocą polecenia [az container show:][az-container-show]

```azurecli-interactive
az container show \
    --resource-group myResourceGroup \
    --name mycontainer \
    --query containers[0].instanceView.currentState.state
```

Przykładowe dane wyjściowe:

```bash
"Terminated"
```

Gdy stan przykładowego kontenera pokazuje *Zakończone*, można zobaczyć jego dane wyjściowe zadania, wyświetlając dzienniki kontenera. Uruchom polecenie [dzienniki kontenera az,][az-container-logs] aby wyświetlić dane wyjściowe skryptu:

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

W tym przykładzie pokazano dane wyjściowe, które skrypt wysłany do STDOUT. Konteneryzowane zadania mogą jednak zamiast tego zapisywać ich dane wyjściowe w magazynie trwałym w celu późniejszego pobrania. Na przykład do [udziału plików platformy Azure](container-instances-mounting-azure-files-volume.md).

## <a name="next-steps"></a>Następne kroki

Scenariusze oparte na zadaniach, takie jak przetwarzanie wsadowe dużego zestawu danych z kilkoma [kontenerami,](container-instances-environment-variables.md) mogą korzystać z niestandardowych zmiennych środowiskowych lub [wierszy poleceń](container-instances-start-command.md) w czasie wykonywania.

Aby uzyskać szczegółowe informacje na temat zachowywania danych wyjściowych kontenerów, które są uruchamiane do ukończenia, zobacz [Instalowanie udziału plików platformy Azure za pomocą wystąpień kontenera platformy Azure.](container-instances-mounting-azure-files-volume.md)

<!-- LINKS - External -->
[aci-wordcount-image]: https://hub.docker.com/_/microsoft-azuredocs-aci-wordcount

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container?view=azure-cli-latest#az-container-create
[az-container-logs]: /cli/azure/container?view=azure-cli-latest#az-container-logs
[az-container-show]: /cli/azure/container?view=azure-cli-latest#az-container-show
[azure-cli-install]: /cli/azure/install-azure-cli
