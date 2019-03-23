---
title: Wdrażanie grup wielu kontenerów w usłudze Azure Container Instances za pomocą wiersza polecenia platformy Azure i YAML
description: Dowiedz się, jak wdrożyć grupę kontenerów za pomocą wielu kontenerów w usłudze Azure Container Instances za pomocą wiersza polecenia platformy Azure i pliku YAML.
services: container-instances
author: dlepow
ms.service: container-instances
ms.topic: article
ms.date: 03/21/2019
ms.author: danlep
ms.openlocfilehash: 10f2340bd85da3dabcd50d51a4dd56d58d31675b
ms.sourcegitcommit: 49c8204824c4f7b067cd35dbd0d44352f7e1f95e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/22/2019
ms.locfileid: "58372441"
---
# <a name="deploy-a-multi-container-container-group-with-yaml"></a>Wdrożyć grupę kontenerów z obsługą wielu kontenerów przy użyciu kodu YAML

Usługa Azure Container Instances obsługuje wdrażanie wielu kontenerów na jednym hoście za pomocą [grupy kontenerów](container-instances-container-groups.md). Grupy kontenerów z obsługą wielu kontenerów są przydatne podczas tworzenia przyczepki aplikacji, rejestrowanie, monitorowanie lub dowolnej innej konfiguracji których usługa wymaga drugiego dołączony proces.

Istnieją dwie metody wdrażania grup wielu kontenerów przy użyciu wiersza polecenia platformy Azure:

* Wdrażanie pliku YAML (w tym artykule)
* [Wdrażanie szablonu usługi Resource Manager](container-instances-multi-container-group.md)

Ze względu na charakter bardziej zwięzły widok formacie YAML, jest zalecane wdrożenie przy użyciu pliku YAML, gdy Twoje wdrożenie obejmuje *tylko* wystąpienia kontenera. Jeśli musisz wdrożyć zasoby dodatkowe usługi platformy Azure (na przykład udział usługi Azure Files) w czasie wdrażaniem wystąpienia kontenera, zaleca się wdrożenie szablonu usługi Resource Manager.

> [!NOTE]
> Grup wielu kontenerów są obecnie ograniczone do kontenerów systemu Linux. Podczas gdy pracujemy, aby udostępnić wszystkie funkcje dostępne w kontenerach Windows, można znaleźć bieżące różnice dotyczące platform w [limity przydziałów i dostępność regionów dla usługi Azure Container Instances](container-instances-quotas.md).

## <a name="configure-the-yaml-file"></a>Konfigurowanie pliku YAML

Aby wdrożyć grupę kontenerów z obsługą wielu kontenerów przy użyciu [utworzyć kontener az] [ az-container-create] polecenia w interfejsie wiersza polecenia platformy Azure, należy określić konfigurację grupy kontenerów w pliku YAML, a następnie przekazać plik YAML jako parametr w poleceniu.

Zacznij od skopiowania poniższego kodu YAML do nowego pliku o nazwie **wdrażanie aci.yaml**.

Tego pliku YAML definiuje grupę kontenerów o nazwie "myContainerGroup" z dwóch kontenerów, publiczny adres IP i dwa ujawnionych portów. Kontenery są wdrażane z obrazów w publicznych firmy Microsoft. Pierwszego kontenera w grupie uruchamia aplikację sieci web dostępnym z Internetu. Drugi kontener przyczepki, okresowo tworzą żądania HTTP do aplikacji sieci web, działającej w kontenerze pierwszy za pośrednictwem sieci lokalnej grupy kontenerów.

```YAML
apiVersion: 2018-10-01
location: eastus
name: myContainerGroup
properties:
  containers:
  - name: aci-tutorial-app
    properties:
      image: mcr.microsoft.com/azuredocs/aci-helloworld:latest
      resources:
        requests:
          cpu: 1
          memoryInGb: 1.5
      ports:
      - port: 80
      - port: 8080
  - name: aci-tutorial-sidecar
    properties:
      image: mcr.microsoft.com/azuredocs/aci-tutorial-sidecar
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

## <a name="deploy-the-container-group"></a>Wdrażanie grupy kontenerów

Utwórz grupę zasobów za pomocą [Tworzenie grupy az] [ az-group-create] polecenia:

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Wdróż grupę kontenerów z [utworzyć kontener az] [ az-container-create] polecenia, przekazanie pliku YAML jako argumentu:

```azurecli-interactive
az container create --resource-group myResourceGroup --file deploy-aci.yaml
```

W ciągu kilku sekund powinna pojawić się początkowa odpowiedź z platformy Azure.

## <a name="view-deployment-state"></a>Wyświetl stan wdrożenia

Aby wyświetlić stan wdrożenia, należy użyć następującego [az container show] [ az-container-show] polecenia:

```azurecli-interactive
az container show --resource-group myResourceGroup --name myContainerGroup --output table
```

Jeśli chcesz wyświetlić uruchomioną aplikację, przejdź do adresu IP w przeglądarce. Na przykład adres IP jest `52.168.26.124` w poniższych przykładowych danych wyjściowych:

```bash
Name              ResourceGroup    Status    Image                                                                                               IP:ports              Network    CPU/Memory       OsType    Location
----------------  ---------------  --------  --------------------------------------------------------------------------------------------------  --------------------  ---------  ---------------  --------  ----------
myContainerGroup  danlep0318r      Running   mcr.microsoft.com/azuredocs/aci-tutorial-sidecar,mcr.microsoft.com/azuredocs/aci-helloworld:latest  20.42.26.114:80,8080  Public     1.0 core/1.5 gb  Linux     eastus
```

## <a name="view-logs"></a>Wyświetlanie dzienników

Wyświetl dane wyjściowe dziennika kontenera przy użyciu [dzienniki kontenerów az] [ az-container-logs] polecenia. `--container-name` Argument określa kontener, z którego ma zostać pobierania dzienników. W tym przykładzie określono pierwszego kontenera.

```azurecli-interactive
az container logs --resource-group myResourceGroup --name myContainerGroup --container-name aci-tutorial-app
```

Dane wyjściowe:

```console
listening on port 80
::1 - - [21/Mar/2019:23:17:48 +0000] "HEAD / HTTP/1.1" 200 1663 "-" "curl/7.54.0"
::1 - - [21/Mar/2019:23:17:51 +0000] "HEAD / HTTP/1.1" 200 1663 "-" "curl/7.54.0"
::1 - - [21/Mar/2019:23:17:54 +0000] "HEAD / HTTP/1.1" 200 1663 "-" "curl/7.54.0"
```

Aby wyświetlić dzienniki dla kontenera po stronie samochód, uruchomić to samo polecenie, określając nazwę drugiego kontenera.

```azurecli-interactive
az container logs --resource-group myResourceGroup --name myContainerGroup --container-name aci-tutorial-sidecar
```

Dane wyjściowe:

```console
Every 3s: curl -I http://localhost                          2019-03-21 20:36:41

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
Date: Thu, 21 Mar 2019 20:36:41 GMT
Connection: keep-alive
```

Jak widać, przyczepka okresowo wysłał żądanie HTTP do aplikacji internetowej głównego za pośrednictwem sieci lokalnej grupy, aby upewnić się, że jest on uruchomiony. Ten przykład przyczepka może rozszerzyć w taki sposób, aby wyzwolić alert, jeśli otrzymał kod odpowiedzi HTTP inne niż 200 OK.

## <a name="deploy-from-private-registry"></a>Wdrożenie z rejestru prywatnego

Aby korzystać z prywatnego obrazu rejestru kontenerów, obejmują poniższego kodu YAML wartościami zmodyfikowane w danym środowisku:

```YAML
  imageRegistryCredentials:
  - server: imageRegistryLoginServer
    username: imageRegistryUsername
    password: imageRegistryPassword
```

Na przykład poniższego kodu YAML wdraża grupę kontenerów za pomocą jednego kontenera, obraz, którego są pobierane z prywatnego rejestru kontenerów platformy Azure o nazwie "myregistry":

```YAML
apiVersion: 2018-10-01
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

## <a name="export-container-group-to-yaml"></a>Eksportowanie grupy kontenerów do YAML

Konfigurację istniejącej grupy kontenerów można wyeksportować do pliku YAML, za pomocą polecenia interfejsu wiersza polecenia Azure [az container eksportu][az-container-export].

Przydatne dla grupy kontenerów przez konfigurację zachowania eksportu umożliwia przechowywanie konfiguracje grupy kontenerów w systemie kontroli wersji dla "konfiguracji jako kodu". Możesz też użyć wyeksportowanego pliku jako punktu wyjścia podczas tworzenia nowej konfiguracji w YAML.

Eksportuj konfigurację dla grupy kontenerów utworzone wcześniej przez wydanie następujących [az container eksportu] [ az-container-export] polecenia:

```azurecli-interactive
az container export --resource-group myResourceGroup --name myContainerGroup --file deployed-aci.yaml
```

Brak danych wyjściowych jest wyświetlana, jeśli polecenie zakończy się pomyślnie, ale można wyświetlić zawartość pliku, aby wyświetlić wynik. Na przykład pierwszych kilka wierszy z `head`:

```console
$ head deployed-aci.yaml
additional_properties: {}
apiVersion: '2018-06-01'
location: eastus
name: myContainerGroup
properties:
  containers:
  - name: aci-tutorial-app
    properties:
      environmentVariables: []
      image: mcr.microsoft.com/azuredocs/aci-helloworld:latest
```

## <a name="next-steps"></a>Kolejne kroki

W tym artykule opisano kroki wymagane do wdrożenia wystąpienia kontenera platformy Azure obsługującej wiele kontenerów. Środowisko usługi Azure Container Instances end-to-end, w tym za pomocą prywatnej usługi Azure container registry zobacz samouczek usługi Azure Container Instances.

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
