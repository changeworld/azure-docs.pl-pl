---
title: Azure Container Registry formatów zawartości
description: Dowiedz się więcej na temat obsługiwanych formatów zawartości w Azure Container Registry.
services: container-registry
author: dlepow
manager: gwallace
ms.service: container-registry
ms.topic: article
ms.date: 04/18/2019
ms.author: danlep
ms.openlocfilehash: c804dd7b73ea4a51c02c8b342a4ac60d992ec7c5
ms.sourcegitcommit: f5075cffb60128360a9e2e0a538a29652b409af9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/18/2019
ms.locfileid: "68310690"
---
# <a name="content-formats-supported-in-azure-container-registry"></a>Formaty zawartości obsługiwane w Azure Container Registry

Aby zarządzać jednym z następujących formatów zawartości, należy użyć prywatnego repozytorium w Azure Container Registry. 

## <a name="docker-compatible-container-images"></a>Obrazy kontenerów zgodnych z platformą Docker

Obsługiwane są następujące formaty obrazów kontenerów platformy Docker:

* [Manifest obrazu platformy Docker v2, schemat 1](https://docs.docker.com/registry/spec/manifest-v2-1/)

* [Manifest obrazu platformy Docker v2, schemat 2](https://docs.docker.com/registry/spec/manifest-v2-2/) — zawiera listę manifestów umożliwiającą rejestrom przechowywanie obrazów wieloplatformowych w ramach jednego odwołania "Image: tag"

## <a name="oci-images"></a>Obrazy OCI

Azure Container Registry obsługuje również obrazy zgodne ze [specyfikacją formatu obrazu Open Container Initiative (OCI)](https://github.com/opencontainers/image-spec/blob/master/spec.md). Formaty pakietów obejmują [Format obrazu Singularity (sif)](https://www.sylabs.io/2018/03/sif-containing-your-containers/).

## <a name="helm-charts"></a>Wykresy Helm

Azure Container Registry mogą hostować repozytoria dla [wykresów Helm](https://helm.sh/), format pakietu służący do szybkiego zarządzania aplikacjami i wdrażania ich w programie Kubernetes. Obsługiwany jest [Helm klienta](https://docs.helm.sh/using_helm/#installing-helm) w wersji 2.11.0 lub nowszej.

## <a name="next-steps"></a>Następne kroki

* Zobacz, jak [wypychanie i ściąganie](container-registry-get-started-docker-cli.md) obrazów za pomocą Azure Container Registry.

* Za pomocą [zadań ACR](container-registry-tasks-overview.md) można tworzyć i testować obrazy kontenerów. 

* Użyj [Moby BuildKit](https://github.com/moby/buildkit) do kompilowania kontenerów i pakietów w formacie OCI.

* Skonfiguruj [repozytorium Helm](container-registry-helm-repos.md) hostowane w Azure Container Registry. 


