---
title: Użyj uruchamianie wiersza polecenia w usłudze Azure Container Instances
description: Zastąp punkt wejścia skonfigurowane w postaci obrazu kontenera w przypadku wdrażania wystąpienia kontenera platformy Azure
services: container-instances
author: dlepow
ms.service: container-instances
ms.topic: article
ms.date: 04/15/2019
ms.author: danlep
ms.openlocfilehash: da94a4c79694f511d41e5c8dda8c786fc7049726
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "64569633"
---
# <a name="set-the-command-line-in-a-container-instance-to-override-the-default-command-line-operation"></a>Ustaw w wierszu polecenia w wystąpieniu kontenera, aby zastąpić domyślne działania wiersza polecenia

Kiedy tworzysz wystąpienie kontenera, opcjonalnie można określić polecenie, aby zastąpić domyślne instrukcji wiersza polecenia, wbudowanymi do obrazu kontenera. To zachowanie jest podobne do `--entrypoint` argument wiersza polecenia, aby `docker run`.

Ustawienia, takie jak [zmienne środowiskowe](container-instances-environment-variables.md) dla container instances, określając uruchamianie wiersza polecenia jest przydatne w przypadku zadań wsadowych gdzie należy przygotować każdego kontenera dynamicznie przy użyciu konfiguracji specyficzne dla zadania.

## <a name="command-line-guidelines"></a>Wytyczne dotyczące wiersza polecenia

* Określa wiersz polecenia, domyślnie *pojedynczego procesu, który uruchamia bez powłokę* w kontenerze. Na przykład wiersza polecenia może uruchomić skrypt w języku Python lub pliku wykonywalnego. 

* Do wykonania wielu poleceń, należy rozpocząć wierszu polecenia, ustawiając środowisko powłoki, który jest obsługiwany w systemie operacyjnym kontenera. Przykłady:

  |System operacyjny  |Domyślnej powłoki  |
  |---------|---------|
  |Ubuntu     |   `/bin/bash`      |
  |Firma Alpine     |   `/bin/sh`      |
  |Windows     |    `cmd`     |

  Postępuj zgodnie z Konwencji powłoki połączenie kilku poleceń do uruchamiania w sekwencji.

* W zależności od konfiguracji kontenera może być konieczne ustawienie pełną ścieżkę do pliku wykonywalnego wiersza polecenia lub argumentów.

* Ustaw odpowiednią [zasady ponownego uruchamiania](container-instances-restart-policy.md) do wystąpienia kontenera, w zależności od tego, czy wiersza polecenia określa długotrwałe zadanie lub zadanie uruchamiane jednokrotnie. Na przykład zasady ponownego uruchamiania dla `Never` lub `OnFailure` jest zalecane w przypadku zadania uruchamiane jednokrotnie. 

* Jeśli potrzebujesz informacji na temat domyślnego punktu wejścia, w postaci obrazu kontenera, użyj [obrazu platformy docker Sprawdź](https://docs.docker.com/engine/reference/commandline/image_inspect/) polecenia.

## <a name="command-line-syntax"></a>Składnia wiersza polecenia

Składnia wiersza polecenia różni się zależnie od interfejsu API platformy Azure lub narzędzie służące do tworzenia wystąpienia. Jeśli określisz środowisko powłoki, również przestrzegać Konwencji składni polecenia powłoki.

* [Tworzenie kontenera az] [ az-container-create] polecenia: Przekaż ciągu za pomocą `--command-line` parametru. Przykład: `--command-line "python myscript.py arg1 arg2"`).

* [Nowy-AzureRmContainerGroup] [ new-azurermcontainergroup] polecenia cmdlet programu Azure PowerShell: Przekaż ciągu za pomocą `-Command` parametru. Przykład: `-Command "echo hello"`.

* Witryna Azure Portal: W **zastąpienia polecenia** właściwości konfiguracji kontenera zapewniają rozdzielaną przecinkami listę ciągów, bez znaków cudzysłowu. Przykład: `python, myscript.py, arg1, arg2`). 

* Szablon usługi Resource Manager lub pliku YAML lub jednego z zestawów SDK platformy Azure: Określ właściwość wiersza polecenia jako tablicę ciągów. Przykład: tablicę JSON `["python", "myscript.py", "arg1", "arg2"]` w szablonie usługi Resource Manager. 

  Jeśli znasz [pliku Dockerfile](https://docs.docker.com/engine/reference/builder/) składni, ten format jest podobny do *exec* formularza instrukcja CMD.

### <a name="examples"></a>Przykłady

|    |  Interfejs wiersza polecenia platformy Azure   | Portal | Szablon | 
| ---- | ---- | --- | --- |
| Jednego polecenia | `--command-line "python myscript.py arg1 arg2"` | **Polecenie zastąpienia**: `python, myscript.py, arg1, arg2` | `"command": ["python", "myscript.py", "arg1", "arg2"]` |
| Wiele poleceń | `--command-line "/bin/bash -c 'mkdir test; touch test/myfile; tail -f /dev/null'"` |**Polecenie zastąpienia**: `/bin/bash, -c, mkdir test; touch test/myfile; tail -f /dev/null` | `"command": ["/bin/bash", "-c", "mkdir test; touch test/myfile; tail -f /dev/null"]` |

## <a name="azure-cli-example"></a>Przykład interfejsu wiersza polecenia platformy Azure

Na przykład zmodyfikować zachowanie [microsoft/aci-wordcount] [ aci-wordcount] obrazu kontenera, który analizuje tekst w firmy Szekspir *osady* można znaleźć najczęściej występujących słów. Zamiast analizowanie *osady*, można ustawić wiersza polecenia, który wskazuje źródło inny tekst.

Aby wyświetlić dane wyjściowe z [microsoft/aci-wordcount] [ aci-wordcount] kontenera, kiedy analizuje ona domyślny tekst, uruchom go z następującymi [utworzyć kontener az] [ az-container-create] polecenia. Brak wiersza poleceń uruchamiania jest określony, aby domyślnego kontenera polecenia uruchamiania. Dla celów ilustracyjnych, w tym przykładzie [zmienne środowiskowe](container-instances-environment-variables.md) można znaleźć wyrazy pierwsze 3, które są co najmniej pięć litery długie:

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer1 \
    --image mcr.microsoft.com/azuredocs/aci-wordcount:latest \
    --environment-variables NumWords=3 MinLength=5 \
    --restart-policy OnFailure
```

Gdy stan kontenera jest wyświetlany jako *zwolniony* (Użyj [az container show] [ az-container-show] Aby sprawdzić stan), Wyświetl dziennik z [dzienniki kontenerów az] [ az-container-logs] Aby wyświetlić dane wyjściowe.

```azurecli-interactive
az container logs --resource-group myResourceGroup --name mycontainer1
```

Dane wyjściowe:

```console
[('HAMLET', 386), ('HORATIO', 127), ('CLAUDIUS', 120)]
```

Teraz skonfigurować drugi kontener przykład do analizowania tekstu różne, określając inną wiersza polecenia. Skrypt języka Python, wykonywane przez kontener, *wordcount.py*, akceptuje jako argument adresu URL i przetwarza tę stronę zawartości zamiast domyślnego.

Na przykład, aby określić u góry, 3 słów, które są co najmniej pięć Long litery *Romeo i Juliet*:

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer2 \
    --image mcr.microsoft.com/azuredocs/aci-wordcount:latest \
    --restart-policy OnFailure \
    --environment-variables NumWords=3 MinLength=5 \
    --command-line "python wordcount.py http://shakespeare.mit.edu/romeo_juliet/full.html"
```

Ponownie gdy kontener będzie *zwolniony*, aby wyświetlić dane wyjściowe, wyświetlanie dzienników kontenera:

```azurecli-interactive
az container logs --resource-group myResourceGroup --name mycontainer2
```

Dane wyjściowe:

```console
[('ROMEO', 177), ('JULIET', 134), ('CAPULET', 119)]
```

## <a name="next-steps"></a>Kolejne kroki

Oparta na zadaniach scenariuszach, na przykład wsadowo duży zestaw danych za pomocą kilku kontenerów mogą korzystać z niestandardowych wiersze poleceń w czasie wykonywania. Aby uzyskać więcej informacji na temat uruchamiania kontenerów opartych na zadaniach, zobacz [uruchamianie zadań konteneryzowanych za pomocą zasady ponownego uruchamiania](container-instances-restart-policy.md).

<!-- LINKS - External -->
[aci-wordcount]: https://hub.docker.com/_/microsoft-azuredocs-aci-wordcount

<!-- LINKS Internal -->
[az-container-create]: /cli/azure/container#az-container-create
[az-container-logs]: /cli/azure/container#az-container-logs
[az-container-show]: /cli/azure/container#az-container-show
[new-azurermcontainergroup]: /powershell/module/azurerm.containerinstance/new-azurermcontainergroup
[portal]: https://portal.azure.com
