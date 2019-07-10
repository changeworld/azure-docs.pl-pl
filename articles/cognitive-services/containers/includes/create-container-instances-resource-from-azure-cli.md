---
title: Obsługa kontenerów
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak utworzyć zasób wystąpienia kontenera platformy Azure z wiersza polecenia platformy Azure.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 7/5/2019
ms.author: dapine
ms.openlocfilehash: 5e7a3d849f726ae4dbbd559d541464404e427775
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/09/2019
ms.locfileid: "67711737"
---
## <a name="create-an-azure-container-instance-resource-from-the-azure-cli"></a>Tworzenie zasobu wystąpienia kontenera platformy Azure z poziomu wiersza polecenia platformy Azure

YAML poniżej definiuje zasobów wystąpienia kontenera platformy Azure. Skopiuj i Wklej zawartość do nowego pliku o nazwie `my-aci.yaml` i Zastąp komentarze wartości swoimi własnymi. Można znaleźć w [formacie szablonu] [szablon formant] dla prawidłowe YAML. Zapoznaj się [kontenera repozytoriów i obrazów][repositories-and-images] nazwy dostępnych obrazów i ich odpowiedniego repozytorium.

```YAML
apiVersion: 2018-10-01
location: # < Valid location >
name: # < Container Group name >
imageRegistryCredentials:
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
> Nie wszystkie lokalizacje mają ten sam dostępności procesora CPU i pamięci. Zapoznaj się [lokalizacji i zasobów][location-to-resource] tabeli, aby uzyskać listę dostępnych zasobów dla kontenerów na lokalizację i systemu operacyjnego.

Firma Microsoft będzie polegać na pliku YAML, utworzyliśmy dla [ `az container create` ][azure-container-create] polecenia. Z wiersza polecenia platformy Azure, wykonaj `az container create` polecenie, zastępując `<resource-group>` swoją własną. Ponadto zabezpieczanie wartości w ramach YAML wdrożenia można znaleźć [secure wartości][secure-values].

```azurecli
az container create -g <resource-group> -f my-aci.yaml
```

Dane wyjściowe polecenia `Running...` Jeśli prawidłowy, po upływie pewnego czasu danych wyjściowych zmieni się na ciąg JSON reprezentujący nowo utworzony zasób usługi ACI. Obraz kontenera jest większa niż może nie być dostępne od pewnego czasu, ale zasób został wdrożony.

> [!TIP]
> Zwracać szczególną uwagę na lokalizacje w publicznej wersji zapoznawczej usługi Azure Cognitive ofert, w razie YAML zostaną odpowiednio dopasowane do lokalizacji.

[azure-container-create]: https://docs.microsoft.com/cli/azure/container?view=azure-cli-latest#az-container-create
[template-format]: https://docs.microsoft.com/azure/templates/Microsoft.ContainerInstance/2018-10-01/containerGroups#template-format

[repositories-and-images]: ../../cognitive-services-container-support.md#container-repositories-and-images
[location-to-resource]: ../../../container-instances/container-instances-region-availability.md#availability---general
[secure-values]: ../../../container-instances/container-instances-environment-variables.md#secure-values