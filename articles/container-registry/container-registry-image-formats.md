---
title: Usługa Azure Container Registry formaty zawartości
description: Więcej informacji na temat obsługiwanych formatów zawartości w usłudze Azure Container Registry.
services: container-registry
author: dlepow
manager: jeconnoc
ms.service: container-registry
ms.topic: article
ms.date: 04/18/2019
ms.author: danlep
ms.openlocfilehash: fe129847e685c7151a9b7ad7ea65abbd38530733
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60827463"
---
# <a name="content-formats-supported-in-azure-container-registry"></a>Formaty zawartości obsługiwane w usłudze Azure Container Registry

Do zarządzania jednym z następujących formatów zawartości, należy użyć prywatnego repozytorium w usłudze Azure Container Registry. 

## <a name="docker-compatible-container-images"></a>Obrazy kontenera zgodnego z platformą docker

Obsługiwane są następujące formaty obrazu kontenera platformy Docker:

* [Docker Image Manifest V2, Schema 1](https://docs.docker.com/registry/spec/manifest-v2-1/)

* [V2 manifestu obrazu platformy docker, 2 schematu](https://docs.docker.com/registry/spec/manifest-v2-2/) — obejmuje manifestu Wyświetla, która zezwala na rejestrów do przechowywania obrazów dla wielu platform, w obszarze odwołaniem do jednego "obraz: tag"

## <a name="oci-images"></a>Obrazy OCI

Usługa Azure Container Registry obsługuje również obrazy, które spełniają [specyfikacji formatu obrazu Otwórz kontener Initiative (OCI)](https://github.com/opencontainers/image-spec/blob/master/spec.md). Pakowanie formaty obejmują [Singularity Format obrazu SIF](https://www.sylabs.io/2018/03/sif-containing-your-containers/).

## <a name="helm-charts"></a>Wykresów rozwiązania Helm

Usługa Azure Container Registry można hostować repozytoriów dla [Helm wykresy](https://helm.sh/), format pakowania, umożliwia szybkie zarządzanie i wdrażanie aplikacji dla platformy Kubernetes. [Klient narzędzia Helm](https://docs.helm.sh/using_helm/#installing-helm) wersji 2.11.0 lub nowszej jest obsługiwana.

## <a name="next-steps"></a>Kolejne kroki

* Zobacz jak [wypychanie i ściąganie](container-registry-get-started-docker-cli.md) obrazów za pomocą usługi Azure Container Registry.

* Użyj [zadania ACR](container-registry-tasks-overview.md) do tworzenia i testowania obrazów kontenerów. 

* Użyj [Moby BuildKit](https://github.com/moby/buildkit) do kompilacji i tworzenia kontenerów w formacie OCI pakietów.

* Konfigurowanie [repozytorium narzędzia Helm](container-registry-helm-repos.md) hostowanej w usłudze Azure Container Registry. 


