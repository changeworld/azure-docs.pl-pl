---
title: Prywatne rejestry kontenerów platformy Docker na platformie Azure
description: Wprowadzenie do usługi Azure Container Registry dostarczającej oparte na chmurze, zarządzane rejestry prywatne platformy Docker.
services: container-registry
author: stevelas
manager: jeconnoc
ms.service: container-registry
ms.topic: overview
ms.date: 05/08/2018
ms.author: stevelas
ms.custom: mvc
ms.openlocfilehash: 394297e87ef03541725aad0689f11bca17c05ed9
ms.sourcegitcommit: 615403e8c5045ff6629c0433ef19e8e127fe58ac
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/06/2018
ms.locfileid: "39576304"
---
# <a name="introduction-to-private-docker-container-registries-in-azure"></a>Wprowadzenie do prywatnych rejestrów kontenerów platformy Docker na platformie Azure

Usługa Azure Container Registry to zarządzana usługa [rejestru platformy Docker](https://docs.docker.com/registry/) oparta na oprogramowaniu typu open-source Docker Registry 2.0. Twórz i obsługuj rejestry kontenerów platformy Azure w celu przechowywania prywatnych obrazów [kontenerów platformy Docker](https://www.docker.com/what-docker) i zarządzania nimi.

Używaj rejestrów kontenerów na platformie Azure wraz z istniejącymi potokami programowania i wdrażania kontenerów. Używaj usługi Azure Container Registry Build (ACR Build) do kompilowania obrazów kontenerów na platformie Azure. Twórz kompilacje dostępne na żądanie lub całkowicie zautomatyzowane przy użyciu wyzwalaczy kompilacji aktualizacji obrazu podstawowego i zatwierdzenia kodu źródłowego.

Aby uzyskać ogólne informacje o platformie Docker i kontenerach, zobacz temat [Docker overview](https://docs.docker.com/engine/docker-overview/) (Omówienie platformy Docker).

## <a name="use-cases"></a>Przypadki zastosowań

Ściąganie obrazów z rejestru kontenerów platformy Azure do różnych celów wdrażania:

* **Skalowalne systemy organizowania** zarządzające konteneryzowanymi aplikacjami w klastrach hostów, włączając w to rozwiązania [DC/OS](https://docs.mesosphere.com/), [Docker Swarm](https://docs.docker.com/swarm/) i [Kubernetes](http://kubernetes.io/docs/).
* **Usługi platformy Azure** obsługujące kompilowanie i uruchamianie aplikacji w odpowiedniej skali, w tym usługi [Azure Kubernetes Service (AKS)](../aks/index.yml), [App Service](/app-service/index.md), [Batch](../batch/index.yml), [Service Fabric](/azure/service-fabric/) i inne.

Deweloperzy mogą również przeprowadzać wypychanie do rejestru kontenerów w ramach przepływu pracy opracowywania kontenera. Na przykład mogą kierować dane do rejestru kontenerów z poziomu narzędzia integracji ciągłej lub narzędzia do wdrażania, takiego jak usługa [Visual Studio Team Services](https://www.visualstudio.com/docs/overview) lub [Jenkins](https://jenkins.io/).

Konfiguruj zadania kompilacji usługi [ACR Build](#azure-container-registry-build), aby automatycznie ponownie kompilować obrazy aplikacji podczas aktualizowania ich obrazów podstawowych. Używaj usługi ACR Build, aby automatyzować kompilacje obrazów, gdy Twój zespół będzie zatwierdzać kod w repozytorium usługi Git. *Usługa ACR Build jest obecnie dostępna w wersji zapoznawczej.*

## <a name="key-concepts"></a>Kluczowe pojęcia

* **Rejestr** — utwórz przynajmniej jeden rejestr kontenerów w subskrypcji platformy Azure. Rejestry są dostępne w ramach trzech jednostek SKU: [podstawowej, standardowej i Premium](container-registry-skus.md). Każda z nich obsługuje integrację elementów webhook, uwierzytelnianie rejestru za pomocą usługi Azure Active Directory oraz funkcję usuwania. Utwórz rejestr w tej samej lokalizacji platformy Azure, w której znajdują się wdrożenia, aby korzystać z lokalnego, bliskiego sieciowo magazynu obrazów kontenerów. Użyj funkcji [replikacji geograficznej](container-registry-geo-replication.md) dostępnej w rejestrach w warstwie Premium dla scenariuszy zaawansowanej replikacji i dystrybucji obrazu kontenera. W pełni kwalifikowana nazwa rejestru ma format `myregistry.azurecr.io`.

  Możesz [kontrolować dostęp](container-registry-authentication.md) do rejestru kontenerów za pomocą [nazwy głównej usługi](../active-directory/develop/app-objects-and-service-principals.md) wspieranej przez usługę Azure Active Directory lub podanego konta administratora. Uruchom standardowe polecenie `docker login` w celu uwierzytelnienia za pomocą rejestru.

* **Repozytorium** — rejestr zawiera przynajmniej jedno repozytorium stanowiące grupę obrazów kontenerów. Usługa Azure Container Registry obsługuje wielopoziomowe przestrzenie nazw repozytoriów. Dzięki wielopoziomowym przestrzeniom nazw można grupować kolekcje obrazów związanych z określoną aplikacją lub kolekcje aplikacji związanych z określonymi zespołami programistycznymi lub operacyjnymi. Na przykład:

  * `myregistry.azurecr.io/aspnetcore:1.0.1` reprezentuje obraz całej firmy
  * `myregistry.azurecr.io/warrantydept/dotnet-build` reprezentuje obraz używany do tworzenia aplikacji platformy .NET współdzielony przez dział gwarancji
  * `myregistry.azurecr.io/warrantydept/customersubmissions/web` reprezentuje obraz sieci Web zgrupowany w aplikacji zgłoszeń klientów i należący do działu gwarancji

* **Obraz** — każdy obraz jest przechowywany w repozytorium i jest migawką tylko do odczytu kontenera platformy Docker. Rejestry kontenerów platformy Azure mogą obejmować zarówno obrazy systemu Windows, jak i Linux. Możesz kontrolować nazwy obrazów wszystkich wdrożeń kontenera. Użyj standardowych [poleceń platformy Docker](https://docs.docker.com/engine/reference/commandline/), aby wypchnąć obrazy do repozytorium lub aby ściągnąć je z repozytorium.

* **Kontener** — mianem kontenera określa się aplikację wraz z jej zależnościami, opakowaną w kompletny system plików, włączając w to kod, środowisko uruchomieniowe, narzędzia systemowe i biblioteki. Uruchom kontenery platformy Docker w oparciu o obrazy systemu Windows lub Linux, które zostały ściągnięte z rejestru kontenerów. Kontenery uruchomione na jednym komputerze współdzielą jądro systemu operacyjnego. Kontenery platformy Docker są całkowicie przenośne na wszystkie główne dystrybucje systemu Linux oraz systemy macOS i Windows.

## <a name="azure-container-registry-build-preview"></a>Azure Container Registry Build (wersja zapoznawcza)

Usługa [Azure Container Registry Build](container-registry-build-overview.md) (ACR Build) to pakiet funkcji usługi Azure Container Registry, który udostępnia udoskonalone i wydajne kompilacje obrazów kontenerów platformy Docker na platformie Azure. Usługa ACR Build umożliwia rozszerzanie wewnętrznej pętli programowania o chmurę dzięki odciążaniu operacji `docker build` na platformie Azure. Konfiguruj zadania kompilacji w celu automatyzowania potoku poprawiania platformy i systemu operacyjnego kontenera, gdy zespół zatwierdza kod w ramach kontroli źródła.

[!INCLUDE [container-registry-build-preview-note](../../includes/container-registry-build-preview-note.md)]

## <a name="next-steps"></a>Następne kroki

* [Create a container registry using the Azure portal](container-registry-get-started-portal.md) (Tworzenie rejestru kontenerów za pomocą witryny Azure Portal)
* [Create a container registry using the Azure CLI](container-registry-get-started-azure-cli.md) (Tworzenie rejestru kontenerów za pomocą interfejsu wiersza polecenia platformy Azure)
* [Automate OS and framework patching with ACR Build](container-registry-build-overview.md) (Preview) (Automatyzowanie poprawiania systemu operacyjnego i platformy przy użyciu usługi ACR Build (wersja zapoznawcza))
