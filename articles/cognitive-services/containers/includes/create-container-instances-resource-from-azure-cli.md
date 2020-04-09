---
title: Obsługa kontenerów
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak utworzyć zasób wystąpienia kontenera platformy Azure z interfejsu wiersza polecenia platformy Azure.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: e3542b976921aa45794d62cad9517984c8348ce3
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/08/2020
ms.locfileid: "80875136"
---
## <a name="create-an-azure-container-instance-resource-from-the-azure-cli"></a>Tworzenie zasobu wystąpienia kontenera platformy Azure z interfejsu wiersza polecenia platformy Azure

Yaml poniżej definiuje zasób wystąpienia kontenera platformy Azure. Skopiuj i wklej zawartość `my-aci.yaml` do nowego pliku, nazwanego i zastąp komentowane wartości własnymi. Prawidłowy [format YAML][template-format] można znaleźć w formacie szablonu. Informacje na temat dostępnych nazw obrazów i odpowiednich repozytoriów kontenerów można znaleźć w [repozytoriach kontenerów i ich][repositories-and-images] odpowiednich repozytoriach. Aby uzyskać więcej informacji na temat odwołania YAML dla wystąpień kontenera, zobacz [odwołanie YAML: Wystąpienia kontenera platformy Azure][aci-yaml-ref].

```YAML
apiVersion: 2018-10-01
location: # < Valid location >
name: # < Container Group name >
imageRegistryCredentials: # This is required when pulling a non-public image
  - server: containerpreview.azurecr.io
    username: # < The username for the preview container registry >
    password: # < The password for the preview container registry >
properties:
  containers:
  - name: # < Container name >
    properties:
      image: # < Repository/Image name >
      environmentVariables: # These env vars are required
        - name: eula
          value: accept
        - name: billing
          value: # < Service specific Endpoint URL >
        - name: apikey
          value: # < Service specific API key >
      resources:
        requests:
          cpu: 4 # Always refer to recommended minimal resources
          memoryInGb: 8 # Always refer to recommended minimal resources
      ports:
        - port: 5000
  osType: Linux
  volumes: # This node, is only required for container instances that pull their model in at runtime, such as LUIS.
  - name: aci-file-share
    azureFile:
      shareName: # < File share name >
      storageAccountName: # < Storage account name>
      storageAccountKey: # < Storage account key >
  restartPolicy: OnFailure
  ipAddress:
    type: Public
    ports:
    - protocol: tcp
      port: 5000
tags: null
type: Microsoft.ContainerInstance/containerGroups
```

> [!NOTE]
> Nie wszystkie lokalizacje mają taką samą dostępność procesora i pamięci. Zobacz [tabelę lokalizacji i zasobów,][location-to-resource] aby uzyskać listę dostępnych zasobów dla kontenerów na lokalizację i system operacyjny.

Będziemy polegać na pliku YAML, [`az container create`][azure-container-create] który utworzyliśmy dla polecenia. Z interfejsu wiersza `az container create` polecenia platformy `<resource-group>` Azure wykonaj polecenie zastępując własne. Ponadto w celu zabezpieczenia wartości we wdrożeniu YAML należy zapoznać się [z wartościami bezpiecznych][secure-values].

```azurecli
az container create -g <resource-group> -f my-aci.yaml
```

Dane wyjściowe polecenia `Running...` jest, jeśli jest prawidłowy, po pewnym czasie dane wyjściowe zmienia się ciąg JSON reprezentujący nowo utworzony zasób ACI. Obraz kontenera jest bardziej niż prawdopodobne, nie będzie dostępny przez jakiś czas, ale zasób jest teraz wdrożony.

> [!TIP]
> Należy zwrócić szczególną uwagę na lokalizacje oferty usługi Azure Cognitive Service w publicznej wersji zapoznawczej, ponieważ yaml będzie musiał zostać odpowiednio dostosowany, aby dopasować lokalizację.

[azure-container-create]: https://docs.microsoft.com/cli/azure/container?view=azure-cli-latest#az-container-create
[template-format]: https://docs.microsoft.com/azure/templates/Microsoft.ContainerInstance/2018-10-01/containerGroups#template-format
[aci-yaml-ref]: ../../../container-instances/container-instances-reference-yaml.md
[repositories-and-images]: ../../cognitive-services-container-support.md#container-repositories-and-images
[location-to-resource]: ../../../container-instances/container-instances-region-availability.md#availability---general
[secure-values]: ../../../container-instances/container-instances-environment-variables.md#secure-values
