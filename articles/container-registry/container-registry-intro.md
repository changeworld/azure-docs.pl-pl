---
title: Prywatne rejestry kontenerów platformy Docker na platformie Azure — omówienie
description: Wprowadzenie do usługi Azure Container Registry dostarczającej oparte na chmurze, zarządzane rejestry prywatne platformy Docker.
services: container-registry
author: stevelas
ms.service: container-registry
ms.topic: overview
ms.date: 04/03/2019
ms.author: stevelas
ms.custom: seodec18, mvc
ms.openlocfilehash: ba75d196bdb53fab104ab6c01391e762b4a3841b
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2019
ms.locfileid: "59270527"
---
# <a name="introduction-to-private-docker-container-registries-in-azure"></a>Wprowadzenie do prywatnych rejestrów kontenerów platformy Docker na platformie Azure

Usługa Azure Container Registry to zarządzana usługa [rejestru platformy Docker](https://docs.docker.com/registry/) oparta na oprogramowaniu typu open-source Docker Registry 2.0. Twórz i obsługuj rejestry kontenerów platformy Azure w celu przechowywania prywatnych obrazów [kontenerów platformy Docker](https://www.docker.com/what-docker) i zarządzania nimi.

Rejestry kontenerów na platformie Azure za pomocą Twojego istniejącego kontenera potoków programowania i wdrażania lub użyj [zadania ACR](#azure-container-registry-tasks) do tworzenia obrazów kontenerów na platformie Azure. Twórz kompilacje dostępne na żądanie lub całkowicie zautomatyzowane przy użyciu wyzwalaczy kompilacji aktualizacji obrazu podstawowego i zatwierdzenia kodu źródłowego.

Aby uzyskać ogólne informacje o platformie Docker i kontenerach, zobacz temat [Docker overview](https://docs.docker.com/engine/docker-overview/) (Omówienie platformy Docker).

## <a name="use-cases"></a>Przypadki zastosowań

Ściąganie obrazów z rejestru kontenerów platformy Azure do różnych celów wdrażania:

* **Skalowalne systemy organizowania** zarządzające konteneryzowanymi aplikacjami w klastrach hostów, włączając w to rozwiązania [DC/OS](https://kubernetes.io/docs/), [Docker Swarm](https://docs.mesosphere.com/) i [Kubernetes](https://docs.docker.com/swarm/).
* **Usługi platformy Azure** obsługujące kompilowanie i uruchamianie aplikacji w odpowiedniej skali, w tym usługi [Azure Kubernetes Service (AKS)](../aks/index.yml), [App Service](../app-service/index.yml), [Batch](../batch/index.yml), [Service Fabric](/azure/service-fabric/) i inne.

Deweloperzy mogą również przeprowadzać wypychanie do rejestru kontenerów w ramach przepływu pracy opracowywania kontenera. Na przykład mogą kierować dane do rejestru kontenerów z poziomu narzędzia integracji ciągłej lub narzędzia do wdrażania, takiego jak usługa [Azure DevOps Services](https://docs.microsoft.com/azure/devops/) lub [Jenkins](https://jenkins.io/).

Konfigurowanie zadań ACR może automatycznie odbudować obrazy aplikacji, po zaktualizowaniu ich obrazy podstawowe lub Automatyzowanie kompilacji obrazów, gdy zespół zatwierdzenia kodu do repozytorium Git. Tworzenie wieloetapowych zadań do automatyzowania tworzenia, testowania i stosowanie poprawek wiele obrazów kontenera równolegle w chmurze.

Platforma Azure udostępnia narzędzia, w tym interfejsu wiersza polecenia platformy Azure, witryny Azure portal i obsługa interfejsu API do zarządzania usługi rejestry kontenerów platformy Azure. Opcjonalnie Zainstaluj [rozszerzenie Docker Extension for Visual Studio Code](https://code.visualstudio.com/docs/azure/docker) i [konta platformy Azure](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account) rozszerzenie, aby pracować z rejestrów kontenerów platformy Azure. Ściąganie i wypychanie obrazów do usługi Azure container registry lub uruchamiania zadań podrzędnych usługi ACR, wszystko to w ramach programu Visual Studio Code.

## <a name="key-concepts"></a>Kluczowe pojęcia

* **Rejestr** — utwórz przynajmniej jeden rejestr kontenerów w subskrypcji platformy Azure. Rejestry są dostępne w ramach trzech jednostek SKU: [Podstawowa, standardowa i Premium](container-registry-skus.md), każdy z nich obsługuje integrację elementów webhook, uwierzytelnianie rejestru za pomocą usługi Azure Active Directory i funkcji usuwania. Utwórz rejestr w tej samej lokalizacji platformy Azure, w której znajdują się wdrożenia, aby korzystać z lokalnego, bliskiego sieciowo magazynu obrazów kontenerów. Użyj funkcji [replikacji geograficznej](container-registry-geo-replication.md) dostępnej w rejestrach w warstwie Premium dla scenariuszy zaawansowanej replikacji i dystrybucji obrazu kontenera. W pełni kwalifikowana nazwa rejestru ma format `myregistry.azurecr.io`.

  Możesz [kontrolować dostęp](container-registry-authentication.md) do rejestru kontenerów za pomocą tożsamości platformy Azure, [jednostki usługi](../active-directory/develop/app-objects-and-service-principals.md) wspieranej przez usługę Azure Active Directory lub podanego konta administratora. Zaloguj się do rejestru przy użyciu wiersza polecenia platformy Azure lub standardem `docker login` polecenia.

* **Repozytorium** — Rejestr zawiera przynajmniej jednej stanowiące wirtualnego grupę obrazów kontenerów przy użyciu tej samej nazwie, ale różnych znaczników lub skróty. Usługa Azure Container Registry obsługuje wielopoziomowe przestrzenie nazw repozytoriów. Dzięki wielopoziomowym przestrzeniom nazw można grupować kolekcje obrazów związanych z określoną aplikacją lub kolekcje aplikacji związanych z określonymi zespołami programistycznymi lub operacyjnymi. Na przykład:

  * `myregistry.azurecr.io/aspnetcore:1.0.1` reprezentuje obraz całej firmy
  * `myregistry.azurecr.io/warrantydept/dotnet-build` reprezentuje obraz używany do tworzenia aplikacji platformy .NET współdzielony przez dział gwarancji
  * `myregistry.azurecr.io/warrantydept/customersubmissions/web` reprezentuje obraz sieci Web zgrupowany w aplikacji zgłoszeń klientów i należący do działu gwarancji

* **Obraz** — każdy obraz jest przechowywany w repozytorium i jest migawką tylko do odczytu kontenera zgodnego z platformą Docker. Rejestry kontenerów platformy Azure mogą obejmować zarówno obrazy systemu Windows, jak i Linux. Możesz kontrolować nazwy obrazów wszystkich wdrożeń kontenera. Użyj standardowych [poleceń platformy Docker](https://docs.docker.com/engine/reference/commandline/), aby wypchnąć obrazy do repozytorium lub aby ściągnąć je z repozytorium. Oprócz obrazów kontenerów usługa Azure Container Registry umożliwia przechowywanie [formatów powiązanej zawartości](container-registry-image-formats.md), takich jak [wykresy Helm](container-registry-helm-repos.md), które są używane do wdrażania aplikacji w usłudze Kubernetes.

* **Kontener** — mianem kontenera określa się aplikację wraz z jej zależnościami, opakowaną w kompletny system plików, włączając w to kod, środowisko uruchomieniowe, narzędzia systemowe i biblioteki. Uruchom kontenery platformy Docker w oparciu o obrazy systemu Windows lub Linux, które zostały ściągnięte z rejestru kontenerów. Kontenery uruchomione na jednym komputerze współdzielą jądro systemu operacyjnego. Kontenery platformy Docker są całkowicie przenośne na wszystkie główne dystrybucje systemu Linux oraz systemy macOS i Windows.

## <a name="azure-container-registry-tasks"></a>Usługa Azure Container Registry Tasks

Usługa [Azure Container Registry Tasks](container-registry-tasks-overview.md) (ACR Tasks) to pakiet funkcji usługi Azure Container Registry, który udostępnia uproszczone i wydajne kompilacje obrazów kontenerów platformy Docker na platformie Azure. Usługa ACR Tasks umożliwia rozszerzenie wewnętrznej pętli programowania o chmurę dzięki odciążaniu operacji `docker build` na platformie Azure. Konfiguruj zadania kompilacji w celu automatyzowania potoku poprawiania platformy i systemu operacyjnego kontenera, gdy zespół zatwierdza kod w ramach kontroli źródła.

[Zadania wieloetapowe](container-registry-tasks-overview.md#multi-step-tasks) Podaj definicji zadań na podstawie kroku i wykonywania dla tworzenia, testowania i poprawianie obrazów kontenerów w chmurze. Kroki zadań definiują pojedyncze operacje tworzenia i wypychania obrazu kontenera. Mogą one również definiować wykonanie jednego lub kilku kontenerów, z każdym krokiem używającym kontenera jako jego środowiska wykonawczego.

## <a name="next-steps"></a>Kolejne kroki

* [Create a container registry using the Azure portal](container-registry-get-started-portal.md) (Tworzenie rejestru kontenerów za pomocą witryny Azure Portal)
* [Create a container registry using the Azure CLI](container-registry-get-started-azure-cli.md) (Tworzenie rejestru kontenerów za pomocą interfejsu wiersza polecenia platformy Azure)
* [Automatyzacja systemu operacyjnego i poprawianie struktury przy użyciu usługi ACR Tasks](container-registry-tasks-overview.md)
