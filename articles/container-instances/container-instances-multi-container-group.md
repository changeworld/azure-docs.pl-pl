---
title: Wdrażanie grup wielu kontenerów w usłudze Azure Container Instances
description: Dowiedz się, jak wdrożyć grupę kontenerów za pomocą wielu kontenerów w usłudze Azure Container Instances za pomocą szablonu usługi Azure Resource Manager.
services: container-instances
author: dlepow
ms.service: container-instances
ms.topic: article
ms.date: 06/08/2018
ms.author: danlep
ms.custom: mvc
ms.openlocfilehash: 93f73e133e99025b479d0b38512e26088a8eaefa
ms.sourcegitcommit: 49c8204824c4f7b067cd35dbd0d44352f7e1f95e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/22/2019
ms.locfileid: "58369121"
---
# <a name="deploy-a-multi-container-group-with-a-resource-manager-template"></a>Wdrażanie grupy wielu kontenerów przy użyciu szablonu usługi Resource Manager

Usługa Azure Container Instances obsługuje wdrażanie wielu kontenerów na jednym hoście za pomocą [grupy kontenerów](container-instances-container-groups.md). Jest to przydatne podczas tworzenia przyczepki aplikacji, rejestrowanie, monitorowanie lub dowolnej innej konfiguracji których usługa wymaga drugiego dołączony proces.

Istnieją dwie metody wdrażania grup wielu kontenerów przy użyciu wiersza polecenia platformy Azure:

* Wdrażanie szablonu usługi Resource Manager (w tym artykule)
* [Wdrażanie pliku YAML](container-instances-multi-container-yaml.md)

Wdrażanie za pomocą szablonu usługi Resource Manager zaleca się, gdy należy wdrożyć zasoby dodatkowe usługi platformy Azure (na przykład udział usługi Azure Files) w czasie wdrażaniem wystąpienia kontenera. Ze względu na charakter bardziej zwięzły widok formacie YAML, jest zalecane wdrożenie przy użyciu pliku YAML, gdy Twoje wdrożenie obejmuje *tylko* wystąpienia kontenera.

> [!NOTE]
> Grup wielu kontenerów są obecnie ograniczone do kontenerów systemu Linux. Podczas gdy pracujemy, aby udostępnić wszystkie funkcje na potrzeby kontenerów systemu Windows, bieżące różnice dotyczące platform możesz znaleźć w temacie [Limity przydziałów i dostępność regionów dla usługi Azure Container Instances](container-instances-quotas.md).

Aby uzyskać przykłady dodatkowe szablony, zobacz [szablonów usługi Azure Resource Manager dla usługi Azure Container Instances](container-instances-samples-rm.md). 

## <a name="configure-the-template"></a>Konfigurowanie szablonu

Sekcje w tym artykule opisano uruchamianie konfiguracji proste przyczepki wielokontenerowych przez wdrożenie szablonu usługi Azure Resource Manager.

Rozpocznij od utworzenia pliku o nazwie `azuredeploy.json`, następnie skopiuj następujący kod JSON do niego.

Ten szablon usługi Resource Manager definiuje grupę kontenerów, z dwóch kontenerów, publiczny adres IP i dwa ujawnionych portów. Kontenery są wdrażane z obrazów w publicznych firmy Microsoft. Pierwszego kontenera w grupie uruchamia aplikację dostępnego z Internetu. Drugi kontener przyczepki, sprawia, że żądanie HTTP do aplikacji głównej sieci web za pośrednictwem sieci lokalnej grupy.

```JSON
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "containerGroupName": {
      "type": "string",
      "defaultValue": "myContainerGroup",
      "metadata": {
        "description": "Container Group name."
      }
    }
  },
  "variables": {
    "container1name": "aci-tutorial-app",
    "container1image": "mcr.microsoft.com/azuredocs/aci-helloworld:latest",
    "container2name": "aci-tutorial-sidecar",
    "container2image": "mcr.microsoft.com/azuredocs/aci-tutorial-sidecar"
  },
  "resources": [
    {
      "name": "[parameters('containerGroupName')]",
      "type": "Microsoft.ContainerInstance/containerGroups",
      "apiVersion": "2018-10-01",
      "location": "[resourceGroup().location]",
      "properties": {
        "containers": [
          {
            "name": "[variables('container1name')]",
            "properties": {
              "image": "[variables('container1image')]",
              "resources": {
                "requests": {
                  "cpu": 1,
                  "memoryInGb": 1.5
                }
              },
              "ports": [
                {
                  "port": 80
                },
                {
                  "port": 8080
                }
              ]
            }
          },
          {
            "name": "[variables('container2name')]",
            "properties": {
              "image": "[variables('container2image')]",
              "resources": {
                "requests": {
                  "cpu": 1,
                  "memoryInGb": 1.5
                }
              }
            }
          }
        ],
        "osType": "Linux",
        "ipAddress": {
          "type": "Public",
          "ports": [
            {
              "protocol": "tcp",
              "port": "80"
            },
            {
                "protocol": "tcp",
                "port": "8080"
            }
          ]
        }
      }
    }
  ],
  "outputs": {
    "containerIPv4Address": {
      "type": "string",
      "value": "[reference(resourceId('Microsoft.ContainerInstance/containerGroups/', parameters('containerGroupName'))).ipAddress.ip]"
    }
  }
}
```

Aby użyć rejestru obrazów kontenera prywatnych, należy dodać obiekt do dokumentu JSON o następującym formacie. Na przykład stosowania tej konfiguracji, zobacz [odwołanie do szablonu usługi Resource Manager w usłudze ACI] [ template-reference] dokumentacji.

```JSON
"imageRegistryCredentials": [
  {
    "server": "[parameters('imageRegistryLoginServer')]",
    "username": "[parameters('imageRegistryUsername')]",
    "password": "[parameters('imageRegistryPassword')]"
  }
]
```

## <a name="deploy-the-template"></a>Wdrożenie szablonu

Utwórz grupę zasobów za pomocą polecenia [az group create][az-group-create].

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Wdrażanie szablonu przy użyciu [Utwórz wdrożenie grupy az] [ az-group-deployment-create] polecenia.

```azurecli-interactive
az group deployment create --resource-group myResourceGroup --template-file azuredeploy.json
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

```bash
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

```bash
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

## <a name="next-steps"></a>Kolejne kroki

W tym artykule opisano kroki wymagane do wdrożenia wystąpienia kontenera platformy Azure obsługującej wiele kontenerów. Środowisko usługi Azure Container Instances end-to-end zobacz samouczek usługi Azure Container Instances.

> [!div class="nextstepaction"]
> [Samouczek dotyczący usługi Azure Container Instances][aci-tutorial]

<!-- LINKS - Internal -->
[aci-tutorial]: ./container-instances-tutorial-prepare-app.md
[az-container-logs]: /cli/azure/container#az-container-logs
[az-container-show]: /cli/azure/container#az-container-show
[az-group-create]: /cli/azure/group#az-group-create
[az-group-deployment-create]: /cli/azure/group/deployment#az-group-deployment-create
[template-reference]: https://docs.microsoft.com/azure/templates/microsoft.containerinstance/containergroups
