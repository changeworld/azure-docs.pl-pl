---
title: Ustawianie zmiennych środowiskowych w usłudze Azure Container Instances
description: Dowiedz się, jak ustawić zmienne środowiskowe w kontenerach, uruchamianych w usłudze Azure Container Instances
services: container-instances
author: dlepow
ms.service: container-instances
ms.topic: article
ms.date: 04/15/2019
ms.author: danlep
ms.openlocfilehash: c311eea80c604366196a0725e4f9982bb43f8b5d
ms.sourcegitcommit: 5f348bf7d6cf8e074576c73055e17d7036982ddb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2019
ms.locfileid: "59606892"
---
# <a name="set-environment-variables-in-container-instances"></a>Ustawianie zmiennych środowiskowych w container instances

Ustawianie zmiennych środowiskowych w usługi container instances umożliwia przekazanie dynamiczną konfigurację aplikację lub skrypt uruchamiany przez kontener. Jest to podobne do `--env` argument wiersza polecenia, aby `docker run`. 

Aby ustawić zmienne środowiskowe w kontenerze, należy je określić podczas tworzenia wystąpienia kontenera. W tym artykule przedstawiono przykłady Ustawianie zmiennych środowiskowych podczas uruchamiania kontenera za pomocą [wiersza polecenia platformy Azure](#azure-cli-example), [programu Azure PowerShell](#azure-powershell-example)i [witryny Azure portal](#azure-portal-example). 

Na przykład po uruchomieniu programu Microsoft [aci wordcount] [ aci-wordcount] obraz kontenera, jego zachowanie można zmienić, określając następujące zmienne środowiskowe:

*NumWords*: Liczba słów wysyłane do strumienia wyjściowego STDOUT.

*Element MinLength*: Minimalna liczba znaków w słowie na jej do zliczenia. Większa liczba zostanie podana ignoruje popularne wyrazy, takie jak "z" i "".

Jeśli musisz przekazać wpisów tajnych jako zmienne środowiskowe usługi Azure Container Instances obsługuje [secure wartości](#secure-values) dla kontenerów systemów Windows i Linux.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="azure-cli-example"></a>Przykład interfejsu wiersza polecenia platformy Azure

Aby wyświetlić domyślne dane wyjściowe z [aci wordcount] [ aci-wordcount] kontener, uruchom go z tym [utworzyć kontener az] [ az-container-create] polecenie (nie zmienne środowiskowe określone):

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer1 \
    --image mcr.microsoft.com/azuredocs/aci-wordcount:latest \
    --restart-policy OnFailure
```

Aby zmodyfikować dane wyjściowe, uruchom drugi kontener z `--environment-variables` argument dodane, określanie wartości dla *NumWords* i *MinLength* zmiennych. (W tym przykładzie przyjęto założenie, że używany jest interfejs wiersza polecenia powłoki Bash lub programu Azure Cloud Shell. Korzystając z wiersza polecenia Windows, określ zmienne przy użyciu podwójnych cudzysłowów, takich jak `--environment-variables "NumWords"="5" "MinLength"="8"`.)

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer2 \
    --image mcr.microsoft.com/azuredocs/aci-wordcount:latest \
    --restart-policy OnFailure \
    --environment-variables 'NumWords'='5' 'MinLength'='8'
```

Po obu kontenery stan jest wyświetlany jako *zwolniony* (Użyj [az container show] [ az-container-show] Aby sprawdzić stan), wyświetlenia ich dzienników za pomocą [dzienniki kontenerów az] [ az-container-logs] Aby wyświetlić dane wyjściowe.

```azurecli-interactive
az container logs --resource-group myResourceGroup --name mycontainer1
az container logs --resource-group myResourceGroup --name mycontainer2
```

Dane wyjściowe kontenery pokazują, jak zostały zmodyfikowane drugiego kontenera skryptu zachowanie przez ustawienie zmiennych środowiskowych.

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

## <a name="azure-powershell-example"></a>Przykład programu PowerShell Azure

Ustawianie zmiennych środowiskowych w programie PowerShell jest podobny do interfejsu wiersza polecenia, ale używa `-EnvironmentVariable` argument wiersza polecenia.

Najpierw uruchom [aci wordcount] [ aci-wordcount] kontenera w konfiguracji domyślnej z tym [New AzContainerGroup] [ new-Azcontainergroup] polecenia:

```azurepowershell-interactive
New-AzContainerGroup `
    -ResourceGroupName myResourceGroup `
    -Name mycontainer1 `
    -Image mcr.microsoft.com/azuredocs/aci-wordcount:latest
```

Teraz uruchom następujące polecenie [New AzContainerGroup] [ new-Azcontainergroup] polecenia. Ta określa *NumWords* i *MinLength* zmienne środowiskowe po wypełnieniu zmienną tablicową `envVars`:

```azurepowershell-interactive
$envVars = @{'NumWords'='5';'MinLength'='8'}
New-AzContainerGroup `
    -ResourceGroupName myResourceGroup `
    -Name mycontainer2 `
    -Image mcr.microsoft.com/azuredocs/aci-wordcount:latest `
    -RestartPolicy OnFailure `
    -EnvironmentVariable $envVars
```

Po stanie zarówno kontenery *zwolniony* (Użyj [Get AzContainerInstanceLog] [ azure-instance-log] Aby sprawdzić stan), ściąganie dzienników przy użyciu [ Get-AzContainerInstanceLog] [ azure-instance-log] polecenia.

```azurepowershell-interactive
Get-AzContainerInstanceLog -ResourceGroupName myResourceGroup -ContainerGroupName mycontainer1
Get-AzContainerInstanceLog -ResourceGroupName myResourceGroup -ContainerGroupName mycontainer2
```

Dane wyjściowe dla każdego kontenera pokazuje, jak został zmodyfikowany skrypt uruchamiany przez kontener przez ustawienie zmiennych środowiskowych.

```console
PS Azure:\> Get-AzContainerInstanceLog -ResourceGroupName myResourceGroup -ContainerGroupName mycontainer1
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
PS Azure:\> Get-AzContainerInstanceLog -ResourceGroupName myResourceGroup -ContainerGroupName mycontainer2
[('CLAUDIUS', 120),
 ('POLONIUS', 113),
 ('GERTRUDE', 82),
 ('ROSENCRANTZ', 69),
 ('GUILDENSTERN', 54)]

Azure:\
```

## <a name="azure-portal-example"></a>Przykład portalu Azure

Do ustawiania zmiennych środowiskowych podczas uruchamiania kontenera w witrynie Azure portal, określ je w **konfiguracji** stronie podczas tworzenia kontenera.

Podczas wdrażania za pomocą portalu, jest obecnie ograniczona do trzech zmiennych i należy wprowadzić je w następującym formacie: `"variableName":"value"`

Aby zobaczyć przykład, uruchom [aci wordcount] [ aci-wordcount] kontener za pomocą *NumWords* i *MinLength* zmiennych.

1. W **konfiguracji**ustaw **zasady ponownego uruchamiania** do *w przypadku niepowodzenia*
2. Wprowadź `"NumWords":"5"` pierwszej zmiennej wybierz **tak** w obszarze **Dodaj dodatkowe zmienne środowiskowe**, a następnie wprowadź `"MinLength":"8"` drugiej zmiennej. Wybierz **OK** sprawdzić, a następnie wdrożyć kontener.

![Strona Portal przedstawiający środowiska zmiennej włączenie przycisku i pól tekstowych][portal-env-vars-01]

Aby wyświetlić dzienniki kontenera, w obszarze **ustawienia** wybierz **kontenery**, następnie **dzienniki**. Podobnie jak dane wyjściowe wyświetlane w poprzedniej interfejsu wiersza polecenia i programu PowerShell sekcjach, możesz zobaczyć, jak zachowanie przez skrypt został zmodyfikowany przez zmienne środowiskowe. Wyświetlane są tylko pięć słów, każdy z długość co najmniej osiem znaków.

![Dane wyjściowe dziennika kontenera Portal przedstawiający][portal-env-vars-02]

## <a name="secure-values"></a>Zabezpieczanie wartości

Obiekty z bezpiecznych wartości są przeznaczone do przechowywania informacji poufnych, takich jak hasła lub kluczy dla aplikacji. Przy użyciu bezpiecznych wartości zmiennych środowiskowych jest bezpieczniejsze i bardziej elastyczne niż go w tym obrazie z kontenerem. Innym rozwiązaniem jest użycie woluminy tajne opisanego w [zainstalować wolumin tajny w usłudze Azure Container Instances](container-instances-volume-secret.md).

Zmienne środowiskowe z bezpiecznych wartości nie są widoczne we właściwościach usługi kontenera — ich wartości są dostępne tylko z w ramach kontenera. Na przykład kontener wyświetlić właściwości w witrynie Azure portal lub obraz wyświetlonego wiersza polecenia platformy Azure tylko nazwę zmiennej bezpieczny, nie jego wartość.

Ustaw zmienną środowiskową bezpieczny, określając `secureValue` zamiast zwykłych właściwości `value` dla typu zmiennej. Dwie zmienne zdefiniowane w poniższego kodu YAML pokazują dwa typy zmiennych.

### <a name="yaml-deployment"></a>Wdrożenie YAML

Utwórz `secure-env.yaml` pliku następującym fragmentem kodu.

```yaml
apiVersion: 2018-10-01
location: eastus
name: securetest
properties:
  containers:
  - name: mycontainer
    properties:
      environmentVariables:
        - name: 'NOTSECRET'
          value: 'my-exposed-value'
        - name: 'SECRET'
          secureValue: 'my-secret-value'
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

Uruchom następujące polecenie, aby wdrożyć grupę kontenerów, z YAML (zmienić nazwę grupy zasobów zgodnie z potrzebami):

```azurecli-interactive
az container create --resource-group myResourceGroup --file secure-env.yaml
```

### <a name="verify-environment-variables"></a>Sprawdź zmienne środowiskowe

Uruchom [az container show] [ az-container-show] polecenie, aby zbadać zmienne środowiskowe przez kontener:

```azurecli-interactive
az container show --resource-group myResourceGroup --name securetest --query 'containers[].environmentVariables'
```

Odpowiedź JSON zawiera zmienną środowiskową niezabezpieczonego klucza i wartości, ale tylko nazwę zmiennej środowiskowej bezpiecznego:

```json
[
  [
    {
      "name": "NOTSECRET",
      "secureValue": null,
      "value": "my-exposed-value"
    },
    {
      "name": "SECRET",
      "secureValue": null,
      "value": null
    }
  ]
]
```

Za pomocą [az container exec] [ az-container-exec] polecenia, które umożliwia wykonywanie polecenia w kontenerze uruchomiona, można sprawdzić, czy została ustawiona zmienna bezpieczne środowisko. Uruchom następujące polecenie, aby uruchomić sesję interaktywnych funkcji bash w kontenerze:

```azurecli-interactive
az container exec --resource-group myResourceGroup --name securetest --exec-command "/bin/bash"
```

Po otwarciu interaktywnej powłoki, w tym kontenerze, możesz uzyskać dostęp `SECRET` wartość zmiennej:

```console
root@caas-ef3ee231482549629ac8a40c0d3807fd-3881559887-5374l:/# echo $SECRET
my-secret-value
```

## <a name="next-steps"></a>Kolejne kroki

Oparta na zadaniach scenariuszach, na przykład wsadowo duży zestaw danych za pomocą kilku kontenerów mogą korzystać z niestandardowych zmiennych środowiskowych w czasie wykonywania. Aby uzyskać więcej informacji na temat uruchamiania kontenerów opartych na zadaniach, zobacz [uruchamianie zadań konteneryzowanych za pomocą zasady ponownego uruchamiania](container-instances-restart-policy.md).

<!-- IMAGES -->
[portal-env-vars-01]: ./media/container-instances-environment-variables/portal-env-vars-01.png
[portal-env-vars-02]: ./media/container-instances-environment-variables/portal-env-vars-02.png

<!-- LINKS - External -->
[aci-wordcount]: https://hub.docker.com/_/microsoft-azuredocs-aci-wordcount

<!-- LINKS Internal -->
[az-container-create]: /cli/azure/container#az-container-create
[az-container-exec]: /cli/azure/container#az-container-exec
[az-container-logs]: /cli/azure/container#az-container-logs
[az-container-show]: /cli/azure/container#az-container-show
[azure-cli-install]: /cli/azure/
[azure-instance-log]: /powershell/module/az.containerinstance/get-azcontainerinstancelog
[azure-powershell-install]: /powershell/azure/install-Az-ps
[new-Azcontainergroup]: /powershell/module/az.containerinstance/new-azcontainergroup
[portal]: https://portal.azure.com
