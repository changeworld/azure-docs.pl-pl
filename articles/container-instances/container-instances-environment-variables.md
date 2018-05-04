---
title: Ustaw zmienne środowiskowe w wystąpień kontenera platformy Azure
description: Dowiedz się, jak ustawić zmienne środowiskowe w wystąpień kontenera platformy Azure
services: container-instances
author: david-stanford
manager: jeconnoc
ms.service: container-instances
ms.topic: article
ms.date: 03/13/2018
ms.author: dastanfo
ms.openlocfilehash: 37fde41b6dc2ea0a4d3b4b38a0e3df81a297c125
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2018
---
# <a name="set-environment-variables"></a>Ustawianie zmiennych środowiskowych

Ustawianie zmiennych środowiskowych w swoich wystąpień kontenera umożliwia podanie dynamicznej konfiguracji aplikacji lub skrypt uruchamiany przez kontener.

Obecnie możesz ustawić zmienne środowiskowe z poziomu interfejsu wiersza polecenia i programu PowerShell. W obu przypadkach czy Użyj flagi poleceń, aby ustawić zmienne środowiskowe. Ustawianie zmiennych środowiskowych w ACI jest podobny do `--env` argumentu wiersza polecenia do `docker run`. Na przykład jeśli używasz obrazu kontenera microsoft/aci-wordcount zachowanie można zmienić, określając następujące zmienne środowiskowe:

*NumWords*: liczba wysłanych do STDOUT słów.

*Element MinLength*: minimalna liczba znaków w wyrazie go do zliczenia. Większa liczba ignoruje popularnych wyrazów, takich jak "z" i "".

## <a name="azure-cli-example"></a>Przykład interfejsu wiersza polecenia platformy Azure

Aby wyświetlić dane wyjściowe domyślnego kontenera uruchom następujące polecenie:

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer1 \
    --image microsoft/aci-wordcount:latest \
    --restart-policy OnFailure
```

Określając `NumWords=5` i `MinLength=8` zmiennych środowiskowych kontenera, dzienniki kontenera powinien być wyświetlany różnych danych wyjściowych.

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer2 \
    --image microsoft/aci-wordcount:latest \
    --restart-policy OnFailure \
    --environment-variables NumWords=5 MinLength=8
```

Po stan kontenera to *zwolniony* (Użyj [Pokaż kontenera az] [ az-container-show] sprawdź jej status), wyświetlić jego dzienniki, aby wyświetlić dane wyjściowe.  Aby wyświetlić dane wyjściowe z nie ustawiono zmiennych środowiska — nazwa ma zostać mycontainer1 zamiast mycontainer2 kontenera.

```azurecli-interactive
az container logs --resource-group myResourceGroup --name mycontainer2
```

## <a name="azure-powershell-example"></a>Przykład programu PowerShell systemu Azure

Aby wyświetlić dane wyjściowe domyślnego kontenera uruchom następujące polecenie:

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer1 \
    --image microsoft/aci-wordcount:latest \
    --restart-policy OnFailure
```

Określając `NumWords=5` i `MinLength=8` zmiennych środowiskowych kontenera, dzienniki kontenera powinien być wyświetlany różnych danych wyjściowych.

```azurepowershell-interactive
$envVars = @{NumWord=5;MinLength=8}
New-AzureRmContainerGroup `
    -ResourceGroupName myResourceGroup `
    -Name mycontainer2 `
    -Image microsoft/aci-wordcount:latest `
    -RestartPolicy OnFailure `
    -EnvironmentVariable $envVars
```

Po stan kontenera *zwolniony* (Użyj [Get-AzureRmContainerInstanceLog] [ azure-instance-log] sprawdź jej status), wyświetl jego dzienniki, aby wyświetlić dane wyjściowe. Aby wyświetlić kontenera dzienniki nie zmiennych środowiskowych ustawić ContainerGroupName jako mycontainer1 zamiast mycontainer2.

```azurepowershell-interactive
Get-AzureRmContainerInstanceLog `
    -ResourceGroupName myResourceGroup `
    -ContainerGroupName mycontainer2
```

## <a name="example-output-without-environment-variables"></a>Przykładowe dane wyjściowe bez zmienne środowiskowe

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

## <a name="example-output-with-environment-variables"></a>Przykładowe dane wyjściowe z zmienne środowiskowe

```bash
[('CLAUDIUS', 120),
 ('POLONIUS', 113),
 ('GERTRUDE', 82),
 ('ROSENCRANTZ', 69),
 ('GUILDENSTERN', 54)]
```

## <a name="next-steps"></a>Kolejne kroki

Teraz, gdy wiesz, jak dostosować dane wejściowe użytkownika kontenera, Dowiedz się, jak zachować dane wyjściowe kontenerów, które mogły zostać wykonane.
> [!div class="nextstepaction"]
> [Instalowanie udziału plików na platformę Azure z wystąpień kontenera platformy Azure](container-instances-mounting-azure-files-volume.md)

<!-- LINKS Internal -->
[azure-cloud-shell]: ../cloud-shell/overview.md
[azure-cli-install]: /cli/azure/
[azure-powershell-install]: /powershell/azure/install-azurerm-ps
[azure-instance-log]: /powershell/module/azurerm.containerinstance/get-azurermcontainerinstancelog
[az-container-show]: /cli/azure/container?view=azure-cli-latest#az_container_show