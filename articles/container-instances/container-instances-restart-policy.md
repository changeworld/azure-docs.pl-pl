---
title: Użyj ponownie zasad przy użyciu zadań konteneryzowanych w usłudze Azure Container Instances
description: Dowiedz się, jak używać usługi Azure Container Instances do wykonania zadania, które zostało ukończone, takie jak w kompilacji, testów lub zadań renderowania obrazu.
services: container-instances
author: dlepow
ms.service: container-instances
ms.topic: article
ms.date: 03/21/2019
ms.author: danlep
ms.openlocfilehash: ef34985e7897aa751275231a28c6031d6c9747b0
ms.sourcegitcommit: 49c8204824c4f7b067cd35dbd0d44352f7e1f95e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/22/2019
ms.locfileid: "58369980"
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

## <a name="manually-stop-and-start-a-container-group"></a>Ręcznie uruchamiać i zatrzymywać grupy kontenerów

Niezależnie od zasady ponownego uruchamiania skonfigurowane dla [grupy kontenerów](container-instances-container-groups.md), możesz chcieć ręcznie zatrzymywać ani uruchamiać grupy kontenerów.

* **Zatrzymaj** - uruchomionej grupy kontenerów można ręcznie zatrzymać w dowolnym momencie — na przykład za pomocą [az container stop] [ az-container-stop] polecenia. W przypadku niektórych obciążeń kontenerów może chcesz zatrzymać grupy kontenerów po określonym przedziale czasu, aby zaoszczędzić na kosztach. 

  Zatrzymywanie grupy kontenerów kończy działanie i jest odtwarzana kontenerów w grupie; nie pozwala zachować stan kontenera. 

* **Rozpocznij** — w przypadku grupy kontenerów jest zatrzymana,-albo ponieważ kontenery został przerwany w ich własnych lub ręcznie zatrzymana grupy — możesz użyć [kontenera start interfejsu API](/rest/api/container-instances/containergroups/start) lub ręcznie uruchom kontenery witrynie Azure portal Grupa. Jeśli zostanie zaktualizowany obraz kontenera dla dowolnego kontenera, zostanie ściągnięty nowego obrazu. 

  Uruchamianie grupy kontenerów rozpoczyna nowe wdrożenie z taką samą konfiguracją kontenera. Ta akcja ułatwia szybkie ponowne zastosowanie konfiguracji grupy znanych kontenera, który działa zgodnie z oczekiwaniami. Nie trzeba utworzyć nową grupę kontenerów do uruchomienia tego samego obciążenia.

* **Uruchom ponownie** — możesz ponownie uruchomić grupy kontenerów jest uruchomiona — na przykład za pomocą [ponownego uruchomienia kontenera az] [ az-container-restart] polecenia. Ta akcja powoduje ponowne uruchomienie wszystkich kontenerów w grupie kontenerów. Jeśli zostanie zaktualizowany obraz kontenera dla dowolnego kontenera, zostanie ściągnięty nowego obrazu. 

  Ponowne uruchamianie grupy kontenerów jest przydatne w przypadku, gdy chcesz rozwiązać problem z wdrożenia. Na przykład jeśli to ograniczenie zasobów zapobiega pomyślnemu kontenerów, ponowne uruchamianie grupy może rozwiązać problem.

Po ręcznie uruchom lub uruchom ponownie grupę kontenerów, przebiegów grupy kontenera zgodnie ze skonfigurowanym ponownie uruchomić zasad.

## <a name="configure-containers-at-runtime"></a>Konfigurowanie kontenerów w czasie wykonywania

Podczas tworzenia wystąpienia kontenera można ustawić jego **zmienne środowiskowe**, jak również określić niestandardowy **wiersza polecenia** do wykonania, gdy kontener jest uruchomiona. Te ustawienia w zadaniach wsadowych służy do przygotowania każdego kontenera za pomocą konfiguracji specyficznej dla zadania.

## <a name="environment-variables"></a>Zmienne środowiskowe

Ustawianie zmiennych środowiskowych w kontenerze zapewnienie dynamiczną konfigurację aplikację lub skrypt uruchamiany przez kontener. Jest to podobne do `--env` argument wiersza polecenia, aby `docker run`.

Na przykład można zmodyfikować zachowanie skryptów w kontenerze przykład podczas tworzenia wystąpienia kontenera, określając następujące zmienne środowiskowe:

*NumWords*: Liczba słów wysyłane do strumienia wyjściowego STDOUT.

*Element MinLength*: Minimalna liczba znaków w słowie na jej do zliczenia. Większa liczba zostanie podana ignoruje popularne wyrazy, takie jak "z" i "".

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer2 \
    --image mcr.microsoft.com/azuredocs/aci-wordcount:latest \
    --restart-policy OnFailure \
    --environment-variables NumWords=5 MinLength=8
```

Określając `NumWords=5` i `MinLength=8` zmiennych środowiskowych kontenera, dzienniki kontenerów powinna zostać wyświetlona różne wyniki. Gdy stan kontenera jest wyświetlany jako *zwolniony* (Użyj `az container show` sprawdzić jego stan), wyświetlać jej dzienniki, aby zobaczyć nowe dane wyjściowe:

```azurecli-interactive
az container logs --resource-group myResourceGroup --name mycontainer2
```

Dane wyjściowe:

```bash
[('CLAUDIUS', 120),
 ('POLONIUS', 113),
 ('GERTRUDE', 82),
 ('ROSENCRANTZ', 69),
 ('GUILDENSTERN', 54)]
```



## <a name="command-line-override"></a>Zastąpienie wiersza polecenia

Wiersza polecenia można określić podczas tworzenia wystąpienia kontenera, aby zastąpić wiersz polecenia wbudowanymi w obrazie kontenera. Jest to podobne do `--entrypoint` argument wiersza polecenia, aby `docker run`.

Na przykład masz kontener przykład analizowania tekstu innego niż *osady* , określając inną wiersza polecenia. Skrypt języka Python, wykonywane przez kontener, *wordcount.py*akceptuje jako argument adresu URL i będą przetwarzać zawartość tej strony, zamiast domyślnego.

Na przykład, aby określić 3 pierwszych pięć wyrazów w *Romeo i Juliet*:

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer3 \
    --image mcr.microsoft.com/azuredocs/aci-wordcount:latest \
    --restart-policy OnFailure \
    --environment-variables NumWords=3 MinLength=5 \
    --command-line "python wordcount.py http://shakespeare.mit.edu/romeo_juliet/full.html"
```

Ponownie gdy kontener będzie *zwolniony*, aby wyświetlić dane wyjściowe, wyświetlanie dzienników kontenera:

```azurecli-interactive
az container logs --resource-group myResourceGroup --name mycontainer3
```

Dane wyjściowe:

```bash
[('ROMEO', 177), ('JULIET', 134), ('CAPULET', 119)]
```

## <a name="next-steps"></a>Kolejne kroki

### <a name="persist-task-output"></a>Utrwalanie danych wyjściowych zadania

Aby uzyskać więcej informacji na temat sposobu utrwalanie danych wyjściowych z kontenerów, które zostało ukończone, zobacz [instalowania udziału plików platformy Azure przy użyciu usługi Azure Container Instances](container-instances-mounting-azure-files-volume.md).

<!-- LINKS - External -->
[aci-wordcount-image]: https://hub.docker.com/_/microsoft-azuredocs-aci-wordcount

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container?view=azure-cli-latest#az-container-create
[az-container-logs]: /cli/azure/container?view=azure-cli-latest#az-container-logs
[az-container-restart]: /cli/azure/container?view=azure-cli-latest#az-container-restart
[az-container-show]: /cli/azure/container?view=azure-cli-latest#az-container-show
[az-container-stop]: /cli/azure/container?view=azure-cli-latest#az-container-stop
[azure-cli-install]: /cli/azure/install-azure-cli
