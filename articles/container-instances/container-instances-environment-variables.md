---
title: Ustawianie zmiennych środowiskowych w wystąpieniu kontenera
description: Dowiedz się, jak ustawić zmienne środowiskowe w kontenerach uruchamianych w wystąpieniach kontenerów platformy Azure
ms.topic: article
ms.date: 04/17/2019
ms.openlocfilehash: c3c76ba0c6131a8ab3de68c13c9dfddaf7e8749a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79247230"
---
# <a name="set-environment-variables-in-container-instances"></a>Ustawianie zmiennych środowiskowych w wystąpieniach kontenera

Ustawienie zmiennych środowiskowych w wystąpieniach kontenerów umożliwia udostępnienie dynamicznej konfiguracji aplikacji lub skryptu uruchamianego przez kontener. Jest to podobne `--env` do argumentu `docker run`wiersza polecenia do . 

Aby ustawić zmienne środowiskowe w kontenerze, należy je określić podczas tworzenia wystąpienia kontenera. W tym artykule przedstawiono przykłady ustawiania zmiennych środowiskowych podczas uruchamiania kontenera za pomocą interfejsu [wiersza polecenia platformy Azure,](#azure-cli-example) [programu Azure PowerShell](#azure-powershell-example)i [portalu Azure.](#azure-portal-example) 

Na przykład po uruchomieniu obrazu [kontenera microsoft aci-wordcount][aci-wordcount] można zmodyfikować jego zachowanie, określając następujące zmienne środowiskowe:

*NumWords*: Liczba słów wysłanych do STDOUT.

*MinLength*: Minimalna liczba znaków w słowie, aby można było je policzyć. Większa liczba ignoruje typowe słowa, takie jak "of" i "the".

Jeśli musisz przekazać wpisy tajne jako zmienne środowiskowe, wystąpienia kontenerów platformy Azure obsługuje [bezpieczne wartości](#secure-values) dla kontenerów systemu Windows i Linux.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="azure-cli-example"></a>Przykład interfejsu wiersza polecenia platformy Azure

Aby wyświetlić domyślne dane wyjściowe [kontenera aci-wordcount,][aci-wordcount] uruchom go najpierw za pomocą tego polecenia [tworzenia kontenera az][az-container-create] (nie określono zmiennych środowiskowych):

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer1 \
    --image mcr.microsoft.com/azuredocs/aci-wordcount:latest \
    --restart-policy OnFailure
```

Aby zmodyfikować dane wyjściowe, `--environment-variables` uruchom drugi kontener z dodanym argumentem, określając wartości *zmiennych NumWords* i *MinLength.* (W tym przykładzie założono, że są uruchomione interfejsu wiersza polecenia w powłoki Bash lub usługi Azure Cloud Shell. Jeśli używasz wiersza polecenia systemu Windows, określ zmienne `--environment-variables "NumWords"="5" "MinLength"="8"`z podwójnymi cudzysłowami, takimi jak .)

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer2 \
    --image mcr.microsoft.com/azuredocs/aci-wordcount:latest \
    --restart-policy OnFailure \
    --environment-variables 'NumWords'='5' 'MinLength'='8'
```

Gdy stan obu kontenerów pokazuje jako *Zakończone* (użyj [az container show,][az-container-show] aby sprawdzić stan), wyświetlić ich dzienniki z [dzienników kontenera az,][az-container-logs] aby zobaczyć dane wyjściowe.

```azurecli-interactive
az container logs --resource-group myResourceGroup --name mycontainer1
az container logs --resource-group myResourceGroup --name mycontainer2
```

Dane wyjściowe kontenerów pokazują, jak zmodyfikowano zachowanie skryptu drugiego kontenera przez ustawienie zmiennych środowiskowych.

**mycontainer1**
```output
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

**mycontainer2**
```output
[('CLAUDIUS', 120),
 ('POLONIUS', 113),
 ('GERTRUDE', 82),
 ('ROSENCRANTZ', 69),
 ('GUILDENSTERN', 54)]
```

## <a name="azure-powershell-example"></a>Przykład programu Azure PowerShell

Ustawianie zmiennych środowiskowych w programie PowerShell jest `-EnvironmentVariable` podobne do interfejsu wiersza polecenia, ale używa argumentu wiersza polecenia.

Najpierw uruchom kontener [aci-wordcount][aci-wordcount] w domyślnej konfiguracji za pomocą tego polecenia [New-AzContainerGroup:][new-Azcontainergroup]

```azurepowershell-interactive
New-AzContainerGroup `
    -ResourceGroupName myResourceGroup `
    -Name mycontainer1 `
    -Image mcr.microsoft.com/azuredocs/aci-wordcount:latest
```

Teraz uruchom następujące polecenie [New-AzContainerGroup.][new-Azcontainergroup] Ten określa zmienne środowiskowe *NumWords* i *MinLength* po zapełnieniu zmiennej tablicowej: `envVars`

```azurepowershell-interactive
$envVars = @{'NumWords'='5';'MinLength'='8'}
New-AzContainerGroup `
    -ResourceGroupName myResourceGroup `
    -Name mycontainer2 `
    -Image mcr.microsoft.com/azuredocs/aci-wordcount:latest `
    -RestartPolicy OnFailure `
    -EnvironmentVariable $envVars
```

Po zakończeniu stanu obu kontenerów *(użyj* [Get-AzContainerInstanceLog,][azure-instance-log] aby sprawdzić stan), pociągnij ich dzienniki za pomocą polecenia [Get-AzContainerInstanceLog.][azure-instance-log]

```azurepowershell-interactive
Get-AzContainerInstanceLog -ResourceGroupName myResourceGroup -ContainerGroupName mycontainer1
Get-AzContainerInstanceLog -ResourceGroupName myResourceGroup -ContainerGroupName mycontainer2
```

Dane wyjściowe dla każdego kontenera pokazuje, jak zostały zmodyfikowane skryptu uruchamiane przez kontener przez ustawienie zmiennych środowiskowych.

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

Aby ustawić zmienne środowiskowe podczas uruchamiania kontenera w witrynie Azure portal, należy określić je na stronie **Zaawansowane** podczas tworzenia kontenera.

1. Na stronie **Zaawansowane** ustaw **zasadę Uruchom ponownie** na Błąd *Włączone*
2. W **obszarze Zmienne środowiskowe**wprowadź `NumWords` z wartością dla pierwszej zmiennej `5` i wprowadź `MinLength` z wartością dla drugiej zmiennej. `8` 
1. Wybierz **opcję Przejrzyj + utwórz,** aby zweryfikować, a następnie wdrożyć kontener.

![Strona portalu z zmienną środowiskową Przycisk Włącz i pola tekstowe][portal-env-vars-01]

Aby wyświetlić dzienniki kontenera, w obszarze **Ustawienia** wybierz **pozycję Kontenery**, a następnie **dzienniki**. Podobnie jak dane wyjściowe pokazane w poprzednich sekcjach interfejsu wiersza polecenia i programu PowerShell, można zobaczyć, jak zachowanie skryptu zostało zmodyfikowane przez zmienne środowiskowe. Wyświetlanych jest tylko pięć słów, z których każdy ma minimalną długość ośmiu znaków.

![Portal z danymi wyjściowymi dziennika kontenera][portal-env-vars-02]

## <a name="secure-values"></a>Bezpieczne wartości

Obiekty z bezpiecznymi wartościami są przeznaczone do przechowywania poufnych informacji, takich jak hasła lub klucze do aplikacji. Korzystanie z bezpiecznych wartości dla zmiennych środowiskowych jest bezpieczniejsze i bardziej elastyczne niż uwzględnienie ich w obrazie kontenera. Inną opcją jest użycie woluminów tajnych, opisanych w [mount tajny wolumin w instancjach kontenera platformy Azure](container-instances-volume-secret.md).

Zmienne środowiskowe z bezpiecznymi wartościami nie są widoczne we właściwościach kontenera — ich wartości są dostępne tylko z poziomu kontenera. Na przykład właściwości kontenera wyświetlane w witrynie Azure portal lub interfejsu wiersza polecenia platformy Azure wyświetlają tylko nazwę bezpiecznej zmiennej, a nie jej wartość.

Ustaw bezpieczną zmienną środowiskową, określając `secureValue` właściwość zamiast zwykłej `value` dla typu zmiennej. Dwie zmienne zdefiniowane w następującym YAML demonstrują dwa typy zmiennych.

### <a name="yaml-deployment"></a>Wdrożenie YAML

Utwórz `secure-env.yaml` plik z następującym fragmentem kodu.

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

Uruchom następujące polecenie, aby wdrożyć grupę kontenerów z YAML (w razie potrzeby dostosuj nazwę grupy zasobów):

```azurecli-interactive
az container create --resource-group myResourceGroup --file secure-env.yaml
```

### <a name="verify-environment-variables"></a>Weryfikowanie zmiennych środowiskowych

Uruchom polecenie [az container show,][az-container-show] aby zbadać zmienne środowiskowe kontenera:

```azurecli-interactive
az container show --resource-group myResourceGroup --name securetest --query 'containers[].environmentVariables'
```

Odpowiedź JSON pokazuje zarówno klucz i wartość zmiennej środowiska niezabezpieczone, ale tylko nazwę zmiennej bezpiecznego środowiska:

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

Za pomocą polecenia [az container exec,][az-container-exec] które umożliwia wykonywanie polecenia w uruchomionym kontenerze, można sprawdzić, czy ustawiono zmienną bezpiecznego środowiska. Uruchom następujące polecenie, aby rozpocząć interaktywną sesję bash w kontenerze:

```azurecli-interactive
az container exec --resource-group myResourceGroup --name securetest --exec-command "/bin/bash"
```

Po otwarciu interaktywnej powłoki w kontenerze `SECRET` można uzyskać dostęp do wartości zmiennej:

```console
root@caas-ef3ee231482549629ac8a40c0d3807fd-3881559887-5374l:/# echo $SECRET
my-secret-value
```

## <a name="next-steps"></a>Następne kroki

Scenariusze oparte na zadaniach, takie jak przetwarzanie wsadowe dużego zestawu danych z kilkoma kontenerami, mogą korzystać z niestandardowych zmiennych środowiskowych w czasie wykonywania. Aby uzyskać więcej informacji na temat uruchamiania kontenerów opartych na zadaniach, zobacz [Uruchamianie zadań konteneryzowanych z zasadami ponownego uruchamiania](container-instances-restart-policy.md).

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
