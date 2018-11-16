---
title: Usługa Azure Container Registry formaty zawartości
description: Więcej informacji na temat obsługiwanych formatów zawartości w usłudze Azure Container Registry.
services: container-registry
author: dlepow
manager: jeconnoc
ms.service: container-registry
ms.topic: article
ms.date: 10/31/2018
ms.author: danlep
ms.openlocfilehash: e7155604339bc634078fd022e05ede5f902bc0d8
ms.sourcegitcommit: 0b7fc82f23f0aa105afb1c5fadb74aecf9a7015b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/14/2018
ms.locfileid: "51634713"
---
# <a name="content-formats-supported-in-azure-container-registry"></a>Formaty zawartości obsługiwane w usłudze Azure Container Registry

Do zarządzania jednym z następujących formatów zawartości, należy użyć prywatnego repozytorium w usłudze Azure Container Registry. 

## <a name="docker-compatible-container-images"></a>Obrazy kontenera zgodnego z platformą docker

* [V2 manifestu obrazu platformy docker, schematu 1](https://docs.docker.com/registry/spec/manifest-v2-1/)

* [V2 manifestu obrazu platformy docker, 2 schematu](https://docs.docker.com/registry/spec/manifest-v2-2/) — obejmuje manifestu Wyświetla, która zezwala na rejestrów do przechowywania obrazów dla wielu platform, w obszarze odwołaniem do jednego "obraz: tag"

* [Specyfikacja formatu obrazu Initiative (OCI) Open Container](https://github.com/opencontainers/image-spec/blob/master/spec.md) 


## <a name="helm-charts"></a>Wykresów rozwiązania Helm

Usługa Azure Container Registry może również obsługiwać repozytoriów dla [Helm wykresy](https://helm.sh/), format pakowania, umożliwia szybkie zarządzanie i wdrażanie aplikacji dla platformy Kubernetes. [Klient narzędzia Helm](https://docs.helm.sh/using_helm/#installing-helm) wersji 2.11.0 lub nowszej jest obsługiwana.

## <a name="next-steps"></a>Kolejne kroki

* Zobacz jak [wypychanie i ściąganie](container-registry-get-started-docker-cli.md) obrazów za pomocą usługi Azure Container Registry.

* Użyj [zadania ACR](container-registry-tasks-overview.md) do tworzenia i testowania obrazów kontenerów. 

* Użyj [Moby BuildKit](https://github.com/moby/buildkit) do kompilacji i tworzenia kontenerów w formacie OCI pakietów.

* Konfigurowanie [repozytorium narzędzia Helm](container-registry-helm-repos.md) hostowanej w usłudze Azure Container Registry. 


