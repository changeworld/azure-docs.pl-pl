---
title: Wdrażanie kontenera wielu grup wystąpień kontenera platformy Azure z wiersza polecenia platformy Azure i yaml programu
description: Dowiedz się, jak wdrożyć grupę kontenera o wielu kontenerów w wystąpień kontenera Azure przy użyciu wiersza polecenia platformy Azure i plik yaml programu.
services: container-instances
author: mmacy
manager: jeconnoc
ms.service: container-instances
ms.topic: article
ms.date: 06/08/2018
ms.author: marsma
ms.openlocfilehash: 5dfee15e978d2dba0f50d1dc4b78953698389950
ms.sourcegitcommit: 3c3488fb16a3c3287c3e1cd11435174711e92126
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/07/2018
ms.locfileid: "34851331"
---
# <a name="deploy-a-multi-container-container-group-with-yaml"></a>Wdrażanie grupa kontenerem usługi kontenera o yaml programu

Wystąpień kontenera platformy Azure obsługuje wdrażanie wielu kontenerów na jednym hoście przy użyciu [grupy kontenerów](container-instances-container-groups.md). Kontener usługi kontenera grupy są przydatne podczas kompilowania aplikacji boczną rejestrowania, monitorowania lub dowolnej innej konfiguracji gdzie usługa musi drugi dołączony proces.

Istnieją dwie metody wdrażania grup usługi kontenera przy użyciu wiersza polecenia platformy Azure:

* Wdrażanie plików yaml programu (w tym artykule)
* [Wdrożenie szablonu usługi Resource Manager](container-instances-multi-container-group.md)

Ze względu na format yaml programu charakter bardziej zwięzły, wdrożenia przy użyciu pliku yaml programu jest zalecana, gdy wdrożenie obejmuje *tylko* wystąpień kontenera. Jeśli zajdzie potrzeba wdrożenia usługi Azure dodatkowe zasoby (na przykład udział plików Azure) w czasie wdrażania wystąpienia kontenera, zaleca się wdrożenie szablonu usługi Resource Manager.

> [!NOTE]
> Kontener wielu grup są obecnie ograniczone do kontenerów systemu Linux. Gdy pracujemy, aby wyświetlić wszystkie funkcje w celu kontenery systemu Windows, można znaleźć bieżącej platformy różnice w [przydziały i dostępność wystąpień kontenera platformy Azure w danym regionie](container-instances-quotas.md).

## <a name="configure-the-yaml-file"></a>Konfigurowanie pliku yaml programu

Aby wdrożyć grupę kontenerem usługi kontenera o [utworzyć kontener az] [ az-container-create] poleceń w wiersza polecenia platformy Azure, należy określić konfigurację grupy kontenera w pliku yaml programu, a następnie przekazać plik yaml programu jako do polecenia parametr.

Uruchom kopiowanie następujące yaml programu do nowego pliku o nazwie **wdrażanie aci.yaml**.

Ten plik yaml programu definiuje grupę kontenera o dwa kontenery, publiczny adres IP i dwa porty uwidocznione. Pierwszy kontenera w grupie działa aplikacja sieci web skierowane do Internetu. Drugi kontenera boczną, okresowo zgłasza żądania HTTP do aplikacji sieci web uruchomione w kontenerze pierwszy za pośrednictwem sieci lokalnej grupy kontenerów.

```YAML
apiVersion: 2018-06-01
location: eastus
name: myContainerGroup
properties:
  containers:
  - name: aci-tutorial-app
    properties:
      image: microsoft/aci-helloworld:latest
      resources:
        requests:
          cpu: 1
          memoryInGb: 1.5
      ports:
      - port: 80
      - port: 8080
  - name: aci-tutorial-sidecar
    properties:
      image: microsoft/aci-tutorial-sidecar
      resources:
        requests:
          cpu: 1
          memoryInGb: 1.5
  osType: Linux
  ipAddress:
    type: Public
    ports:
    - protocol: tcp
      port: '80'
    - protocol: tcp
      port: '8080'
tags: null
type: Microsoft.ContainerInstance/containerGroups
```

## <a name="deploy-the-container-group"></a>Wdrożenie grupy kontenera

Utwórz nową grupę zasobów o [Tworzenie grupy az] [ az-group-create] polecenia:

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Wdrożenie grupy kontenera z [utworzyć kontener az] [ az-container-create] polecenie przekazanie pliku yaml programu jako argument:

```azurecli-interactive
az container create --resource-group myResourceGroup --name myContainerGroup -f deploy-aci.yaml
```

W ciągu kilku sekund powinna pojawić się początkowa odpowiedź z platformy Azure.

## <a name="view-deployment-state"></a>Wyświetl stan wdrożenia

Aby wyświetlić stan wdrożenia, należy użyć następującego [Pokaż kontenera az] [ az-container-show] polecenia:

```azurecli-interactive
az container show --resource-group myResourceGroup --name myContainerGroup --output table
```

Jeśli chcesz wyświetlić działającej aplikacji, przejdź do adresu IP w przeglądarce. Na przykład adres IP jest `52.168.26.124` w tym przykładzie danych wyjściowych:

```bash
Name              ResourceGroup    ProvisioningState    Image                                                           IP:ports               CPU/Memory       OsType    Location
----------------  ---------------  -------------------  --------------------------------------------------------------  ---------------------  ---------------  --------  ----------
myContainerGroup  myResourceGroup  Succeeded            microsoft/aci-helloworld:latest,microsoft/aci-tutorial-sidecar  52.168.26.124:80,8080  1.0 core/1.5 gb  Linux     eastus
```

## <a name="view-logs"></a>Wyświetlanie dzienników

Wyświetlanie danych wyjściowych dziennika przy użyciu kontenera [dzienniki kontenera az] [ az-container-logs] polecenia. `--container-name` Argument określa kontener, od którego do pobierania dzienników. W tym przykładzie pierwsze kontener jest określony.

```azurecli-interactive
az container logs --resource-group myResourceGroup --name myContainerGroup --container-name aci-tutorial-app
```

Dane wyjściowe:

```console
listening on port 80
::1 - - [09/Jan/2018:23:17:48 +0000] "HEAD / HTTP/1.1" 200 1663 "-" "curl/7.54.0"
::1 - - [09/Jan/2018:23:17:51 +0000] "HEAD / HTTP/1.1" 200 1663 "-" "curl/7.54.0"
::1 - - [09/Jan/2018:23:17:54 +0000] "HEAD / HTTP/1.1" 200 1663 "-" "curl/7.54.0"
```

Aby wyświetlić dzienniki dla kontenera po stronie samochodu, uruchom tego samego polecenie, określając nazwę drugiego kontenera.

```azurecli-interactive
az container logs --resource-group myResourceGroup --name myContainerGroup --container-name aci-tutorial-sidecar
```

Dane wyjściowe:

```console
Every 3s: curl -I http://localhost                          2018-01-09 23:25:11

  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
  0  1663    0     0    0     0      0      0 --:--:-- --:--:-- --:--:--     0
HTTP/1.1 200 OK
X-Powered-By: Express
Accept-Ranges: bytes
Cache-Control: public, max-age=0
Last-Modified: Wed, 29 Nov 2017 06:40:40 GMT
ETag: W/"67f-16006818640"
Content-Type: text/html; charset=UTF-8
Content-Length: 1663
Date: Tue, 09 Jan 2018 23:25:11 GMT
Connection: keep-alive
```

Jak widać, boczną okresowo wysłał żądanie HTTP do aplikacji głównego sieci web za pośrednictwem sieci lokalnej grupy, aby upewnić się, że jest uruchomiona. W tym przykładzie boczną można rozszerzyć do wyzwolenia alertu, jeżeli Odebrano kod odpowiedzi HTTP innych niż 200 OK.

## <a name="deploy-from-private-registry"></a>Wdrażanie z rejestru prywatnych

Aby użyć rejestru obrazu Kontener prywatny, obejmują następujące yaml programu z wartościami zmodyfikowane w danym środowisku:

```YAML
  imageRegistryCredentials:
  - server: imageRegistryLoginServer
    username: imageRegistryUsername
    password: imageRegistryPassword
```

Na przykład następujące yaml programu wdraża grupa kontener o jeden kontener, w których obrazu są pobierane z prywatnej rejestru kontenera Azure o nazwie "myregistry":

```YAML
apiVersion: 2018-06-01
location: eastus
name: myContainerGroup2
properties:
  containers:
  - name: aci-tutorial-app
    properties:
      image: myregistry.azurecr.io/aci-helloworld:latest
      resources:
        requests:
          cpu: 1
          memoryInGb: 1.5
      ports:
      - port: 80
  osType: Linux
  ipAddress:
    type: Public
    ports:
    - protocol: tcp
      port: '80'
  imageRegistryCredentials:
  - server: myregistry.azurecr.io
    username: myregistry
    password: REGISTRY_PASSWORD
tags: null
type: Microsoft.ContainerInstance/containerGroups
```

## <a name="export-container-group-to-yaml"></a>Eksportowanie grupy kontenerów do yaml programu

Konfigurację istniejącej grupy kontenera można wyeksportować do pliku yaml programu za pomocą polecenia interfejsu wiersza polecenia Azure [eksportu kontenera az][az-container-export].

Przydatne w przypadku zachowania konfiguracji grupy kontenera, eksportu służy do przechowywania konfiguracji grupy kontenera w kontroli wersji dla "Konfiguracja jako kod". Możesz też użyć wyeksportowanego pliku jako punktu wyjścia podczas tworzenia nowej konfiguracji w yaml programu.

Eksportuj konfigurację dla grupy kontenerów utworzony wcześniej przez wystawienie następujące [eksportu kontenera az] [ az-container-export] polecenia:

```azurecli-interactive
az container export --resource-group rg604 --name myContainerGroup --file deployed-aci.yaml
```

Brak danych wyjściowych jest wyświetlana, jeśli zakończyło się pomyślnie, ale mogą wyświetlać zawartość pliku, aby zobaczyć wynik. Na przykład pierwszych kilku wierszy z `head`:

```console
$ head deployed-aci.yaml
apiVersion: 2018-02-01-preview
location: eastus
name: myContainerGroup
properties:
  containers:
  - name: aci-tutorial-app
    properties:
      environmentVariables: []
      image: microsoft/aci-helloworld:latest
      ports:
```

> [!NOTE]
> Począwszy od wersji 2.0.34 Azure CLI, istnieje [znany problem] [ cli-issue-6525] w które wyeksportowane grupy kontenerów Określ starszą wersję interfejsu API **2018-02-01-preview** (widoczne w poprzednim Przykład danych wyjściowych JSON). Jeśli chcesz ponownie wdrożyć za pomocą wyeksportowanego pliku yaml programu, można bezpiecznie zaktualizować `apiVersion` wartości w wyeksportowanym pliku yaml programu do **2018-06-01**.

## <a name="next-steps"></a>Kolejne kroki

W tym artykule opisano kroki niezbędne do wdrożenia wystąpienie kontenerem usługi kontenera platformy Azure. Dla pracy wystąpień kontenera Azure end-to-end, na przykład za pomocą rejestru prywatnej kontenera platformy Azure zobacz samouczek wystąpień kontenera platformy Azure.

> [!div class="nextstepaction"]
> [Samouczek dotyczący usługi Azure Container Instances][aci-tutorial]

<!-- LINKS - External -->
[cli-issue-6525]: https://github.com/Azure/azure-cli/issues/6525

<!-- LINKS - Internal -->
[aci-tutorial]: ./container-instances-tutorial-prepare-app.md
[az-container-create]: /cli/azure/container#az-container-create
[az-container-export]: /cli/azure/container#az-container-export
[az-container-logs]: /cli/azure/container#az-container-logs
[az-container-show]: /cli/azure/container#az-container-show
[az-group-create]: /cli/azure/group#az-group-create
[az-group-deployment-create]: /cli/azure/group/deployment#az-group-deployment-create
[template-reference]: https://docs.microsoft.com/azure/templates/microsoft.containerinstance/containergroups
