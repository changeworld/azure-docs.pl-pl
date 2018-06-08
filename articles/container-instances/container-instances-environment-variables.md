---
title: Ustaw zmienne środowiskowe w wystąpień kontenera platformy Azure
description: Dowiedz się, jak ustawić zmienne środowiskowe w kontenerach, po uruchomieniu w wystąpień kontenera platformy Azure
services: container-instances
author: mmacy
manager: jeconnoc
ms.service: container-instances
ms.topic: article
ms.date: 06/07/2018
ms.author: marsma
ms.openlocfilehash: bc30352f50344031f8356d2be1b800dd035f12ad
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/07/2018
ms.locfileid: "34830466"
---
# <a name="set-environment-variables"></a>Ustawianie zmiennych środowiskowych

Ustawianie zmiennych środowiskowych w swoich wystąpień kontenera umożliwia podanie dynamicznej konfiguracji aplikacji lub skrypt uruchamiany przez kontener. Aby ustawić zmienne środowiskowe w kontenerze, należy je określić podczas tworzenia wystąpienia kontenera. Można ustawić zmienne środowiskowe, po ponownym uruchomieniu kontener z [interfejsu wiersza polecenia Azure](#azure-cli-example), [programu Azure PowerShell](#azure-powershell-example)i [portalu Azure](#azure-portal-example).

Na przykład, jeśli uruchomisz [microsoft/aci-wordcount] [ aci-wordcount] kontener obrazu, jego zachowanie można zmienić, określając następujące zmienne środowiskowe:

*NumWords*: liczba wysłanych do STDOUT słów.

*Element MinLength*: minimalna liczba znaków w wyrazie go do zliczenia. Większa liczba ignoruje popularnych wyrazów, takich jak "z" i "".

Jeśli chcesz przekazać kluczy tajnych jako zmienne środowiskowe wystąpień kontenera Azure obsługuje [secure wartości](#secure-values) secure wartości dla kontenerów w systemach Windows i Linux.

## <a name="azure-cli-example"></a>Przykład interfejsu wiersza polecenia platformy Azure

Aby wyświetlić dane wyjściowe domyślne [microsoft/aci-wordcount] [ aci-wordcount] kontenera, uruchom go z tym [utworzyć kontener az] [ az-container-create] polecenie (nie zmienne środowiskowe określony):

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer1 \
    --image microsoft/aci-wordcount:latest \
    --restart-policy OnFailure
```

Aby zmodyfikować dane wyjściowe, uruchom drugi kontener z `--environment-variables` argument dodany, określania wartości dla *NumWords* i *MinLength* zmiennych:

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer2 \
    --image microsoft/aci-wordcount:latest \
    --restart-policy OnFailure \
    --environment-variables NumWords=5 MinLength=8
```

Po obu kontenery stan jest wyświetlany jako *zwolniony* (Użyj [Pokaż kontenera az] [ az-container-show] można sprawdzić stanu), wyświetlanie dzienników z [dzienniki kontenera az] [ az-container-logs] aby zobaczyć dane wyjściowe.

```azurecli-interactive
az container logs --resource-group myResourceGroup --name mycontainer1
az container logs --resource-group myResourceGroup --name mycontainer2
```

Dane wyjściowe z kontenerów pokazują, jak zmodyfikowano kontenera drugi skrypt zachowanie przez ustawienie zmiennych środowiskowych.

```console
azureuser@Azure:~$ az container logs --resource-group myResourceGroup --name mycontainer1
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

azureuser@Azure:~$ az container logs --resource-group myResourceGroup --name mycontainer2
[('CLAUDIUS', 120),
 ('POLONIUS', 113),
 ('GERTRUDE', 82),
 ('ROSENCRANTZ', 69),
 ('GUILDENSTERN', 54)]
```

## <a name="azure-powershell-example"></a>Przykład programu PowerShell systemu Azure

Ustawianie zmiennych środowiskowych w programie PowerShell jest podobny do interfejsu wiersza polecenia, ale używa `-EnvironmentVariable` argumentu wiersza polecenia.

Najpierw należy uruchomić [microsoft/aci-wordcount] [ aci-wordcount] kontenera w konfiguracji domyślnej z tym [AzureRmContainerGroup nowy] [ new-azurermcontainergroup]polecenia:

```azurepowershell-interactive
New-AzureRmContainerGroup `
    -ResourceGroupName myResourceGroup `
    -Name mycontainer1 `
    -Image microsoft/aci-wordcount:latest
```

Teraz uruchom następujące polecenie [AzureRmContainerGroup nowy] [ new-azurermcontainergroup] polecenia. Ta określa *NumWords* i *MinLength* zmiennych środowiskowych po wypełnieniu zmienną tablicową `envVars`:

```azurepowershell-interactive
$envVars = @{NumWords=5;MinLength=8}
New-AzureRmContainerGroup `
    -ResourceGroupName myResourceGroup `
    -Name mycontainer2 `
    -Image microsoft/aci-wordcount:latest `
    -RestartPolicy OnFailure `
    -EnvironmentVariable $envVars
```

Po obu kontenery stanu *zwolniony* (Użyj [Get-AzureRmContainerInstanceLog] [ azure-instance-log] można sprawdzić stanu), pobierania dzienników z [ Get-AzureRmContainerInstanceLog] [ azure-instance-log] polecenia.

```azurepowershell-interactive
Get-AzureRmContainerInstanceLog -ResourceGroupName myResourceGroup -ContainerGroupName mycontainer1
Get-AzureRmContainerInstanceLog -ResourceGroupName myResourceGroup -ContainerGroupName mycontainer2
```

Dane wyjściowe dla każdego kontenera pokazuje, jak zmodyfikowano skryptu Uruchom przez kontener przez ustawienie zmiennych środowiskowych.

```console
PS Azure:\> Get-AzureRmContainerInstanceLog -ResourceGroupName myResourceGroup -ContainerGroupName mycontainer1
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

Azure:\
PS Azure:\> Get-AzureRmContainerInstanceLog -ResourceGroupName myResourceGroup -ContainerGroupName mycontainer2
[('CLAUDIUS', 120),
 ('POLONIUS', 113),
 ('GERTRUDE', 82),
 ('ROSENCRANTZ', 69),
 ('GUILDENSTERN', 54)]

Azure:\
```

## <a name="azure-portal-example"></a>Przykład portalu Azure

Aby ustawić zmienne środowiskowe, po uruchomieniu kontener w portalu Azure, określ je w **konfiguracji** strony po utworzeniu kontenera.

Podczas wdrażania przy użyciu portalu jest obecnie ograniczona do trzech zmiennych i należy je wprowadzić w następującym formacie: `"variableName":"value"`

Aby zapoznać się przykładem, należy uruchomić [microsoft/aci-wordcount] [ aci-wordcount] kontener z *NumWords* i *MinLength* zmiennych.

1. W **konfiguracji**ustaw **ponowne uruchomienie zasad** do *w przypadku awarii*
2. Wprowadź `"NumWords":"5"` pierwszej zmiennej, wybierz **tak** w obszarze **dodać dodatkowe zmienne środowiskowe**, a następnie wprowadź `"MinLength":"8"` drugiej zmiennej. Wybierz **OK** Aby sprawdzić, a następnie wdrożyć kontenera.

![Strony portalu przedstawiający środowiska zmiennej Włącz przycisk i pola tekstowe][portal-env-vars-01]

Aby wyświetlić dzienniki kontenera, w obszarze **ustawienia** wybierz **kontenery**, następnie **dzienniki**. Podobny do danych wyjściowych pokazano w poprzednim interfejsu wiersza polecenia i programu PowerShell sekcje, widać, jak zachowanie skryptu został zmodyfikowany przez zmiennych środowiskowych. Wyświetlane są tylko pięć wyrazy, każde z nich długość co najmniej osiem znaków.

![Dane wyjściowe dziennika kontenera przedstawiający portalu][portal-env-vars-02]

## <a name="secure-values"></a>Bezpieczne wartości
Obiekty o wartości bezpiecznego są przeznaczone do przechowywania poufnych informacji, takich jak hasła lub kluczy dla aplikacji. Przy użyciu bezpiecznego wartości zmiennych środowiskowych jest bezpieczniejsze i bardziej elastyczne niż uwzględniania go w obrazie z kontenera. Inną opcją jest użycie woluminy poufną, opisane w [zainstalować tajny woluminu w wystąpień kontenera Azure](container-instances-volume-secret.md).

Zmienne środowiskowe bezpiecznego wartościami bezpiecznego nie ujawnić bezpiecznego wartość właściwości z kontenera, więc wartość można uzyskać tylko z Twojej kontenera. Na przykład wyświetlić właściwości kontenera w portalu Azure lub interfejsu wiersza polecenia Azure nie można wyświetlić zmienną środowiskową o wartości bezpieczne.

Ustaw zmienną środowiskową bezpieczny, określając `secureValue` zamiast zwykłej właściwości `value` dla typu zmiennej. Dwie zmienne zdefiniowane w następujących yaml programu pokazują dwa typy zmiennych.

### <a name="yaml-deployment"></a>Wdrożenie yaml programu

Utwórz `secure-env.yaml` pliku z następującego fragmentu kodu.

```yaml
apiVersion: 2018-06-01
location: westus
name: securetest
properties:
  containers:
  - name: mycontainer
    properties:
      environmentVariables:
        - "name": "SECRET"
          "secureValue": "my-secret-value"
        - "name": "NOTSECRET"
          "value": "my-exposed-value"
      image: nginx
      ports: []
      resources:
        requests:
          cpu: 1.0
          memoryInGB: 1.5
  osType: Linux
  restartPolicy: Always
tags: null
type: Microsoft.ContainerInstance/containerGroups
```

Uruchom następujące polecenie, aby wdrożyć grupę kontenera z yaml programu.

```azurecli-interactive
az container create --resource-group myRG --name securetest -f secure-env.yaml
```

### <a name="verify-environment-variables"></a>Sprawdź zmienne środowiskowe

Uruchom następujące polecenie, aby zapytania z kontenera zmiennych środowiskowych.

```azurecli-interactive
az container show --resource-group myRG --name securetest --query 'containers[].environmentVariables`
```

Odpowiedź w formacie JSON przy użyciu szczegółów dla tego kontenera będzie Pokaż tylko zmiennej środowiskowej niezabezpieczonego i zabezpieczyć klucz zmiennej środowiskowej.

```json
  "environmentVariables": [
    {
      "name": "NOTSECRET",
      "value": "my-exposed-value"
    },
    {
      "name": "SECRET"
    }
```

Możesz przejrzeć bezpieczny ustawiono zmiennej środowiskowej `exec` polecenia, co umożliwia, wykonując polecenie z kontenera uruchomione. 

Uruchom następujące polecenie, aby uruchomić sesję interakcyjne bash z kontenera.
```azurecli-interactive
az container exec --resource-group myRG --name securetest --exec-command "/bin/bash"
```

W ramach Twojej kontenera wydrukować Twojej zmiennej środowiskowej przy użyciu następującego polecenia bash.
```bash
echo $SECRET
```

## <a name="next-steps"></a>Kolejne kroki

Scenariusze opartego na zadaniach, takie jak dużego zestawu danych z wielu kontenerów przetwarzania wsadowego mogą korzystać z zmiennych środowiskowych niestandardowych w czasie wykonywania. Aby uzyskać więcej informacji o uruchomionych kontenerów opartego na zadaniach, zobacz [uruchamiać zadania konteneryzowanych wystąpień kontenera Azure](container-instances-restart-policy.md).

<!-- IMAGES -->
[portal-env-vars-01]: ./media/container-instances-environment-variables/portal-env-vars-01.png
[portal-env-vars-02]: ./media/container-instances-environment-variables/portal-env-vars-02.png

<!-- LINKS - External -->
[aci-wordcount]: https://hub.docker.com/r/microsoft/aci-wordcount/

<!-- LINKS Internal -->
[az-container-create]: /cli/azure/container#az-container-create
[az-container-logs]: /cli/azure/container#az-container-logs
[az-container-show]: /cli/azure/container#az-container-show
[azure-cli-install]: /cli/azure/
[azure-instance-log]: /powershell/module/azurerm.containerinstance/get-azurermcontainerinstancelog
[azure-powershell-install]: /powershell/azure/install-azurerm-ps
[new-azurermcontainergroup]: /powershell/module/azurerm.containerinstance/new-azurermcontainergroup
[portal]: https://portal.azure.com
