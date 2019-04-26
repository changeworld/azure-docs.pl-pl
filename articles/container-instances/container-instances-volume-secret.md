---
title: Zainstaluj wolumin tajny w usłudze Azure Container Instances
description: Dowiedz się, jak zainstalować wpisu tajnego wolumin do przechowywania poufnych informacji dotyczących dostępu przez wystąpienia kontenera
services: container-instances
author: dlepow
ms.service: container-instances
ms.topic: article
ms.date: 07/19/2018
ms.author: danlep
ms.openlocfilehash: 3c1c83bb0c3e46a7eaab519050d9c556e2cc1a7a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60563090"
---
# <a name="mount-a-secret-volume-in-azure-container-instances"></a>Zainstaluj wolumin tajny w usłudze Azure Container Instances

Użyj *klucz tajny* wolumin, aby podać poufne informacje do kontenerów w grupie kontenerów. *Klucz tajny* wolumin przechowuje wpisy tajne w plikach w woluminie, dostępny dla kontenerów w grupie kontenerów. Dzięki przechowywaniu wpisów tajnych w *klucz tajny* woluminu, możesz uniknąć dodawania dane poufne, takie jak klucze SSH lub poświadczenia bazy danych do kodu aplikacji.

Wszystkie *klucz tajny* woluminy są wspierane przez [tmpfs][tmpfs], filesystem kopią zapasową w pamięci RAM; ich zawartość nigdy nie są zapisywane w pamięci trwałej.

> [!NOTE]
> *Klucz tajny* woluminy są obecnie ograniczone do kontenerów systemu Linux. Dowiedz się, jak przekazać zmienne bezpieczne środowisko dla kontenerów systemów Windows i Linux w [ustawić zmienne środowiskowe](container-instances-environment-variables.md). Podczas gdy pracujemy, aby udostępnić wszystkie funkcje dostępne w kontenerach Windows, można znaleźć bieżące różnice dotyczące platform w [limity przydziałów i dostępność regionów dla usługi Azure Container Instances](container-instances-quotas.md).

## <a name="mount-secret-volume---azure-cli"></a>Zainstaluj wolumin tajny — interfejs wiersza polecenia platformy Azure

Aby wdrożyć kontener z jednego lub więcej wpisów tajnych za pomocą wiersza polecenia platformy Azure, należy dołączyć `--secrets` i `--secrets-mount-path` parametrów w [utworzyć kontener az] [ az-container-create] polecenia. W tym przykładzie instaluje *klucz tajny* składający się z dwóch wpisów tajnych, "mysecret1" i "mysecret2," w woluminie `/mnt/secrets`:

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name secret-volume-demo \
    --image mcr.microsoft.com/azuredocs/aci-helloworld \
    --secrets mysecret1="My first secret FOO" mysecret2="My second secret BAR" \
    --secrets-mount-path /mnt/secrets
```

Następujące [az container exec] [ az-container-exec] dane wyjściowe pokazują otworzyć powłokę w działający kontener, wyświetlając listę plików w woluminie wpisu tajnego, a następnie wyświetlanie ich zawartość:

```console
$ az container exec --resource-group myResourceGroup --name secret-volume-demo --exec-command "/bin/sh"
/usr/src/app # ls -1 /mnt/secrets
mysecret1
mysecret2
/usr/src/app # cat /mnt/secrets/mysecret1
My first secret FOO
/usr/src/app # cat /mnt/secrets/mysecret2
My second secret BAR
/usr/src/app # exit
Bye.
```

## <a name="mount-secret-volume---yaml"></a>Zainstaluj wolumin tajny - YAML

Można także wdrożyć grup kontenerów przy użyciu wiersza polecenia platformy Azure i [szablonów YAML](container-instances-multi-container-yaml.md). Wdrażanie szablonów YAML jest preferowaną metodą w przypadku wdrażania grup kontenerów składające się z wielu kontenerów.

Podczas wdrażania za pomocą szablonu YAML, muszą być wartościami wpisów tajnych **algorytmem Base64** w szablonie. Jednak wartościami wpisów tajnych są wyświetlane w postaci zwykłego tekstu w plikach w kontenerze.

Następujący szablon YAML definiuje grupę kontenerów za pomocą jednego kontenera, który instaluje *klucz tajny* woluminu w `/mnt/secrets`. Wolumin tajny ma dwa klucze tajne, "mysecret1" i "mysecret2."

```yaml
apiVersion: '2018-06-01'
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

Wdrożyć za pomocą szablonu YAML, należy zapisać w pliku o nazwie poprzedniego YAML `deploy-aci.yaml`, a następnie wykonaj [utworzyć kontener az] [ az-container-create] polecenia `--file` parametru:

```azurecli-interactive
# Deploy with YAML template
az container create --resource-group myResourceGroup --file deploy-aci.yaml
```

## <a name="mount-secret-volume---resource-manager"></a>Zainstaluj wolumin tajny — Resource Manager

Oprócz wdrażania interfejs wiersza polecenia i YAML, możesz wdrożyć grupę kontenerów za pomocą platformy Azure [szablonu usługi Resource Manager](/azure/templates/microsoft.containerinstance/containergroups).

Najpierw należy wypełnić `volumes` tablicy w grupie kontenerów `properties` części szablonu. Podczas wdrażania za pomocą szablonu usługi Resource Manager, muszą być wartościami wpisów tajnych **algorytmem Base64** w szablonie. Jednak wartościami wpisów tajnych są wyświetlane w postaci zwykłego tekstu w plikach w kontenerze.

Następnie dla każdego kontenera w grupie kontenerów, w którym chcesz zainstalować *klucz tajny* woluminu, wypełnij `volumeMounts` tablicy w `properties` sekcja definicji kontenera.

Następujący szablon usługi Resource Manager definiuje grupę kontenerów za pomocą jednego kontenera, który instaluje *klucz tajny* woluminu w `/mnt/secrets`. Wolumin tajny ma dwa klucze tajne, "mysecret1" i "mysecret2."

<!-- https://github.com/Azure/azure-docs-json-samples/blob/master/container-instances/aci-deploy-volume-secret.json -->
[!code-json[volume-secret](~/azure-docs-json-samples/container-instances/aci-deploy-volume-secret.json)]

Wdrażanie przy użyciu szablonu usługi Resource Manager, należy zapisać w pliku o nazwie powyższego kodu JSON `deploy-aci.json`, a następnie wykonaj [Utwórz wdrożenie grupy az] [ az-group-deployment-create] polecenia `--template-file` parametru:

```azurecli-interactive
# Deploy with Resource Manager template
az group deployment create --resource-group myResourceGroup --template-file deploy-aci.json
```

## <a name="next-steps"></a>Kolejne kroki

### <a name="volumes"></a>Woluminy

Dowiedz się, jak zainstalować inne typy woluminu w usłudze Azure Container Instances:

* [Instalowanie udziału plików platformy Azure w usłudze Azure Container Instances](container-instances-volume-azure-files.md)
* [Zainstalować emptyDir woluminu w wystąpień kontenera platformy Azure](container-instances-volume-emptydir.md)
* [Zainstalować wolumin gitRepo w wystąpień kontenera platformy Azure](container-instances-volume-gitrepo.md)

### <a name="secure-environment-variables"></a>Zabezpieczanie zmiennych środowiskowych

Inną metodą poufnych informacji do kontenerów (w tym Windows kontenery) jest za pośrednictwem [secure zmienne środowiskowe](container-instances-environment-variables.md#secure-values).

<!-- LINKS - External -->
[tmpfs]: https://wikipedia.org/wiki/Tmpfs

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container#az-container-create
[az-container-exec]: /cli/azure/container#az-container-exec
[az-group-deployment-create]: /cli/azure/group/deployment#az-group-deployment-create
