---
title: Utwórz ciągłej integracji platformy Azure potok — zespołu danych dla celów naukowych
description: 'Metodyka DevOps na potrzeby aplikacji sztucznej inteligencji (AI): Tworzenie potoku ciągłej integracji na platformie Azure przy użyciu platformy Docker i Kubernetes'
services: machine-learning
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 05/22/2018
ms.author: tdsp
ms.custom: seodec18, previous-author=jainr, previous-ms.author=jainr
ms.openlocfilehash: d99149f8112c19a07208523a1ee26ba1c36e5362
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "62103569"
---
# <a name="creating-continuous-integration-pipeline-on-azure-using-docker-kubernetes-and-python-flask-application"></a>Tworzenie potoku ciągłej integracji na platformie Azure za pomocą aplikacji platformy Docker, Kubernetes i Python Flask
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
* [Konto usługi Azure Container Registry (ACR)](https://docs.microsoft.com/azure/container-registry/container-registry-get-started-portal)
* [Zainstaluj narzędzie Kubectl w celu uruchomienia poleceń w klastrze Kubernetes.](https://kubernetes.io/docs/tasks/tools/install-kubectl/) Firma Microsoft będzie on potrzebny można pobrać konfiguracji z klastra usługi ACS. 
* Utwórz rozwidlenie repozytorium do konta usługi GitHub.

## <a name="description-of-the-cicd-pipeline"></a>Opis potoku ciągłej integracji/ciągłego wdrażania
Kopnięć potoku wyłączone dla każdego nowego zatwierdzenia i uruchom zestaw testów, jeśli przebiegów testowych pobiera najnowszą kompilację pakiety go w kontenerze platformy Docker. Kontener jest następnie wdrażana przy użyciu usługi Azure Container Service (ACS) i obrazy są bezpiecznie przechowywane w usłudze Azure Container Registry (ACR). Usługi ACS jest uruchomiona Kubernetes dla zarządzania klastrem kontenera, ale można wybrać, Docker Swarm lub Mesos.

Aplikacja bezpiecznie pobiera najnowszy model z konta usługi Azure Storage i pakiety, jako część aplikacji. Wdrożonej aplikacji ma kod aplikacji i modelu usługi uczenie Maszynowe spakowany jako jeden kontener. Oddziela to aplikacja deweloperów i analityków danych, aby upewnić się, że ich aplikacji w środowisku produkcyjnym zawsze działa najnowszy kod z najnowszy model uczenia Maszynowego.

Architektura potoku są podane poniżej. 

![Architektura](./media/ci-cd-flask/Architecture.PNG?raw=true)

## <a name="steps-of-the-cicd-pipeline"></a>Etapy potoku ciągłej integracji/ciągłego wdrażania
1. Deweloper pracują nad IDE wybranych przez nich w kodzie aplikacji.
2. Mogą oni zatwierdzać kod do kontroli źródła wybranych przez nich (DevOps platformy Azure ma dobrą obsługę różnych kontroli źródła)
3. Oddzielnie badacz danych działają na temat tworzenia modelu.
4. Po wszystkiego, publikują modelu do repozytorium modelu, w tym przypadku użyto konta usługi blob storage. 
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
* [Usługa Azure DevOps](https://www.visualstudio.com/vso/)
* [Usługi Azure Kubernetes (AKS)](https://docs.microsoft.com/azure/aks/intro-kubernetes)
