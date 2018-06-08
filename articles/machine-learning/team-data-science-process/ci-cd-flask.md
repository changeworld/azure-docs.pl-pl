---
title: 'Opracowywania oprogramowania dla aplikacji analizy sztucznego (AI): tworzenie potoku integracji ciągłej na platformie Azure przy użyciu aplikacji Docker, Kubernetes & platformy Python Flask'
description: 'Opracowywania oprogramowania dla aplikacji analizy sztucznego (AI): tworzenie potoku integracji ciągłej na platformie Azure przy użyciu rozwiązania Docker i Kubernetes'
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
ms.openlocfilehash: 233da393bb9e030d885ce588f4841dc1c707c1cb
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/07/2018
ms.locfileid: "34836270"
---
# <a name="devops-for-artificial-intelligence-ai-applications-creating-continuous-integration-pipeline-on-azure-using-docker-and-kubernetes"></a>Opracowywania oprogramowania dla aplikacji analizy sztucznego (AI): tworzenie potoku ciągłej integracji na platformie Azure przy użyciu rozwiązania Docker i Kubernetes
Aplikacja AI są często dwóch strumieni pracy, budowania modeli uczenia maszyny i deweloperom tworzenie aplikacji i ujawnienie go użytkownikom końcowym zużyje analityków danych. W tym artykule przedstawiony sposób wdrożenia ciągłej integracji (CI) / ciągłej dostarczania (CD) w potoku aplikacji AI. Aplikacja AI jest kombinacją kodu aplikacji osadzonych z modelem pretrained machine learning (ML). W tym artykule możemy są pobierania pretrained modelu z konta magazynu obiektów blob platformy Azure prywatne, może to być również konta usług AWS S3. Używamy aplikacji sieci web platformy flask prostego języka python dla tego artykułu.

> [!NOTE]
> Jest kilka sposobów, może zostać wykonana CI/CD. Brak alternatywy dla narzędzia i inne wymagania wstępne wymienione poniżej. Jak opracowywania dodatkowej zawartości, firma Microsoft publikuje te.
>
>

## <a name="github-repository-with-document-and-code"></a>Repozytorium GitHub z dokumentu i kod
Można pobrać kodu źródłowego z [GitHub](https://github.com/Azure/DevOps-For-AI-Apps). A [szczegółowy samouczek](https://github.com/Azure/DevOps-For-AI-Apps/blob/master/Tutorial.md) jest również dostępna.

## <a name="pre-requisites"></a>Wymagania wstępne
Poniżej przedstawiono wymagania wstępne dla następującego elementu konfiguracji/CD potoku opisanych poniżej:
* [Visual Studio Team Services konta](https://docs.microsoft.com/en-us/vsts/accounts/create-account-msa-or-work-student)
* [Interfejs wiersza polecenia platformy Azure](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli?view=azure-cli-latest)
* [Azure klastra usługi kontenera (AKS) z systemem Kubernetes](https://docs.microsoft.com/en-us/azure/container-service/kubernetes/container-service-tutorial-kubernetes-deploy-cluster)
* [Konto platformy Azure rejestrze kontenera (ACR)](https://docs.microsoft.com/en-us/azure/container-registry/container-registry-get-started-portal)
* [Zainstaluj Kubectl w celu uruchomienia polecenia Kubernetes klastra.](https://kubernetes.io/docs/tasks/tools/install-kubectl/) Firma Microsoft będzie on potrzebny można pobrać konfiguracji z klastrem usługi ACS. 
* Rozwidlenia repozytorium na koncie usługi GitHub.

## <a name="description-of-the-cicd-pipeline"></a>Opis elementu konfiguracji/CD potoku
Kopnięć potoku poza dla każdego nowego zatwierdzenie, uruchom zestaw testów, jeśli przebiegów testowych przyjmuje ostatniej kompilacji pakiety go w kontenerze Docker. Kontener jest następnie wdrażane za pomocą usługi kontenera platformy Azure (ACS), a następnie obrazy są bezpiecznie przechowywane w rejestrze kontenera platformy Azure (ACR). ACS jest uruchomiony Kubernetes Zarządzanie klastrem kontenera, ale można wybrać Docker Swarm lub Mesos.

Aplikacja bezpiecznie ściąga najnowszego modelu z konta usługi Azure Storage i pakiety który jako części aplikacji. Wdrożonej aplikacji ma kod aplikacji i model usługi uczenie Maszynowe dostarczana w jeden kontener. Zapewnia to oddzielenie aplikacji deweloperów i danych służące, aby upewnić się, że ich aplikacji produkcyjnej zawsze działa z najnowszą model usługi uczenie Maszynowe najnowsze kodu.

Poniżej podano Architektura potoku. 

![Architektura](./media/ci-cd-flask/Architecture.PNG?raw=true)

## <a name="steps-of-the-cicd-pipeline"></a>Etapy potoku CI/CD
1. Projektant pracować IDE wybranych przez nich w kodzie aplikacji.
2. One zatwierdzić kod do kontroli źródła wybranych przez nich (VSTS ma dobrą obsługę różnych kontrolki źródła)
3. Oddzielnie naukowca danych działają na temat tworzenia ich modelu.
4. Po wszystkiego publikują one modelu do repozytorium modelu w tym przypadku użyto konta magazynu obiektów blob. Mogły być łatwo zastąpione z usługą zarządzania modelu Workbench uczenia Maszynowego Azure za pomocą ich interfejsów API REST.
5. Kompilacja zostało rozpoczęte w VSTS oparte na zatwierdzenie w serwisie GitHub.
6. Potok kompilacji programu VSTS ściąga najnowszego modelu z kontenera obiektów Blob i tworzy kontener.
7. VSTS wypychanie obrazu do repozytorium obrazów prywatnych w rejestrze kontenera platformy Azure
8. Zgodnie z ustalonym harmonogramem (co noc) wersja potoku zostało rozpoczęte.
9. Najnowsze obrazu z ACR jest pobierane i wdrożyć w klastrze Kubernetes na ACS.
10. Żądania użytkowników dla aplikacji przechodzi przez serwer DNS.
11. Serwer DNS przekazuje żądanie do modułu równoważenia obciążenia i wysyła odpowiedź do użytkownika.

## <a name="next-steps"></a>Kolejne kroki
* Zapoznaj się [samouczek]((https://github.com/Azure/DevOps-For-AI-Apps/blob/master/Tutorial.md)) wykonaj szczegóły i wdrażanie własnego elementu konfiguracji/CD potoku aplikacji.

## <a name="references"></a>Dokumentacja
* [Proces nauki danych zespołu (TDSP)](https://aka.ms/tdsp)
* [Uczenie maszynowe Azure (AML)](https://docs.microsoft.com/en-us/azure/machine-learning/service/)
* [Visual Studio Team Services (VSTS)](https://www.visualstudio.com/vso/)
* [Usług Azure Kubernetes (AKS)](https://docs.microsoft.com/en-us/azure/aks/intro-kubernetes)