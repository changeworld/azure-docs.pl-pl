---
title: Uruchamiać zadania konteneryzowanych wystąpień kontenera platformy Azure
description: Dowiedz się, jak użyć wystąpień kontenera platformy Azure, aby wykonać zadania uruchamiane do ukończenia, takie jak w kompilacji, testów lub zadania renderowania obrazu.
services: container-instances
author: mmacy
manager: jeconnoc
ms.service: container-instances
ms.topic: article
ms.date: 11/16/2017
ms.author: marsma
ms.openlocfilehash: 3bbe3e891423b6ad62a1d1093daef304206f3d76
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2018
ms.locfileid: "32167133"
---
# <a name="run-a-containerized-task-in-azure-container-instances"></a>Uruchamianie zadania konteneryzowanych w wystąpień kontenera platformy Azure

Łatwość i szybkość wdrażanie kontenerów w wystąpień kontenera Azure udostępnia platformę atrakcyjnych wykonywania jednokrotnego uruchamiania zadania, takie jak kompilacji, badanie i renderowanie obrazów w wystąpieniu kontenera.

Za pomocą zasad można skonfigurować ponownego uruchomienia można określić, że kontenerów zostały zatrzymane po zakończeniu procesu. Ponieważ wystąpień kontenera są rozliczane przez drugą, są pobierane tylko dla zasobów obliczeniowych, używane podczas wykonywania zadania kontenera.

Przykłady przedstawione w tym artykule wiersza polecenia platformy Azure. Musi mieć wiersza polecenia platformy Azure w wersji 2.0.21 lub nowszej [zainstalowane lokalnie][azure-cli-install], lub użyj interfejsu wiersza polecenia w [powłoki chmury Azure](../cloud-shell/overview.md).

## <a name="container-restart-policy"></a>Zasada ponownego uruchamiania kontenera

Po utworzeniu kontenera w wystąpień kontenera Azure można określić jedną z trzech ustawień zasad ponownego uruchomienia.

| Uruchom ponownie zasad   | Opis |
| ---------------- | :---------- |
| `Always` | Kontenery w grupie kontenera zawsze są ponownie uruchamiane. Jest to **domyślne** ustawienia zastosowane, jeśli nie określono żadnych zasad ponownego uruchomienia podczas tworzenia kontenera. |
| `Never` | Kontenery w grupie kontenera nigdy nie są ponownie uruchamiane. Kontenery uruchamiane co najwyżej jeden raz. |
| `OnFailure` | Kontenery w grupie kontenera są ponownie uruchamiane tylko w przypadku niepowodzenia procesu wykonywane w kontenerze (gdy zostaje zakończone z kodem zakończenia różną od zera). Kontenery są uruchamiane co najmniej raz. |

## <a name="specify-a-restart-policy"></a>Określ zasady ponownego uruchamiania

Określania zasada ponownego uruchamiania zależy od tego, jak utworzyć swoich wystąpień kontenera, takich jak z wiersza polecenia platformy Azure, poleceń cmdlet programu Azure PowerShell lub w portalu Azure. W polu wiersza polecenia platformy Azure, określ `--restart-policy` parametr podczas wywoływania [utworzyć kontener az][az-container-create].

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer \
    --image mycontainerimage \
    --restart-policy OnFailure
```

## <a name="run-to-completion-example"></a>Uruchom przykład uzupełniania

Aby wyświetlić zasady ponownego uruchomienia w działaniach, Utwórz wystąpienie kontenera z [microsoft/aci-wordcount] [ aci-wordcount-image] obrazu, a następnie określ `OnFailure` ponowne uruchomienie zasad. Ten kontener przykład uruchamia skrypt języka Python, który domyślnie analizuje tekst w Szekspir [osady](http://shakespeare.mit.edu/hamlet/full.html), zapisuje 10 najbardziej popularnych wyrazów stdout i kończy pracę.

Uruchom przykład kontener z następującymi [utworzyć kontener az] [ az-container-create] polecenia:

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer \
    --image microsoft/aci-wordcount:latest \
    --restart-policy OnFailure
```

Wystąpień kontenera Azure rozpoczyna się kontenera i zatrzymuje go po jej aplikacji (lub skrypt, w tym przypadku) kończy pracę. Gdy wystąpień kontenera Azure zatrzymuje kontener, w której zasada ponownego uruchamiania jest `Never` lub `OnFailure`, kontenera stan jest ustawiony na **zwolniony**. Możesz sprawdzić stan kontenera o [Pokaż kontenera az] [ az-container-show] polecenia:

```azurecli-interactive
az container show --resource-group myResourceGroup --name mycontainer --query containers[0].instanceView.currentState.state
```

Przykładowe dane wyjściowe:

```bash
"Terminated"
```

Po pokazuje stan kontenera przykład *zwolniony*, można wyświetlić dane wyjściowe zadania wyświetlania dziennika kontenera. Uruchom [dzienniki kontenera az] [ az-container-logs] danych wyjściowych polecenia, aby wyświetlić skrypt:

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

Ten przykład przedstawia skrypt wysyłanej do STDOUT dane wyjściowe. Konteneryzowanych zadania, jednak może zamiast tego zapisu ich dane wyjściowe do magazynu trwałego nowsze pobierania. Na przykład, aby [udziału plików na platformę Azure](container-instances-mounting-azure-files-volume.md).

## <a name="configure-containers-at-runtime"></a>Skonfiguruj kontenery w czasie wykonywania

Podczas tworzenia wystąpienia kontenera, można ustawić jej **zmiennych środowiskowych**, a także określić niestandardowy **wiersza polecenia** do wykonania po uruchomieniu kontenera. Te ustawienia w zadań wsadowych służy do przygotowania każdego kontenera konfiguracji poszczególnych zadań.

## <a name="environment-variables"></a>Zmienne środowiskowe

Ustaw zmienne środowiskowe w Twojej kontenera zapewnienie dynamicznej konfiguracji aplikacji lub skrypt uruchamiany przez kontener. Jest to podobne do `--env` argumentu wiersza polecenia do `docker run`.

Na przykład można zmodyfikować zachowanie skryptu, na przykład kontenera przez określenie następujących zmiennych środowiskowych podczas tworzenia wystąpienia kontenera:

*NumWords*: liczba wysłanych do STDOUT słów.

*Element MinLength*: minimalna liczba znaków w wyrazie go do zliczenia. Większa liczba ignoruje popularnych wyrazów, takich jak "z" i "".

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer2 \
    --image microsoft/aci-wordcount:latest \
    --restart-policy OnFailure \
    --environment-variables NumWords=5 MinLength=8
```

Określając `NumWords=5` i `MinLength=8` zmiennych środowiskowych kontenera, dzienniki kontenera powinien być wyświetlany różnych danych wyjściowych. Gdy stan kontenera to *zwolniony* (Użyj `az container show` sprawdź jej status), wyświetlić jego dzienniki, aby zobaczyć nowe dane wyjściowe:

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

Wiersz polecenia można określić podczas tworzenia wystąpienia kontenera, aby przesłonić rozszerzania w obrazie kontenera wiersza polecenia. Jest to podobne do `--entrypoint` argumentu wiersza polecenia do `docker run`.

Na przykład masz kontenera przykład analizować tekstu innego niż *osady* , określając inną wiersza polecenia. Wykonywane przez kontener, skrypt w języku Python *wordcount.py*akceptuje jako argument adresu URL i będzie przetwarzać zawartości tej strony, zamiast domyślnej.

Na przykład, aby określić 3 pierwszych pięciu litery słowa *Romeo i Juliet*:

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer3 \
    --image microsoft/aci-wordcount:latest \
    --restart-policy OnFailure \
    --environment-variables NumWords=3 MinLength=5 \
    --command-line "python wordcount.py http://shakespeare.mit.edu/romeo_juliet/full.html"
```

Ponownie gdy kontener jest *zwolniony*, wyświetlić dane wyjściowe poprzez wyświetlenie kontenera dzienników:

```azurecli-interactive
az container logs --resource-group myResourceGroup --name mycontainer3
```

Dane wyjściowe:

```bash
[('ROMEO', 177), ('JULIET', 134), ('CAPULET', 119)]
```

## <a name="next-steps"></a>Kolejne kroki

### <a name="persist-task-output"></a>Utrwalanie dane wyjściowe zadania

Aby uzyskać więcej informacji na temat zachować dane wyjściowe z kontenerów, które mogły zostać wykonane, zobacz [instalowanie udziału plików na platformę Azure z wystąpień kontenera Azure](container-instances-mounting-azure-files-volume.md).

<!-- LINKS - External -->
[aci-wordcount-image]: https://hub.docker.com/r/microsoft/aci-wordcount/

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container?view=azure-cli-latest#az_container_create
[az-container-logs]: /cli/azure/container?view=azure-cli-latest#az_container_logs
[az-container-show]: /cli/azure/container?view=azure-cli-latest#az_container_show
[azure-cli-install]: /cli/azure/install-azure-cli
