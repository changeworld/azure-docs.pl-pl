---
title: Obsługiwane formaty zawartości
description: Dowiedz się więcej o formatach zawartości obsługiwanych przez usługę Azure Container Registry, w tym obrazach kontenerów zgodnych ze standardami Platformy Docker, wykresach helm, obrazach OCI i artefaktach OCI.
ms.topic: article
ms.date: 08/30/2019
ms.openlocfilehash: ab915385f46f83c7b655acd1a48d66df84b50653
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79247009"
---
# <a name="content-formats-supported-in-azure-container-registry"></a>Formaty zawartości obsługiwane w rejestrze kontenerów platformy Azure

Użyj prywatnego repozytorium w rejestrze kontenerów platformy Azure, aby zarządzać jednym z następujących formatów zawartości. 

## <a name="docker-compatible-container-images"></a>Obrazy kontenerów zgodne z programem Docker

Obsługiwane są następujące formaty obrazów kontenera platformy Docker:

* [Manifest obrazu platformy Docker V2, schemat 1](https://docs.docker.com/registry/spec/manifest-v2-1/)

* [Manifest obrazu platformy Docker V2, Schemat 2](https://docs.docker.com/registry/spec/manifest-v2-2/) — zawiera listy manifestów, które umożliwiają rejestrom przechowywanie obrazów wieloplatformowych w jednym odwołaniu "image:tag"

## <a name="oci-images"></a>Obrazy OCI

Usługa Azure Container Registry obsługuje obrazy spełniające [specyfikację formatu obrazu Open Container Initiative (OCI).](https://github.com/opencontainers/image-spec/blob/master/spec.md) Formaty opakowań obejmują [format obrazu osobliwości (SIF).](https://github.com/sylabs/sif)

## <a name="oci-artifacts"></a>Artefakty OCI

Usługa Azure Container Registry obsługuje [specyfikację dystrybucji OCI](https://github.com/opencontainers/distribution-spec), specyfikację neutralną dla dostawców, niezależną od chmury do przechowywania, udostępniania, zabezpieczania i wdrażania obrazów kontenerów i innych typów zawartości (artefaktów). Specyfikacja umożliwia rejestru do przechowywania szerokiej gamy artefaktów oprócz obrazów kontenerów. Narzędzia odpowiednie dla artefaktu są używane do wypychania i ściągania artefaktów. Na przykład zobacz [Wypychanie i ściąganie artefaktu OCI przy użyciu rejestru kontenerów platformy Azure.](container-registry-oci-artifacts.md)

Aby dowiedzieć się więcej o artefaktach OCI, zobacz [rejestr OCI jako repozytorium magazynu (ORAS)](https://github.com/deislabs/oras) i repozytorium [artefaktów OCI](https://github.com/opencontainers/artifacts) w usłudze GitHub.

## <a name="helm-charts"></a>Wykresy helmowe

Usługa Azure Container Registry może hostować repozytoria [wykresów helm,](https://helm.sh/)format pakowania używany do szybkiego zarządzania aplikacjami i wdrażania aplikacji dla aplikacji Kubernetes. [Obsługiwany](https://docs.helm.sh/using_helm/#installing-helm) jest klient helm w wersji 2 (2.11.0 lub nowszej).

## <a name="next-steps"></a>Następne kroki

* Zobacz, jak [wypychać i pobierać](container-registry-get-started-docker-cli.md) obrazy za pomocą usługi Azure Container Registry.

* Zadania [usługi ACR](container-registry-tasks-overview.md) służy do tworzenia i testowania obrazów kontenerów. 

* Użyj [Moby BuildKit](https://github.com/moby/buildkit) do tworzenia i pakowania kontenerów w formacie OCI.

* Konfigurowanie [repozytorium Helm](container-registry-helm-repos.md) hostowanego w rejestrze kontenerów platformy Azure. 


