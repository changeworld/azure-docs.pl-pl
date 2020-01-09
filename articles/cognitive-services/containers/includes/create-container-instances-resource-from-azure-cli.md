---
title: Obsługa kontenerów
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak utworzyć zasób wystąpienia kontenera platformy Azure z poziomu interfejsu wiersza polecenia platformy Azure.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 01/06/2020
ms.author: dapine
ms.openlocfilehash: 700a04b58c13a9c7fd5301875226ca234cabeb96
ms.sourcegitcommit: 2f8ff235b1456ccfd527e07d55149e0c0f0647cc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/07/2020
ms.locfileid: "75689452"
---
## <a name="create-an-azure-container-instance-resource-from-the-azure-cli"></a>Tworzenie zasobu wystąpienia kontenera platformy Azure przy użyciu interfejsu wiersza polecenia platformy Azure

YAML poniżej definiuje zasób wystąpienia kontenera platformy Azure. Skopiuj i wklej zawartość do nowego pliku o nazwie `my-aci.yaml` i Zastąp wartości z komentarzami własnymi. Zapoznaj się z [formatem szablonu][template-format] prawidłowy YAML. Zapoznaj się z [repozytoriami i obrazami kontenerów][repositories-and-images] dla dostępnych nazw obrazów i ich odpowiedniego repozytorium. Aby uzyskać więcej informacji o odwołaniach YAML dla wystąpień kontenerów, zobacz [YAML Reference: Azure Container Instances][aci-yaml-ref].

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
> Nie wszystkie lokalizacje mają tę samą dostępność procesora CPU i pamięci. Zapoznaj się z tabelą [Lokalizacja i zasoby][location-to-resource] , aby uzyskać listę dostępnych zasobów dla kontenerów dla poszczególnych lokalizacji i systemu operacyjnego.

Na plik YAML, który został utworzony dla polecenia [`az container create`][azure-container-create] . W interfejsie wiersza polecenia platformy Azure wykonaj `az container create` polecenie, zastępując `<resource-group>` własnym. Ponadto w celu zabezpieczania wartości w ramach wdrożenia YAML odnosi się do [wartości zabezpieczonych][secure-values].

```azurecli
az container create -g <resource-group> -f my-aci.yaml
```

Dane wyjściowe polecenia są `Running...`, jeśli dane wyjściowe zmienią się na ciąg JSON reprezentujący nowo utworzony zasób ACI. Obraz kontenera jest najprawdopodobniej niedostępny przez pewien czas, ale zasób jest teraz wdrażany.

> [!TIP]
> Zwróć szczególną uwagę na lokalizacje publicznej wersji zapoznawczej oferty usługi Azure poznawczej, ponieważ YAML będzie musiał odpowiednio dopasować do lokalizacji.

[azure-container-create]: https://docs.microsoft.com/cli/azure/container?view=azure-cli-latest#az-container-create
[template-format]: https://docs.microsoft.com/azure/templates/Microsoft.ContainerInstance/2018-10-01/containerGroups#template-format
[aci-yaml-ref]: ../../../container-instances/container-instances-reference-yaml.md
[repositories-and-images]: ../../cognitive-services-container-support.md#container-repositories-and-images
[location-to-resource]: ../../../container-instances/container-instances-region-availability.md#availability---general
[secure-values]: ../../../container-instances/container-instances-environment-variables.md#secure-values
