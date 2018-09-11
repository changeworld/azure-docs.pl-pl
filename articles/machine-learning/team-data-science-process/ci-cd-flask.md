---
title: 'Metodyka DevOps na potrzeby aplikacji sztucznej inteligencji (AI): tworzenie potoku integracji ciągłe na platformie Azure za pomocą aplikacji platformy Docker, Kubernetes i Python Flask'
description: 'Metodyka DevOps na potrzeby aplikacji sztucznej inteligencji (AI): tworzenie potoku integracji ciągłe na platformie Azure przy użyciu platformy Docker i Kubernetes'
services: machine-learning, team-data-science-process
documentationcenter: ''
author: jainr
manager: deguhath
editor: cgronlun
ms.assetid: b8fbef77-3e80-4911-8e84-23dbf42c9bee
ms.service: machine-learning
ms.component: team-data-science-process
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/22/2018
ms.author: jainr
ms.openlocfilehash: b0368e742c990feed626a1c4982bfedc35785b49
ms.sourcegitcommit: f3bd5c17a3a189f144008faf1acb9fabc5bc9ab7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/10/2018
ms.locfileid: "44304292"
---
# <a name="devops-for-artificial-intelligence-ai-applications-creating-continuous-integration-pipeline-on-azure-using-docker-and-kubernetes"></a>Metodyka DevOps na potrzeby aplikacji sztucznej inteligencji (AI): tworzenie potoku ciągłej integracji na platformie Azure przy użyciu platformy Docker i Kubernetes
Dla aplikacji sztucznej Inteligencji są często dwóch strumieni pracy analityków danych, tworzenie modeli uczenia maszynowego i deweloperów aplikacji, tworzenie aplikacji i ujawnienie go użytkownikom końcowym z. W tym artykule pokażemy, jak do zaimplementowania ciągłej integracji (CI) / ciągłe dostarczanie (CD) potoku dla aplikacji sztucznej Inteligencji. Aplikacji sztucznej Inteligencji składa się z kodu aplikacji, wbudowane z modelu uczenia (ML) wstępnie przetrenowane maszyny. W tym artykule firma Microsoft pobiera pretrained modelu z konta magazynu prywatnego obiektu blob platformy Azure, może to być także konta usług AWS S3. Będziemy używać aplikacji internetowej platformy flask prostego języka python do tego artykułu.

> [!NOTE]
> Jest to jeden z kilku sposobów wykonania ciągłej integracji/ciągłego wdrażania. Brak alternatywy dla narzędzi i innych warunków wstępnych wymienionych poniżej. Jak możemy opracowanie dodatkowej zawartości, opublikujemy odpowiednie te.
>
>

## <a name="github-repository-with-document-and-code"></a>Repozytorium GitHub zawierające kod i dokumentów
Możesz pobrać kod źródłowy z [GitHub](https://github.com/Azure/DevOps-For-AI-Apps). A [szczegółowy samouczek](https://github.com/Azure/DevOps-For-AI-Apps/blob/master/Tutorial.md) jest również dostępna.

## <a name="pre-requisites"></a>Wymagania wstępne
Poniżej przedstawiono wymagania wstępne do korzystania z potoku CI/CD opisane poniżej:
* [Organizacja DevOps platformy Azure](https://docs.microsoft.com/azure/devops/organizations/accounts/create-organization-msa-or-work-student)
* [Interfejs wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)
* [Klaster usługi Azure Container Service (AKS) uruchamianie rozwiązania Kubernetes](https://docs.microsoft.com/azure/container-service/kubernetes/container-service-tutorial-kubernetes-deploy-cluster)
* [Konto usługi Azure Container rejestru (ACR)](https://docs.microsoft.com/azure/container-registry/container-registry-get-started-portal)
* [Zainstaluj narzędzie Kubectl w celu uruchomienia poleceń w klastrze Kubernetes.](https://kubernetes.io/docs/tasks/tools/install-kubectl/) Firma Microsoft będzie on potrzebny można pobrać konfiguracji z klastra usługi ACS. 
* Utwórz rozwidlenie repozytorium do konta usługi GitHub.

## <a name="description-of-the-cicd-pipeline"></a>Opis potoku ciągłej integracji/ciągłego wdrażania
Kopnięć potoku wyłączone dla każdego nowego zatwierdzenia i uruchom zestaw testów, jeśli przebiegów testowych pobiera najnowszą kompilację pakiety go w kontenerze platformy Docker. Kontener jest następnie wdrażana przy użyciu usługi Azure container service (ACS) i obrazy są bezpiecznie przechowywane w usłudze Azure container registry (ACR). Usługi ACS jest uruchomiona Kubernetes dla zarządzania klastrem kontenera, ale można wybrać, Docker Swarm lub Mesos.

Aplikacja bezpiecznie pobiera najnowszy model z konta usługi Azure Storage i pakiety, jako część aplikacji. Wdrożonej aplikacji ma kod aplikacji i modelu usługi uczenie Maszynowe spakowany jako jeden kontener. Oddziela to aplikacja deweloperów i analityków danych, aby upewnić się, że ich aplikacji w środowisku produkcyjnym zawsze działa najnowszy kod z najnowszy model uczenia Maszynowego.

Architektura potoku są podane poniżej. 

![Architektura](./media/ci-cd-flask/Architecture.PNG?raw=true)

## <a name="steps-of-the-cicd-pipeline"></a>Etapy potoku ciągłej integracji/ciągłego wdrażania
1. Deweloper pracują nad IDE wybranych przez nich w kodzie aplikacji.
2. Mogą oni zatwierdzać kod do kontroli źródła wybranych przez nich (DevOps platformy Azure ma dobrą obsługę różnych kontroli źródła)
3. Oddzielnie badacz danych działają na temat tworzenia modelu.
4. Po wszystkiego, publikują modelu do repozytorium modelu, w tym przypadku użyto konta usługi blob storage. To może łatwo zastąpione Usługa Zarządzanie modelami w usłudze Azure ML Workbench za pośrednictwem swoich interfejsów API REST.
5. Kompilacja rozpoczęła się w DevOps platformy Azure, w oparciu o zatwierdzenia w usłudze GitHub.
6. Usługa Azure potoku metodyki DevOps kompilacji pobiera najnowszy model z kontenera obiektów Blob i tworzy kontener.
7. Usługa Azure DevOps wypycha obraz do repozytorium prywatnego obrazu w usłudze Azure Container Registry
8. Zgodnie z ustalonym harmonogramem (w godzinach nocnych) rozpocznie się kompilowanie potoku tworzenia wersji.
9. Najnowsze obrazu z rejestru Azure container Registry jest pobierane i wdrożonych w klastrze Kubernetes w ACS.
10. Żądania użytkowników dla aplikacji odbywa się przez serwer DNS.
11. Serwer DNS przekazuje żądanie do modułu równoważenia obciążenia i wysyła odpowiedź z powrotem do użytkownika.

## <a name="next-steps"></a>Kolejne kroki
* Zapoznaj się [samouczek](https://github.com/Azure/DevOps-For-AI-Apps/blob/master/Tutorial.md) wykonaj szczegółowe informacje i zaimplementować własny potoku ciągłej integracji/ciągłego Dostarczania dla aplikacji.

## <a name="references"></a>Dokumentacja
* [Zespołowe przetwarzanie danych naukowych (TDSP)](https://aka.ms/tdsp)
* [Usługi Azure Machine Learning (AML)](https://docs.microsoft.com/azure/machine-learning/service/)
* [Visual Studio Team Services (VSTS)](https://www.visualstudio.com/vso/)
* [Usługi Azure Kubernetes (AKS)](https://docs.microsoft.com/azure/aks/intro-kubernetes)
