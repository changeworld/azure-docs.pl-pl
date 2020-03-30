---
title: Zastępowanie punktu wejścia w wystąpieniu kontenera
description: Ustawianie wiersza polecenia w celu zastąpienia punktu wejścia w obrazie kontenera podczas wdrażania wystąpienia kontenera platformy Azure
ms.topic: article
ms.date: 04/15/2019
ms.openlocfilehash: d9554603f78a07fa44af51d8f39a91e1b3c39f70
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79247126"
---
# <a name="set-the-command-line-in-a-container-instance-to-override-the-default-command-line-operation"></a>Ustawianie wiersza polecenia w wystąpieniu kontenera w celu zastąpienia domyślnej operacji wiersza polecenia

Podczas tworzenia wystąpienia kontenera opcjonalnie należy określić polecenie zastępowania domyślnej instrukcji wiersza polecenia zapłodnieną w obrazie kontenera. To zachowanie jest `--entrypoint` podobne do argumentu wiersza polecenia do `docker run`.

Podobnie jak [ustawianie zmiennych środowiskowych](container-instances-environment-variables.md) dla wystąpień kontenera, określenie początkowego wiersza polecenia jest przydatne w przypadku zadań wsadowych, w których należy dynamicznie przygotować każdy kontener za pomocą konfiguracji specyficznej dla zadania.

## <a name="command-line-guidelines"></a>Wskazówki dotyczące wiersza polecenia

* Domyślnie wiersz polecenia określa *pojedynczy proces, który rozpoczyna się bez powłoki* w kontenerze. Na przykład wiersz polecenia może uruchomić skrypt języka Python lub plik wykonywalny. Proces można określić dodatkowe parametry lub argumenty.

* Aby wykonać wiele poleceń, należy rozpocząć wiersz polecenia, ustawiając środowisko powłoki, które jest obsługiwane w systemie operacyjnym kontenera. Przykłady:

  |System operacyjny  |Domyślna powłoka  |
  |---------|---------|
  |Ubuntu     |   `/bin/bash`      |
  |Alpejskie     |   `/bin/sh`      |
  |Windows     |    `cmd`     |

  Postępuj zgodnie z konwencjami powłoki, aby połączyć wiele poleceń do uruchomienia w sekwencji.

* W zależności od konfiguracji kontenera może być konieczne ustawienie pełnej ścieżki do pliku wykonywalnego wiersza polecenia lub argumentów.

* Ustaw odpowiednie [zasady ponownego uruchamiania](container-instances-restart-policy.md) dla wystąpienia kontenera, w zależności od tego, czy wiersz polecenia określa długotrwałe zadanie, czy zadanie jednorazowe. Na przykład zasady ponownego `Never` `OnFailure` uruchamiania lub jest zalecane dla zadania jednorazowego uruchomienia. 

* Jeśli potrzebujesz informacji o domyślnym punkcie wejścia ustawionym w obrazie kontenera, użyj polecenia [inspekcji obrazu docker.](https://docs.docker.com/engine/reference/commandline/image_inspect/)

## <a name="command-line-syntax"></a>Składnia wiersza polecenia

Składnia wiersza polecenia różni się w zależności od interfejsu API platformy Azure lub narzędzia używanego do tworzenia wystąpień. Jeśli określisz środowisko powłoki, należy również przestrzegać konwencji składni polecenia powłoki.

* [polecenie tworzenia kontenera az:][az-container-create] `--command-line` Przekaż ciąg z parametrem. Przykład: `--command-line "python myscript.py arg1 arg2"`).

* [Nowa grupa AzureRmContainer][new-azurermcontainergroup] Polecenie cmdlet programu Azure PowerShell: Przekaż ciąg z parametrem. `-Command` Przykład: `-Command "echo hello"`.

* Azure portal: W **command zastąpić** właściwość konfiguracji kontenera, podaj listę ciągów oddzielone przecinkami, bez cudzysłowów. Przykład: `python, myscript.py, arg1, arg2`). 

* Szablon Menedżera zasobów lub plik YAML lub jeden z zestawów SDK platformy Azure: Określ właściwość wiersza polecenia jako tablicę ciągów. Przykład: tablica `["python", "myscript.py", "arg1", "arg2"]` JSON w szablonie Menedżera zasobów. 

  Jeśli znasz składnię [Dockerfile,](https://docs.docker.com/engine/reference/builder/) ten format jest podobny do *exec* formularza instrukcji CMD.

### <a name="examples"></a>Przykłady

|    |  Interfejs wiersza polecenia platformy Azure   | Portal | Szablon | 
| ---- | ---- | --- | --- |
| Pojedyncze polecenie | `--command-line "python myscript.py arg1 arg2"` | **Zastępowanie polecenia:**`python, myscript.py, arg1, arg2` | `"command": ["python", "myscript.py", "arg1", "arg2"]` |
| Wiele poleceń | `--command-line "/bin/bash -c 'mkdir test; touch test/myfile; tail -f /dev/null'"` |**Zastępowanie polecenia:**`/bin/bash, -c, mkdir test; touch test/myfile; tail -f /dev/null` | `"command": ["/bin/bash", "-c", "mkdir test; touch test/myfile; tail -f /dev/null"]` |

## <a name="azure-cli-example"></a>Przykład interfejsu wiersza polecenia platformy Azure

Na przykład zmodyfikuj zachowanie obrazu kontenera [microsoft/aci-wordcount,][aci-wordcount] który analizuje tekst w *Hamlecie* Szekspira, aby znaleźć najczęściej występujące słowa. Zamiast analizować *Hamleta,* można ustawić wiersz polecenia wskazujący inne źródło tekstu.

Aby wyświetlić dane wyjściowe kontenera [microsoft/aci-wordcount][aci-wordcount] podczas analizowania tekstu domyślnego, uruchom go za pomocą następującego polecenia [az container create.][az-container-create] Nie określono wiersza polecenia start, więc uruchamia się domyślne polecenie kontenera. Na potrzeby ilustracji w tym przykładzie ustawia [zmienne środowiskowe,](container-instances-environment-variables.md) aby znaleźć 3 najlepsze wyrazy o długości co najmniej pięciu liter:

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer1 \
    --image mcr.microsoft.com/azuredocs/aci-wordcount:latest \
    --environment-variables NumWords=3 MinLength=5 \
    --restart-policy OnFailure
```

Gdy stan kontenera pokazuje jako *Zakończone* (użyj [az container show,][az-container-show] aby sprawdzić stan), wyświetlić dziennik z [dzienników kontenera az,][az-container-logs] aby zobaczyć dane wyjściowe.

```azurecli-interactive
az container logs --resource-group myResourceGroup --name mycontainer1
```

Dane wyjściowe:

```console
[('HAMLET', 386), ('HORATIO', 127), ('CLAUDIUS', 120)]
```

Teraz skonfiguruj drugi przykładowy kontener do analizowania innego tekstu, określając inny wiersz polecenia. Skrypt języka Python wykonywany przez *kontener, wordcount.py*, akceptuje adres URL jako argument i przetwarza zawartość tej strony zamiast domyślnej.

Na przykład, aby określić 3 najlepsze słowa, które są co najmniej pięć liter długości w *Romeo i Julia:*

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer2 \
    --image mcr.microsoft.com/azuredocs/aci-wordcount:latest \
    --restart-policy OnFailure \
    --environment-variables NumWords=3 MinLength=5 \
    --command-line "python wordcount.py http://shakespeare.mit.edu/romeo_juliet/full.html"
```

Ponownie, gdy kontener zostanie *zakończony,* wyświetl dane wyjściowe, pokazując dzienniki kontenera:

```azurecli-interactive
az container logs --resource-group myResourceGroup --name mycontainer2
```

Dane wyjściowe:

```console
[('ROMEO', 177), ('JULIET', 134), ('CAPULET', 119)]
```

## <a name="next-steps"></a>Następne kroki

Scenariusze oparte na zadaniach, takie jak przetwarzanie wsadowe dużego zestawu danych z kilkoma kontenerami, mogą korzystać z niestandardowych wierszy poleceń w czasie wykonywania. Aby uzyskać więcej informacji na temat uruchamiania kontenerów opartych na zadaniach, zobacz [Uruchamianie zadań konteneryzowanych z zasadami ponownego uruchamiania](container-instances-restart-policy.md).

<!-- LINKS - External -->
[aci-wordcount]: https://hub.docker.com/_/microsoft-azuredocs-aci-wordcount

<!-- LINKS Internal -->
[az-container-create]: /cli/azure/container#az-container-create
[az-container-logs]: /cli/azure/container#az-container-logs
[az-container-show]: /cli/azure/container#az-container-show
[new-azurermcontainergroup]: /powershell/module/azurerm.containerinstance/new-azurermcontainergroup
[portal]: https://portal.azure.com
