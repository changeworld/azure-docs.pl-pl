---
title: Instalowanie woluminu tajnego w grupie kontenerów
description: Dowiedz się, jak zainstalować tajny wolumin do przechowywania poufnych informacji w celu uzyskania dostępu przez wystąpienia kontenera
ms.topic: article
ms.date: 04/03/2020
ms.openlocfilehash: 756828e71174246450245938595c8872afc62961
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2020
ms.locfileid: "80657148"
---
# <a name="mount-a-secret-volume-in-azure-container-instances"></a>Instalowanie woluminu tajnego w wystąpieniach kontenera platformy Azure

Użyj *woluminu tajnego,* aby dostarczyć poufne informacje do kontenerów w grupie kontenerów. Tajny *secret* wolumin przechowuje wpisy tajne w plikach w woluminie, dostępne przez kontenery w grupie kontenerów. Przechowując wpisy tajne w *tajnym* woluminie, można uniknąć dodawania poufnych danych, takich jak klucze SSH lub poświadczenia bazy danych do kodu aplikacji.

* Po wdrożeniu z wpisami tajnymi w grupie kontenerów tajny wolumin jest *tylko do odczytu.*
* Wszystkie tajne woluminy są wspierane przez [tmpfs][tmpfs], system plików z pamięcią RAM; ich zawartość nigdy nie są zapisywane do przechowywania nieulotnego.

> [!NOTE]
> *Woluminy tajne* są obecnie ograniczone do kontenerów systemu Linux. Dowiedz się, jak przekazywać zmienne bezpiecznego środowiska dla kontenerów systemu Windows i Linux w [zmiennych środowiska Set.](container-instances-environment-variables.md) Podczas gdy pracujemy nad wprowadzeniem wszystkich funkcji do kontenerów systemu Windows, możesz znaleźć aktualne różnice między platformami w [przeglądzie.](container-instances-overview.md#linux-and-windows-containers)

## <a name="mount-secret-volume---azure-cli"></a>Instalowanie woluminu tajnego — narzędzie interfejsu wiersza polecenia platformy Azure

Aby wdrożyć kontener z co najmniej jednym wpisem `--secrets` `--secrets-mount-path` tajnym przy użyciu interfejsu wiersza polecenia platformy Azure, należy uwzględnić parametry i parametry w [poleceniu tworzenia kontenera az.][az-container-create] W tym przykładzie montuje *tajny* wolumin składający się z dwóch plików zawierających wpisy `/mnt/secrets`tajne, "mysecret1" i "mysecret2", w:

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name secret-volume-demo \
    --image mcr.microsoft.com/azuredocs/aci-helloworld \
    --secrets mysecret1="My first secret FOO" mysecret2="My second secret BAR" \
    --secrets-mount-path /mnt/secrets
```

Następujące [dane wyjściowe az container exec][az-container-exec] pokazuje otwarcie powłoki w uruchomionym kontenerze, listę plików w woluminie tajnym, a następnie wyświetlanie ich zawartości:

```azurecli
az container exec \
  --resource-group myResourceGroup \
  --name secret-volume-demo --exec-command "/bin/sh"
```

```output
/usr/src/app # ls /mnt/secrets
mysecret1
mysecret2
/usr/src/app # cat /mnt/secrets/mysecret1
My first secret FOO
/usr/src/app # cat /mnt/secrets/mysecret2
My second secret BAR
/usr/src/app # exit
Bye.
```

## <a name="mount-secret-volume---yaml"></a>Zamontuj tajny wolumin - YAML

Można również wdrożyć grupy kontenerów za pomocą interfejsu wiersza polecenia platformy Azure i [szablonu YAML](container-instances-multi-container-yaml.md). Wdrażanie przez szablon YAML jest preferowaną metodą podczas wdrażania grup kontenerów składających się z wielu kontenerów.

Podczas wdrażania z szablonem YAML, wartości tajne muszą być **kodowane base64** w szablonie. Jednak tajne wartości są wyświetlane w postaci zwykłego tekstu w plikach w kontenerze.

Poniższy szablon YAML definiuje grupę kontenerów z *secret* jednym `/mnt/secrets`kontenerem, który montuje tajny wolumin w pliku . Tajny wolumin zawiera dwa pliki zawierające tajemnice: "mysecret1" i "mysecret2".

```yaml
apiVersion: '2018-10-01'
location: eastus
name: secret-volume-demo
properties:
  containers:
  - name: aci-tutorial-app
    properties:
      environmentVariables: []
      image: mcr.microsoft.com/azuredocs/aci-helloworld:latest
      ports: []
      resources:
        requests:
          cpu: 1.0
          memoryInGB: 1.5
      volumeMounts:
      - mountPath: /mnt/secrets
        name: secretvolume1
  osType: Linux
  restartPolicy: Always
  volumes:
  - name: secretvolume1
    secret:
      mysecret1: TXkgZmlyc3Qgc2VjcmV0IEZPTwo=
      mysecret2: TXkgc2Vjb25kIHNlY3JldCBCQVIK
tags: {}
type: Microsoft.ContainerInstance/containerGroups
```

Aby wdrożyć za pomocą szablonu YAML, zapisz `deploy-aci.yaml`poprzedni YAML w pliku o `--file` nazwie , a następnie wykonaj polecenie [tworzenia kontenera az][az-container-create] z parametrem:

```azurecli-interactive
# Deploy with YAML template
az container create \
  --resource-group myResourceGroup \
  --file deploy-aci.yaml
```

## <a name="mount-secret-volume---resource-manager"></a>Instalowanie woluminu tajnego — Menedżer zasobów

Oprócz wdrażania interfejsu wiersza polecenia i YAML można wdrożyć grupę kontenerów przy użyciu [szablonu usługi](/azure/templates/microsoft.containerinstance/containergroups)Azure Resource Manager .

Najpierw wypełnij tablicę `volumes` w sekcji `properties` grupy kontenerów szablonu. Podczas wdrażania z szablonem Menedżera zasobów, wartości tajne muszą być **zakodowane w szablonie Base64.** Jednak tajne wartości są wyświetlane w postaci zwykłego tekstu w plikach w kontenerze.

Następnie dla każdego kontenera w grupie kontenerów, w którym chcesz zainstalować `volumeMounts` wolumin *tajny,* wypełnij tablicę `properties` w sekcji definicji kontenera.

Poniższy szablon Menedżera zasobów definiuje grupę kontenerów *secret* z jednym `/mnt/secrets`kontenerem, który montuje tajny wolumin w pliku . Tajny tom ma dwie tajemnice: "mysecret1" i "mysecret2".

<!-- https://github.com/Azure/azure-docs-json-samples/blob/master/container-instances/aci-deploy-volume-secret.json -->
[!code-json[volume-secret](~/azure-docs-json-samples/container-instances/aci-deploy-volume-secret.json)]

Aby wdrożyć za pomocą szablonu Menedżera zasobów, zapisz `deploy-aci.json`poprzedni json w pliku o `--template-file` nazwie , a następnie wykonaj polecenie tworzenia grupy wdrażania [az][az-deployment-group-create] z parametrem:

```azurecli-interactive
# Deploy with Resource Manager template
az deployment group create \
  --resource-group myResourceGroup \
  --template-file deploy-aci.json
```

## <a name="next-steps"></a>Następne kroki

### <a name="volumes"></a>Woluminy

Dowiedz się, jak zainstalować inne typy woluminów w wystąpieniach kontenera platformy Azure:

* [Instalowanie udziału plików platformy Azure w usłudze Azure Container Instances](container-instances-volume-azure-files.md)
* [Instalowanie woluminu pustegodiru w wystąpieniach kontenera platformy Azure](container-instances-volume-emptydir.md)
* [Instalowanie woluminu gitRepo w wystąpieniach kontenera platformy Azure](container-instances-volume-gitrepo.md)

### <a name="secure-environment-variables"></a>Bezpieczne zmienne środowiskowe

Inną metodą dostarczania poufnych informacji do kontenerów (w tym kontenerów systemu Windows) jest użycie [bezpiecznych zmiennych środowiskowych.](container-instances-environment-variables.md#secure-values)

<!-- LINKS - External -->
[tmpfs]: https://wikipedia.org/wiki/Tmpfs

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container#az-container-create
[az-container-exec]: /cli/azure/container#az-container-exec
[az-deployment-group-create]: /cli/azure/deployment/group#az-deployment-group-create
