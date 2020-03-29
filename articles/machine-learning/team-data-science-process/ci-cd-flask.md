---
title: Tworzenie potoku ciągłej integracji/ciągłego wdrażania za pomocą potoków platformy Azure — proces nauki o danych zespołu
description: Tworzenie ciągłej integracji i ciągłego dostarczania potoku dla aplikacji sztucznej inteligencji (AI) przy użyciu platformy Docker i Kubernetes.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=jainr, previous-ms.author=jainr
ms.openlocfilehash: 42433ec419ac9e02077cd0359e18b5114206f27d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76721833"
---
# <a name="create-cicd-pipelines-for-ai-apps-using-azure-pipelines-docker-and-kubernetes"></a>Tworzenie potoków ciągłej integracji/ciągłego wdrażania dla aplikacji AI przy użyciu potoków azure, platformy Docker i Kubernetes

Aplikacja sztucznej inteligencji (AI) to kod aplikacji osadzony w wstępnie wyszkolonym modelu uczenia maszynowego (ML). Zawsze są dwa strumienie pracy dla aplikacji AI: analitycy danych skompilować model ml i deweloperzy aplikacji tworzyć aplikację i udostępnić go użytkownikom końcowym do korzystania. W tym artykule opisano sposób implementacji potoku ciągłej integracji i ciągłego dostarczania (CI/CD) dla aplikacji AI, która osadza model ML w kodzie źródłowym aplikacji. Przykładowy kod i samouczek używają aplikacji sieci web Python Flask i pobierają wstępnie przeszkolony model z prywatnego konta magazynu obiektów blob platformy Azure. Można również użyć konta magazynu AWS S3.

> [!NOTE]
> Poniższy proces jest jednym z kilku sposobów wykonywania ciągłej integracji/ciągłego wdrażania. Istnieją alternatywy dla tego narzędzia i wymagań wstępnych.

## <a name="source-code-tutorial-and-prerequisites"></a>Kod źródłowy, samouczek i wymagania wstępne

Możesz pobrać [kod źródłowy](https://github.com/Azure/DevOps-For-AI-Apps) i [szczegółowy samouczek](https://github.com/Azure/DevOps-For-AI-Apps/blob/master/Tutorial.md) z GitHub. Wykonaj kroki samouczka, aby zaimplementować potok ciągłej integracji/ciągłego wdrażania dla własnej aplikacji.

Aby użyć pobranego kodu źródłowego i samouczka, potrzebne są następujące wymagania wstępne: 

- [Repozytorium kodu źródłowego](https://github.com/Azure/DevOps-For-AI-Apps) rozwieł się na twoim koncie GitHub
- [Organizacja azure devops](/azure/devops/organizations/accounts/create-organization-msa-or-work-student)
- [Interfejs wiersza polecenia platformy Azure](/cli/azure/install-azure-cli)
- [Klaster usługi kontenera platformy Azure dla usługi Kubernetes (AKS)](/azure/container-service/kubernetes/container-service-tutorial-kubernetes-deploy-cluster)
- [Kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl/) do uruchamiania poleceń i pobierania konfiguracji z klastra AKS 
- [Konto usługi Azure Container Registry (ACR)](/azure/container-registry/container-registry-get-started-portal)

## <a name="cicd-pipeline-summary"></a>Podsumowanie potoku ciągłej integracji/dysku CD

Każde nowe zatwierdzenie Git rozpoczyna się potoku kompilacji. Kompilacja bezpiecznie pobiera najnowszy model ml z konta magazynu obiektów blob i pakuje go z kodem aplikacji w jednym kontenerze. To oddzielenie tworzenia aplikacji i strumienipracy do nauki o danych zapewnia, że aplikacja produkcyjna jest zawsze uruchomiony najnowszy kod z najnowszym modelem uczenia maszynowego. Jeśli aplikacja przejdzie testy, potok bezpiecznie przechowuje obraz kompilacji w kontenerze platformy Docker w acr. Potok wydania następnie wdraża kontener przy użyciu usługi AKS. 

## <a name="cicd-pipeline-steps"></a>Kroki potoku ciągłej integracji/ciągłego wdrażania

Na poniższym diagramie i w krokach opisano architekturę potoku ciągłej integracji/ciągłego wdrażania:

![Architektura potoku ciągłej integracji/ciągłego wdrażania](./media/ci-cd-flask/architecture.png)

1. Deweloperzy pracują nad kodem aplikacji w wybranym przez siebie identyfikatorze IDE.
2. Deweloperzy zatwierdzają kod do usługi Azure Repos, GitHub lub innego dostawcy kontroli źródła Git. 
3. Osobno analitycy danych pracują nad rozwojem swojego modelu uczenia maszynowego.
4. Analitycy danych publikują gotowy model do repozytorium modelu, w tym przypadku konta magazynu obiektów blob. 
5. Usługa Azure Pipelines rozpoczyna kompilację opartą na zatwierdzeniu git.
6. Potok kompilacji pobiera najnowszy model ml z magazynu obiektów blob i tworzy kontener.
7. Potok wypycha obraz kompilacji do prywatnego repozytorium obrazów w acr.
8. Potok wydania rozpoczyna się na podstawie pomyślnej kompilacji.
9. Potok pobiera najnowszy obraz z usługi ACR i wdraża go w klastrze Kubernetes w u usługi AKS.
10. Żądania użytkowników dla aplikacji przechodzą przez serwer DNS.
11. Serwer DNS przekazuje żądania do modułu równoważenia obciążenia i wysyła odpowiedzi z powrotem do użytkowników.

## <a name="see-also"></a>Zobacz też

- [Proces nauki o danych zespołowych (TDSP)](/azure/machine-learning/team-data-science-process/)
- [Uczenie maszynowe platformy Azure (AML)](/azure/machine-learning/)
- [Azure DevOps](https://azure.microsoft.com/services/devops/)
- [Usługi Kubernetes platformy Azure (AKS)](/azure/aks/intro-kubernetes)
