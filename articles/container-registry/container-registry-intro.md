---
title: Prywatne rejestry kontenerów platformy Docker na platformie Azure — omówienie
description: Wprowadzenie do usługi Azure Container Registry dostarczającej oparte na chmurze, zarządzane rejestry prywatne platformy Docker.
services: container-registry
author: stevelas
ms.service: container-registry
ms.topic: overview
ms.date: 06/28/2019
ms.author: stevelas
ms.custom: seodec18, mvc
ms.openlocfilehash: b8b4b5fc3ec15d921ff5580aff4d0202be1d38b9
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/11/2019
ms.locfileid: "67797897"
---
# <a name="introduction-to-private-docker-container-registries-in-azure"></a>Wprowadzenie do prywatnych rejestrów kontenerów platformy Docker na platformie Azure

Usługa Azure Container Registry to zarządzana, prywatnych usługi rejestru aparatu Docker w oparciu typu open-source Docker Registry 2.0. Twórz i Obsługuj rejestry kontenerów platformy Azure do przechowywania i zarządzania nimi prywatnych obrazów kontenerów Docker.

Rejestry kontenerów platformy Azure za pomocą Twojego istniejącego kontenera potoków programowania i wdrażania, lub kompilowanie obrazów kontenerów na platformie Azure za pomocą zadań rejestru kontenera platformy Azure. Tworzenie na żądanie, lub w pełni Automatyzuj kompilacje dzięki wyzwalaczy, takich jak zatwierdzenia, kod źródłowy i oprzeć aktualizacji obrazu.

Aby uzyskać więcej informacji o pojęciach dotyczących platformy Docker i rejestru, zobacz [Docker — omówienie](https://docs.docker.com/engine/docker-overview/) i [o rejestrów, repozytoriów i obrazów](container-registry-concepts.md).

## <a name="use-cases"></a>Przypadki zastosowań

Ściąganie obrazów z rejestru kontenerów platformy Azure do różnych celów wdrażania:

* **Skalowalne systemy organizowania** zarządzające konteneryzowanymi aplikacjami w klastrach hostów, włączając w to rozwiązania [DC/OS](https://kubernetes.io/docs/), [Docker Swarm](https://docs.mesosphere.com/) i [Kubernetes](https://docs.docker.com/swarm/).
* **Usługi platformy Azure** obsługujące kompilowanie i uruchamianie aplikacji w odpowiedniej skali, w tym usługi [Azure Kubernetes Service (AKS)](../aks/index.yml), [App Service](../app-service/index.yml), [Batch](../batch/index.yml), [Service Fabric](/azure/service-fabric/) i inne.

Deweloperzy mogą również przeprowadzać wypychanie do rejestru kontenerów w ramach przepływu pracy opracowywania kontenera. Na przykład, takich jak kierować dane do rejestru kontenera przy użyciu narzędzia ciągłej integracji i dostarczania [potoki Azure](/azure/devops/pipelines/get-started/what-is-azure-pipelines.md) lub [Jenkins](https://jenkins.io/).

Konfigurowanie zadań ACR może automatycznie odbudować obrazy aplikacji, po zaktualizowaniu ich obrazy podstawowe lub Automatyzowanie kompilacji obrazów, gdy zespół zatwierdzenia kodu do repozytorium Git. Tworzenie wieloetapowych zadań do automatyzowania tworzenia, testowania i stosowanie poprawek wiele obrazów kontenera równolegle w chmurze.

Platforma Azure udostępnia narzędzia, w tym interfejsu wiersza polecenia platformy Azure, witryny Azure portal i obsługa interfejsu API do zarządzania usługi rejestry kontenerów platformy Azure. Opcjonalnie Zainstaluj [rozszerzenie Docker Extension for Visual Studio Code](https://code.visualstudio.com/docs/azure/docker) i [konta platformy Azure](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account) rozszerzenie, aby pracować z rejestrów kontenerów platformy Azure. Ściąganie i wypychanie obrazów do usługi Azure container registry lub uruchamiania zadań podrzędnych usługi ACR, wszystko to w ramach programu Visual Studio Code.

## <a name="key-features"></a>Najważniejsze funkcje

* **Jednostki SKU rejestru** -tworzenie jednego lub więcej rejestrów kontenerów w ramach subskrypcji platformy Azure. Rejestry są dostępne w ramach trzech jednostek SKU: [Podstawowa, standardowa i Premium](container-registry-skus.md), każdy z nich obsługuje integrację elementów webhook, uwierzytelnianie rejestru za pomocą usługi Azure Active Directory i funkcji usuwania. Utwórz rejestr w tej samej lokalizacji platformy Azure, w której znajdują się wdrożenia, aby korzystać z lokalnego, bliskiego sieciowo magazynu obrazów kontenerów. Użyj funkcji [replikacji geograficznej](container-registry-geo-replication.md) dostępnej w rejestrach w warstwie Premium dla scenariuszy zaawansowanej replikacji i dystrybucji obrazu kontenera. 

  Możesz [kontrolować dostęp](container-registry-authentication.md) do rejestru kontenerów za pomocą tożsamości platformy Azure, [jednostki usługi](../active-directory/develop/app-objects-and-service-principals.md) wspieranej przez usługę Azure Active Directory lub podanego konta administratora. Zaloguj się do rejestru przy użyciu wiersza polecenia platformy Azure lub standardem `docker login` polecenia.

* **Obsługiwane obrazy i artefaktów** — pogrupowane w repozytorium, każdy obraz jest migawką tylko do odczytu kontenera zgodnego z platformą Docker. Rejestry kontenerów platformy Azure mogą obejmować zarówno obrazy systemu Windows, jak i Linux. Możesz kontrolować nazwy obrazów wszystkich wdrożeń kontenera. Użyj standardowych [poleceń platformy Docker](https://docs.docker.com/engine/reference/commandline/), aby wypchnąć obrazy do repozytorium lub aby ściągnąć je z repozytorium. Oprócz obrazów kontenerów platformy Docker, są przechowywane w usłudze Azure Container Registry [związane z zawartością formatów](container-registry-image-formats.md) takich jak [Helm wykresy](container-registry-helm-repos.md) i obrazy stworzone z myślą o [obraz Otwórz kontener Initiative (OCI) Format specyfikacji](https://github.com/opencontainers/image-spec/blob/master/spec.md).

* **Zadania usługi rejestru Azure Container** — użyj [zadania rejestru kontenera platformy Azure](container-registry-tasks-overview.md) (ACR zadania), aby usprawnić tworzenia, testowania, wypychanie i wdrażanie obrazów na platformie Azure. Na przykład użyć zadania ACR rozszerzyć swoje rozwoju wewnętrzny pętli do chmury dzięki przeniesieniu `docker build` operacji na platformie Azure. Konfiguruj zadania kompilacji w celu automatyzowania potoku poprawiania platformy i systemu operacyjnego kontenera, gdy zespół zatwierdza kod w ramach kontroli źródła.

  [Zadania wieloetapowe](container-registry-tasks-overview.md#multi-step-tasks) Podaj definicji zadań na podstawie kroku i wykonywania dla tworzenia, testowania i poprawianie obrazów kontenerów w chmurze. Kroki zadań definiują pojedyncze operacje tworzenia i wypychania obrazu kontenera. Mogą one również definiować wykonanie jednego lub kilku kontenerów, z każdym krokiem używającym kontenera jako jego środowiska wykonawczego.

## <a name="next-steps"></a>Następne kroki

* [Create a container registry using the Azure portal](container-registry-get-started-portal.md) (Tworzenie rejestru kontenerów za pomocą witryny Azure Portal)
* [Create a container registry using the Azure CLI](container-registry-get-started-azure-cli.md) (Tworzenie rejestru kontenerów za pomocą interfejsu wiersza polecenia platformy Azure)
* [Automatyzuj kompilacje kontenera i konserwacji za pomocą zadań usługi ACR](container-registry-tasks-overview.md)
