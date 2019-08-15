---
title: Obsługa kontenerów
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak utworzyć zasób wystąpienia kontenera platformy Azure z poziomu interfejsu wiersza polecenia platformy Azure.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 7/5/2019
ms.author: dapine
ms.openlocfilehash: 2080d283c6cb7466dcb4847a81d76a4c3109217a
ms.sourcegitcommit: 5b76581fa8b5eaebcb06d7604a40672e7b557348
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/13/2019
ms.locfileid: "69012230"
---
## <a name="create-an-azure-container-instance-resource-from-the-azure-cli"></a>Tworzenie zasobu wystąpienia kontenera platformy Azure przy użyciu interfejsu wiersza polecenia platformy Azure

YAML poniżej definiuje zasób wystąpienia kontenera platformy Azure. Skopiuj i wklej zawartość do nowego pliku o nazwie `my-aci.yaml` i Zastąp jej własnymi wartościami. Zapoznaj się z [formatem szablonu][template-format] prawidłowy YAML. Zapoznaj się z [repozytoriami i obrazami kontenerów][repositories-and-images] dla dostępnych nazw obrazów i ich odpowiedniego repozytorium.

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
> Nie wszystkie lokalizacje mają tę samą dostępność procesora CPU i pamięci. Zapoznaj się z tabelą [Lokalizacja i zasoby][location-to-resource] , aby uzyskać listę dostępnych zasobów dla kontenerów dla poszczególnych lokalizacji i systemu operacyjnego.

Zabędziemy korzystać z pliku YAML, który został utworzony dla [`az container create`][azure-container-create] polecenia. W interfejsie wiersza `az container create` polecenia platformy Azure wykonaj polecenie, `<resource-group>` zastępując własny własny. Ponadto w celu zabezpieczania wartości w ramach wdrożenia YAML odnosi się do [wartości zabezpieczonych][secure-values].

```azurecli
az container create -g <resource-group> -f my-aci.yaml
```

Dane wyjściowe polecenia są `Running...` prawidłowe, po upływie zmiany danych wyjściowych na ciąg JSON reprezentujący nowo utworzony zasób ACI. Obraz kontenera jest najprawdopodobniej niedostępny przez pewien czas, ale zasób jest teraz wdrażany.

> [!TIP]
> Zwróć szczególną uwagę na lokalizacje publicznej wersji zapoznawczej oferty usługi Azure poznawczej, ponieważ YAML będzie musiał odpowiednio dopasować do lokalizacji.

[azure-container-create]: https://docs.microsoft.com/cli/azure/container?view=azure-cli-latest#az-container-create
[template-format]: https://docs.microsoft.com/azure/templates/Microsoft.ContainerInstance/2018-10-01/containerGroups#template-format

[repositories-and-images]: ../../cognitive-services-container-support.md#container-repositories-and-images
[location-to-resource]: ../../../container-instances/container-instances-region-availability.md#availability---general
[secure-values]: ../../../container-instances/container-instances-environment-variables.md#secure-values
