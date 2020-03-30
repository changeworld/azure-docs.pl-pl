---
title: Rejestry kontenerów zarządzanych
description: Wprowadzenie do usługi Azure Container Registry dostarczającej oparte na chmurze, zarządzane rejestry prywatne platformy Docker.
author: stevelas
ms.topic: overview
ms.date: 02/10/2020
ms.author: stevelas
ms.custom: seodec18, mvc
ms.openlocfilehash: 1992a2a63d16a955d136459f5dbaece7df815c71
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "77132028"
---
# <a name="introduction-to-private-docker-container-registries-in-azure"></a>Wprowadzenie do prywatnych rejestrów kontenerów platformy Docker na platformie Azure

Usługa Azure Container Registry to zarządzana, prywatna usługa rejestru platformy Docker oparta na rejestrze platformy Docker typu open source 2.0. Tworzenie i obsługa rejestrów kontenerów platformy Azure do przechowywania i zarządzania prywatnymi obrazami kontenerów platformy Docker i powiązanymi artefaktami.

Użyj rejestrów kontenerów platformy Azure z istniejącymi potokami tworzenia i wdrażania kontenerów lub użyj zadań rejestru kontenerów platformy Azure do tworzenia obrazów kontenerów na platformie Azure. Skompiluj na żądanie lub w pełni zautomatyzować kompilacje za pomocą wyzwalaczy, takich jak zatwierdzenia kodu źródłowego i aktualizacje obrazu podstawowego.

Aby uzyskać więcej informacji na temat pojęć platformy Docker i rejestru, zobacz omówienie platformy [Docker](https://docs.docker.com/engine/docker-overview/) i [Informacje o rejestrach, repozytoriach i obrazach](container-registry-concepts.md).

## <a name="use-cases"></a>Przypadki zastosowań

Ściąganie obrazów z rejestru kontenerów platformy Azure do różnych celów wdrażania:

* **Skalowalne systemy organizowania** zarządzające konteneryzowanymi aplikacjami w klastrach hostów, włączając w to rozwiązania [DC/OS](https://kubernetes.io/docs/), [Docker Swarm](https://docs.mesosphere.com/) i [Kubernetes](https://docs.docker.com/swarm/).
* **Usługi platformy Azure** obsługujące kompilowanie i uruchamianie aplikacji w odpowiedniej skali, w tym usługi [Azure Kubernetes Service (AKS)](../aks/index.yml), [App Service](../app-service/index.yml), [Batch](../batch/index.yml), [Service Fabric](/azure/service-fabric/) i inne.

Deweloperzy mogą również przeprowadzać wypychanie do rejestru kontenerów w ramach przepływu pracy opracowywania kontenera. Na przykład kierować rejestr kontenerów z ciągłej integracji i dostarczania narzędzia, takich jak [potoki platformy Azure](/azure/devops/pipelines/ecosystems/containers/acr-template) lub [Jenkins](https://jenkins.io/).

Skonfiguruj zadania ACR, aby automatycznie odbudowywać obrazy aplikacji po zaktualizowaniu ich obrazów podstawowych lub zautomatyzować kompilacje obrazów, gdy zespół zatwierdza kod do repozytorium Git. Tworzenie zadań wieloetapowych w celu automatyzacji tworzenia, testowania i instalowania poprawek wielu obrazów kontenerów równolegle w chmurze.

Platforma Azure zapewnia narzędzia, w tym interfejs wiersza polecenia platformy Azure, portal azure i obsługę interfejsu API do zarządzania rejestrami kontenerów platformy Azure. Opcjonalnie zainstaluj [rozszerzenie platformy Docker dla kodu programu Visual Studio](https://code.visualstudio.com/docs/azure/docker) i rozszerzenie konta platformy [Azure,](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account) aby pracować z rejestrami kontenerów platformy Azure. Ściągaj i wypychaj obrazy do rejestru kontenerów platformy Azure lub uruchom zadania usługi ACR, wszystkie w ramach programu Visual Studio Code.

## <a name="key-features"></a>Najważniejsze funkcje

* **Jednostki SKU rejestru** — tworzenie co najmniej jednego rejestru kontenerów w ramach subskrypcji platformy Azure. Rejestry są dostępne w trzech jednostkach SKU: [Basic, Standard i Premium,](container-registry-skus.md)z których każdy obsługuje integrację elementu webhook, uwierzytelnianie rejestru za pomocą usługi Azure Active Directory i funkcje usuwania. Utwórz rejestr w tej samej lokalizacji platformy Azure, w której znajdują się wdrożenia, aby korzystać z lokalnego, bliskiego sieciowo magazynu obrazów kontenerów. Użyj funkcji [replikacji geograficznej](container-registry-geo-replication.md) dostępnej w rejestrach w warstwie Premium dla scenariuszy zaawansowanej replikacji i dystrybucji obrazu kontenera. 

* **Zabezpieczenia i dostęp** — logowanie się do rejestru `docker login` przy użyciu interfejsu wiersza polecenia platformy Azure lub polecenia standardowego. Usługa Azure Container Registry przesyła obrazy kontenerów za pośrednictwem protokołu HTTPS i obsługuje protokół TLS w celu zabezpieczenia połączeń klientów. 

  > [!IMPORTANT]
  > Od 13 stycznia 2020 r. usługa Azure Container Registry będzie wymagać wszystkich bezpiecznych połączeń z serwerów i aplikacji do korzystania z protokołu TLS 1.2. Włącz TLS 1.2 przy użyciu dowolnego aktualnego klienta platformy docker (wersja 18.03.0 lub nowsza). Obsługa protokołu TLS 1.0 i 1.1 zostanie wycofana. 

  Możesz [kontrolować dostęp](container-registry-authentication.md) do rejestru kontenerów za pomocą tożsamości platformy Azure, [jednostki usługi](../active-directory/develop/app-objects-and-service-principals.md) wspieranej przez usługę Azure Active Directory lub podanego konta administratora. Użyj kontroli dostępu opartej na rolach (RBAC), aby przypisać użytkownikom lub systemom szczegółowe uprawnienia do rejestru.

  Funkcje zabezpieczeń jednostki SKU premium obejmują [zaufanie zawartości](container-registry-content-trust.md) do podpisywania tagów obrazu oraz [zapory i sieci wirtualne (wersja zapoznawcza),](container-registry-vnet.md) aby ograniczyć dostęp do rejestru. Usługa Azure Security Center opcjonalnie integruje się z rejestrem kontenerów platformy Azure w celu [skanowania obrazów](../security-center/azure-container-registry-integration.md?toc=/azure/container-registry/toc.json&bc=/azure/container-registry/breadcrumb/toc.json) za każdym razem, gdy obraz jest wypychany do rejestru.

* **Obsługiwane obrazy i artefakty** — pogrupowane w repozytorium, każdy obraz jest tylko do odczytu migawki kontenera zgodnego z platformy Docker. Rejestry kontenerów platformy Azure mogą obejmować zarówno obrazy systemu Windows, jak i Linux. Możesz kontrolować nazwy obrazów wszystkich wdrożeń kontenera. Użyj standardowych [poleceń platformy Docker](https://docs.docker.com/engine/reference/commandline/), aby wypchnąć obrazy do repozytorium lub aby ściągnąć je z repozytorium. Oprócz obrazów kontenerów platformy Docker usługa Azure Container Registry [przechowuje powiązane formaty zawartości,](container-registry-image-formats.md) takie jak [wykresy helm i](container-registry-helm-repos.md) obrazy utworzone zgodnie [ze specyfikacją formatu obrazu Open Container Initiative (OCI).](https://github.com/opencontainers/image-spec/blob/master/spec.md)

* **Automatyczne kompilacje obrazów** — użyj [zadań rejestru kontenerów platformy Azure](container-registry-tasks-overview.md) (zadania ACR), aby usprawnić tworzenie, testowanie, wypychanie i wdrażanie obrazów na platformie Azure. Na przykład użyj zadań usługi ACR, aby rozszerzyć wewnętrzną `docker build` pętlę deweloperów do chmury, odciążając operacje na platformie Azure. Konfiguruj zadania kompilacji w celu automatyzowania potoku poprawiania platformy i systemu operacyjnego kontenera, gdy zespół zatwierdza kod w ramach kontroli źródła.

  [Zadania wieloetapowe](container-registry-tasks-overview.md#multi-step-tasks) zapewniają definicję i wykonywanie zadań opartych na krokach do tworzenia, testowania i poprawiania obrazów kontenerów w chmurze. Kroki zadań definiują pojedyncze operacje tworzenia i wypychania obrazu kontenera. Mogą one również definiować wykonanie jednego lub kilku kontenerów, z każdym krokiem używającym kontenera jako jego środowiska wykonawczego.

## <a name="next-steps"></a>Następne kroki

* [Create a container registry using the Azure portal](container-registry-get-started-portal.md) (Tworzenie rejestru kontenerów za pomocą witryny Azure Portal)
* [Tworzenie rejestru kontenerów przy użyciu interfejsu wiersza polecenia platformy Azure](container-registry-get-started-azure-cli.md)
* [Automatyzacja kompilacji kontenerów i konserwacji za pomocą zadań ACR](container-registry-tasks-overview.md)