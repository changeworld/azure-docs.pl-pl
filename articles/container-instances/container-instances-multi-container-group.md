---
title: Samouczek — wdrażanie grupy wielu kontenerów — szablon
description: W tym samouczku dowiesz się, jak wdrożyć grupę kontenerów z wieloma kontenerami w wystąpieniach kontenerów platformy Azure przy użyciu szablonu usługi Azure Resource Manager z interfejsu wiersza polecenia platformy Azure.
ms.topic: article
ms.date: 04/03/2019
ms.custom: mvc
ms.openlocfilehash: d2b4e20520cad28c5d62118f6c9d10fcc43ac89e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74533621"
---
# <a name="tutorial-deploy-a-multi-container-group-using-a-resource-manager-template"></a>Samouczek: Wdrażanie grupy wielu kontenerów przy użyciu szablonu Menedżera zasobów

> [!div class="op_single_selector"]
> * [YAML](container-instances-multi-container-yaml.md)
> * [Menedżer zasobów](container-instances-multi-container-group.md)

Wystąpienia kontenera platformy Azure obsługuje wdrażanie wielu kontenerów na jednym hoście przy użyciu [grupy kontenerów.](container-instances-container-groups.md) Grupa kontenerów jest przydatna podczas tworzenia sidecar aplikacji do rejestrowania, monitorowania lub innej konfiguracji, w której usługa wymaga drugiego dołączonego procesu.

W tym samouczku wykonaj kroki, aby uruchomić prostą konfigurację wózka bocznego z dwoma kontenerami, wdrażając szablon usługi Azure Resource Manager przy użyciu interfejsu wiersza polecenia platformy Azure. Omawiane kwestie:

> [!div class="checklist"]
> * Konfigurowanie szablonu grupy wielu kontenerów
> * Wdrażanie grupy kontenerów
> * Wyświetlanie dzienników kontenerów

Szablon Menedżera zasobów można łatwo dostosować do scenariuszy, gdy trzeba wdrożyć dodatkowe zasoby usługi platformy Azure (na przykład udział usługi Azure Files lub sieci wirtualnej) z grupą kontenerów. 

> [!NOTE]
> Grupy wielu kontenerów są obecnie ograniczone do kontenerów systemu Linux. 

Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) przed rozpoczęciem.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="configure-a-template"></a>Konfigurowanie szablonu

Zacznij od skopiowania następującego JSON `azuredeploy.json`do nowego pliku o nazwie . W usłudze Azure Cloud Shell można użyć programu Visual Studio Code do utworzenia pliku w katalogu roboczym:

```
code azuredeploy.json
```

Ten szablon Menedżera zasobów definiuje grupę kontenerów z dwoma kontenerami, publicznym adresem IP i dwoma narażonymi portami. Pierwszy kontener w grupie uruchamia aplikację internetową skierowaną do Internetu. Drugi kontener, sidecar, sprawia, że żądanie HTTP do głównej aplikacji sieci web za pośrednictwem sieci lokalnej grupy.

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

Aby użyć rejestru obrazów kontenera prywatnego, dodaj obiekt do dokumentu JSON w następującym formacie. Przykładowa implementacja tej konfiguracji można znaleźć w dokumentacji [odwołania do szablonu Menedżera zasobów usługi ACI.][template-reference]

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

Wdrażanie szablonu za pomocą polecenia [tworzenie wdrażania grupy AZ.][az-group-deployment-create]

```azurecli-interactive
az group deployment create --resource-group myResourceGroup --template-file azuredeploy.json
```

W ciągu kilku sekund powinna pojawić się początkowa odpowiedź z platformy Azure.

## <a name="view-deployment-state"></a>Wyświetlanie stanu wdrożenia

Aby wyświetlić stan wdrożenia, użyj następującego polecenia [az container show:][az-container-show]

```azurecli-interactive
az container show --resource-group myResourceGroup --name myContainerGroup --output table
```

Jeśli chcesz wyświetlić uruchomiającą aplikację, przejdź do jej adresu IP w przeglądarce. Na przykład adres `52.168.26.124` IP znajduje się w tym przykładzie danych wyjściowych:

```bash
Name              ResourceGroup    Status    Image                                                                                               IP:ports              Network    CPU/Memory       OsType    Location
----------------  ---------------  --------  --------------------------------------------------------------------------------------------------  --------------------  ---------  ---------------  --------  ----------
myContainerGroup  danlep0318r      Running   mcr.microsoft.com/azuredocs/aci-tutorial-sidecar,mcr.microsoft.com/azuredocs/aci-helloworld:latest  20.42.26.114:80,8080  Public     1.0 core/1.5 gb  Linux     eastus
```

## <a name="view-container-logs"></a>Wyświetlanie dzienników kontenerów

Wyświetlanie danych wyjściowych dziennika kontenera za pomocą polecenia [dzienniki kontenera az.][az-container-logs] Argument `--container-name` określa kontener, z którego mają być pobierane dzienniki. W tym przykładzie `aci-tutorial-app` określono kontener.

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

Aby wyświetlić dzienniki kontenera wózka bocznego, `aci-tutorial-sidecar` uruchom podobne polecenie określające kontener.

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

Jak widać, sidecar jest okresowo dokonywania żądania HTTP do głównej aplikacji sieci web za pośrednictwem sieci lokalnej grupy, aby upewnić się, że jest uruchomiony. Ten przykład sidecar można rozwinąć, aby wyzwolić alert, `200 OK`jeśli otrzymał kod odpowiedzi HTTP inny niż .

## <a name="next-steps"></a>Następne kroki

W tym samouczku użyto szablonu usługi Azure Resource Manager do wdrożenia grupy wielu kontenerów w wystąpieniach kontenerów platformy Azure. W tym samouczku omówiono:

> [!div class="checklist"]
> * Konfigurowanie szablonu grupy wielu kontenerów
> * Wdrażanie grupy kontenerów
> * Wyświetlanie dzienników kontenerów

Aby uzyskać dodatkowe przykłady szablonów, zobacz [Szablony usługi Azure Resource Manager dla wystąpień kontenerów platformy Azure](container-instances-samples-rm.md).

Można również określić grupę wielu kontenerów przy użyciu [pliku YAML](container-instances-multi-container-yaml.md). Ze względu na bardziej zwięzły charakter formatu YAML wdrożenie z plikiem YAML jest dobrym wyborem, gdy wdrożenie obejmuje tylko wystąpienia kontenera.


<!-- LINKS - Internal -->
[aci-tutorial]: ./container-instances-tutorial-prepare-app.md
[az-container-logs]: /cli/azure/container#az-container-logs
[az-container-show]: /cli/azure/container#az-container-show
[az-group-create]: /cli/azure/group#az-group-create
[az-group-deployment-create]: /cli/azure/group/deployment#az-group-deployment-create
[template-reference]: https://docs.microsoft.com/azure/templates/microsoft.containerinstance/containergroups
