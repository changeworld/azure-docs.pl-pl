---
title: Omówienie Wpięć i Azure | Dokumentacja firmy Microsoft
description: Host kompilacji Wpięć, a następnie wdrożyć serwer automatyzacji platformy Azure i użycia zasobów obliczeniowych i magazynu systemu Azure do rozszerzenia Twojej ciągłej integracji i wdrażania potoki (CI/CD).
services: jenkins
author: rloutlaw
manager: justhe
ms.service: jenkins
ms.devlang: NA
ms.topic: article
ms.workload: na
ms.date: 08/22/2017
ms.author: routlaw
ms.custom: mvc
ms.openlocfilehash: ca4a6cb886b0453848dc7b29e15de2063878a65d
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/05/2018
---
# <a name="azure-and-jenkins"></a>Azure i Wpięć

[Wpięć](https://jenkins.io/) jest serwerem popularnych automatyzacji open source umożliwia konfigurowanie ciągłej integracji i dostarczania (CI/CD) dla projektów oprogramowania. Można obsługiwać wdrożenia Wpięć na platformie Azure lub rozszerzyć istniejącą konfigurację Wpięć przy użyciu zasobów platformy Azure. Wtyczki Wpięć są także dostępne do uproszczenia CI/CD aplikacji na platformie Azure.

Ten artykuł obejmuje wprowadzenie do korzystania z Wpięć usługi Azure, której opisano podstawowe Azure funkcje dostępne dla użytkowników Wpięć. Aby rozpocząć pracę z Wpięć serwera na platformie Azure, zobacz nasze [szybkiego startu](install-jenkins-solution-template.md).

## <a name="host-your-jenkins-servers-in-azure"></a>Hostować serwery Wpięć na platformie Azure

Wpięć hosta na platformie Azure do centralizacji automatyzacji programu kompilacji i skalowania wdrożenia w miarę wzrostu potrzeb projektów oprogramowania. Wpięć można wdrożyć przy użyciu platformy Azure:
 
- [Szablon rozwiązania Wpięć](install-jenkins-solution-template.md) w portalu Azure Marketplace.
- [Maszyny wirtualne platformy Azure](/azure/virtual-machines/linux/overview). Zobacz nasze [samouczek](/azure/virtual-machines/linux/tutorial-jenkins-github-docker-cicd) można utworzyć wystąpienia elementu Wpięć na maszynie Wirtualnej.
- Na Kubernetes klastra z systemem [usługi kontenera platformy Azure](/azure/container-service/kubernetes/container-service-kubernetes-walkthrough), zobacz nasze [porad](/azure/container-service/kubernetes/container-service-kubernetes-jenkins).

Monitorowanie i zarządzanie nimi z Wpięć Azure wdrożenia przy użyciu [analizy dzienników](/azure/log-analytics/log-analytics-overview) i [interfejsu wiersza polecenia Azure](/cli/azure).

## <a name="scale-your-build-automation-on-demand"></a>Skalowanie programu automatyzacji kompilacji na żądanie

Dodaj agentów kompilacji do istniejącego wdrożenia Wpięć można skalować możliwości kompilacji Wpięć jako liczba kompilacji i złożoność zadań i zwiększyć potoków. Można je uruchomić agentów kompilacji na maszynach wirtualnych Azure za pomocą [wtyczkę Azure VM agentów](jenkins-azure-vm-agents.md). Zobacz nasze [samouczek](/azure/jenkins/jenkins-azure-vm-agents) więcej szczegółów.

Raz skonfigurowano [nazwy głównej usługi Azure](/azure/azure-resource-manager/resource-group-overview), Wpięć zadania i potoki mogą korzystać z tego poświadczenia:

- Bezpiecznie przechowywać i archiwum kompilacji artefaktów [usługi Azure Storage](/azure/storage/common/storage-introduction) przy użyciu [wtyczki usługi Azure Storage](https://plugins.jenkins.io/windows-azure-storage). Przegląd [Wpięć magazynu porad](/azure/storage/common/storage-java-jenkins-continuous-integration-solution) Aby dowiedzieć się więcej.
- Konfigurowanie zasobów Azure przy użyciu oraz zarządzanie nimi [interfejsu wiersza polecenia Azure](/azure/jenkins/execute-cli-jenkins-pipeline).

## <a name="deploy-your-code-into-azure-services"></a>Wdrażanie kodu do usług platformy Azure

Wpięć wtyczek umożliwia wdrażanie aplikacji na platformie Azure w ramach potoków z Wpięć CI/CD. Wdrażanie w [usłudze Azure App Service](/azure/app-service/) i [usługi kontenera platformy Azure](/azure/container-service/kubernetes/) umożliwia etap, testu i aktualizacje wersji aplikacji bez konieczności zarządzania podstawowej infrastruktury.

 Dodatki plug-in są dostępne do wdrożenia na następujących usług i środowisk:

- [Aplikacja sieci Web platformy Azure w systemie Linux](/azure/app-service/containers/app-service-linux-intro). Zobacz [samouczek](java-deploy-webapp-tutorial.md) rozpocząć pracę.
- [Aplikacja sieci Web platformy Azure](/azure/app-service/app-service-web-overview). Zobacz [porad](deploy-Jenkins-app-service-plugin.md) rozpocząć pracę.

