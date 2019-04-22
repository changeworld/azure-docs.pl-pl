---
title: Samouczek — wdrożenia grupy wielu kontenerów w usłudze Azure Container Instances — YAML
description: W tym samouczku dowiesz się, jak wdrożyć grupę kontenerów za pomocą wielu kontenerów w usłudze Azure Container Instances za pomocą pliku YAML przy użyciu wiersza polecenia platformy Azure.
services: container-instances
author: dlepow
ms.service: container-instances
ms.topic: article
ms.date: 04/03/2019
ms.author: danlep
ms.openlocfilehash: a0a91ece4f219cf822673cd457c064c326b89478
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2019
ms.locfileid: "59006192"
---
# <a name="tutorial-deploy-a-multi-container-group-using-a-yaml-file"></a>Samouczek: Wdrażanie grupy wielu kontenerów przy użyciu pliku YAML

> [!div class="op_single_selector"]
> * [YAML](container-instances-multi-container-yaml.md)
> * [Resource Manager](container-instances-multi-container-group.md)
>

Usługa Azure Container Instances obsługuje wdrażanie wielu kontenerów na jednym hoście za pomocą [grupy kontenerów](container-instances-container-groups.md). Grupy kontenerów jest przydatne podczas tworzenia przyczepki aplikacji, rejestrowanie, monitorowanie lub dowolnej innej konfiguracji których usługa wymaga drugiego dołączony proces.

W ramach tego samouczka należy wykonać kroki, aby uruchomić konfigurację proste przyczepki dwóch kontenerów, wdrażając pliku YAML, przy użyciu wiersza polecenia platformy Azure. Plik YAML zawiera zwięzłe formatu do określania ustawień wystąpienia. Omawiane kwestie:

> [!div class="checklist"]
> * Konfigurowanie pliku YAML
> * Wdrażanie grupy kontenerów
> * Wyświetlanie dzienników kontenerów

> [!NOTE]
> Grup wielu kontenerów są obecnie ograniczone do kontenerów systemu Linux.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="configure-a-yaml-file"></a>Konfigurowanie pliku YAML

Do wdrożenia grupy wielu kontenerów przy użyciu [utworzyć kontener az] [ az-container-create] polecenia w interfejsie wiersza polecenia platformy Azure, w pliku YAML, należy określić konfigurację grupy kontenera. Następnie przekaż plik YAML jako parametr do polecenia.

Zacznij od skopiowania poniższego kodu YAML do nowego pliku o nazwie **wdrażanie aci.yaml**. W usłudze Azure Cloud Shell można użyć programu Visual Studio Code, aby utworzyć plik w katalogu roboczym:

```
code deploy-aci.yaml
```

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

Aby użyć rejestru obrazów kontenera prywatnych, Dodaj `imageRegistryCredentials` właściwość do grupy kontenerów przy użyciu wartości zmodyfikowane w danym środowisku:

```YAML
  imageRegistryCredentials:
  - server: imageRegistryLoginServer
    username: imageRegistryUsername
    password: imageRegistryPassword
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

## <a name="view-container-logs"></a>Wyświetlanie dzienników kontenerów

Wyświetl dane wyjściowe dziennika kontenera przy użyciu [dzienniki kontenerów az] [ az-container-logs] polecenia. `--container-name` Argument określa kontener, z którego ma zostać pobierania dzienników. W tym przykładzie `aci-tutorial-app` kontenera jest określony.

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

Aby wyświetlić dzienniki Aby uzyskać kontenerem przyczepki, uruchom polecenie podobne określenie polecenia `aci-tutorial-sidecar` kontenera.

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

Jak widać, przyczepka okresowo wysłał żądanie HTTP do aplikacji internetowej głównego za pośrednictwem sieci lokalnej grupy, aby upewnić się, że jest on uruchomiony. Ten przykład przyczepka może można rozszerzyć, aby wyzwolić alert, jeśli otrzymał kod odpowiedzi HTTP inne niż `200 OK`.

## <a name="next-steps"></a>Kolejne kroki

W tym samouczku plik YAML jest używane do wdrażania grupy wielu kontenerów w usłudze Azure Container Instances. W tym samouczku omówiono:

> [!div class="checklist"]
> * Skonfigurowanie pliku YAML dla grupy wielu kontenerów
> * Wdrażanie grupy kontenerów
> * Wyświetlanie dzienników kontenerów

Można również określić grupy wielu kontenerów przy użyciu [szablonu usługi Resource Manager](container-instances-multi-container-group.md). Szablon usługi Resource Manager można łatwo zaadaptować pod kątem scenariuszy gdy należy wdrożyć dodatkowe usługi platformy Azure zasoby z grupy kontenerów.

<!-- LINKS - External -->


<!-- LINKS - Internal -->
[aci-tutorial]: ./container-instances-tutorial-prepare-app.md
[az-container-create]: /cli/azure/container#az-container-create
[az-container-logs]: /cli/azure/container#az-container-logs
[az-container-show]: /cli/azure/container#az-container-show
[az-group-create]: /cli/azure/group#az-group-create
[az-group-deployment-create]: /cli/azure/group/deployment#az-group-deployment-create
[template-reference]: https://docs.microsoft.com/azure/templates/microsoft.containerinstance/containergroups
