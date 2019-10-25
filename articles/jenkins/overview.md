---
title: Omówienie serwera Jenkins i platformy Azure
description: Hostuj serwer automatyzacji kompilacji i wdrażania Jenkins na platformie Azure i używaj zasobów obliczeniowych i magazynu platformy Azure w celu rozszerzania potoków ciągłej integracji i wdrażania (CI/CD).
ms.service: jenkins
keywords: jenkins, azure, devops, omówienie
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.topic: overview
ms.date: 10/23/2019
ms.openlocfilehash: 9b4ceb144408e0ac1886aa3a7e0e0f48ac4baba3
ms.sourcegitcommit: 7efb2a638153c22c93a5053c3c6db8b15d072949
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/24/2019
ms.locfileid: "72881950"
---
# <a name="azure-and-jenkins"></a>Platforma Azure i serwer Jenkins

[Jenkins](https://jenkins.io/) to popularny serwer automatyzacji typu open-source używany do konfigurowania ciągłej integracji i dostarczania (CI/CD) dla projektów dotyczących oprogramowania. Można hostować swoje wdrożenie narzędzia Jenkins na platformie Azure lub rozszerzyć istniejącą konfigurację narzędzia Jenkins za pomocą zasobów platformy Azure. Są również dostępne wtyczki serwera Jenkins, które umożliwiają upraszczanie ciągłej integracji/ciągłego wdrażania aplikacji na platformie Azure.

Ten artykuł stanowi wprowadzenie do zagadnień dotyczących używania platformy Azure z narzędziem Jenkins i zawiera szczegółowe informacje na temat podstawowych funkcji platformy Azure dostępnych dla użytkowników serwera Jenkins. Aby uzyskać więcej informacji na temat rozpoczynania pracy z własnym serwerem Jenkins na platformie Azure, zobacz [Tworzenie serwera Jenkins na platformie Azure](install-jenkins-solution-template.md).

## <a name="host-your-jenkins-servers-in-azure"></a>Hostowanie serwerów Jenkins na platformie Azure

Hostowanie serwera Jenkins na platformie Azure pozwala na centralizowanie automatyzacji kompilacji i skalowanie wdrożenia w miarę wzrostu potrzeb projektów dotyczących oprogramowania. Narzędzie Jenkins można wdrożyć na platformie Azure za pomocą:
 
- [Szablonu rozwiązania Jenkins](install-jenkins-solution-template.md) dostępnego w witrynie Azure Marketplace.
- [Maszyn wirtualnych platformy Azure](/azure/virtual-machines/linux/overview). Zobacz nasz [samouczek](/azure/virtual-machines/linux/tutorial-jenkins-github-docker-cicd), aby utworzyć wystąpienie serwera Jenkins na maszynie wirtualnej.
- Aby uzyskać informacje na temat klastra Kubernetes działającego w usłudze [Azure Container Service](/azure/container-service/kubernetes/container-service-kubernetes-walkthrough), zobacz nasze [instrukcje](/azure/container-service/kubernetes/container-service-kubernetes-jenkins).

Monitoruj wdrożenie usługi Azure Jenkins i zarządzaj nimi za pomocą [dzienników Azure monitor](/azure/log-analytics/log-analytics-overview) i [interfejsu wiersza polecenia platformy Azure](/cli/azure).

## <a name="scale-your-build-automation-on-demand"></a>Skalowanie automatyzacji kompilacji na żądanie

Dodawanie agentów kompilacji do istniejącego wdrożenia serwera Jenkins umożliwia skalowanie pojemności kompilacji narzędzia Jenkins w miarę wzrostu liczby kompilacji oraz stopnia złożoności zadań i potoków. Tych agentów kompilacji można uruchamiać na maszynach wirtualnych platformy Azure przy użyciu [wtyczki agentów maszyny wirtualnej platformy Azure](jenkins-azure-vm-agents.md). Zobacz nasz [samouczek](/azure/jenkins/jenkins-azure-vm-agents), aby uzyskać więcej informacji.

Po skonfigurowaniu przy użyciu [jednostki usługi platformy Azure](/azure/azure-resource-manager/resource-group-overview) potoki i zadania serwera Jenkins mogą korzystać z tego poświadczenia w celu:

- Bezpiecznie Przechowuj i Archiwizuj artefakty kompilacji w [usłudze Azure Storage](/azure/storage/common/storage-introduction) przy użyciu [wtyczki usługi Azure Storage](https://plugins.jenkins.io/windows-azure-storage). Aby dowiedzieć się więcej, zapoznaj się z [instrukcjami dotyczącymi magazynu serwera Jenkins](/azure/storage/common/storage-java-jenkins-continuous-integration-solution).
- Zarządzania zasobami platformy Azure i konfigurowania ich za pomocą [interfejsu wiersza polecenia platformy Azure](/azure/jenkins/execute-cli-jenkins-pipeline).

## <a name="deploy-your-code-into-azure-services"></a>Wdrażanie swojego kodu w usługach platformy Azure

Wtyczki serwera Jenkins umożliwiają wdrażanie aplikacji na platformie Azure w ramach potoków ciągłej integracji/ciągłego wdrażania narzędzia Jenkins. Wdrażanie w usługach [usługi Azure App Service](/azure/app-service/) i [Azure Container Service](/azure/container-service/kubernetes/) pozwala na wdrażanie przejściowe, testowanie i wydawanie aktualizacji do aplikacji bez konieczności zarządzania podstawową infrastrukturą.

 Wtyczki są dostępne w przypadku wdrażania w następujących usługach i środowiskach:

- [Azure App Service dla systemu Linux](/azure/app-service/containers/app-service-linux-intro). Aby rozpocząć pracę, zobacz [samouczek](java-deploy-webapp-tutorial.md).
- [Azure App Service](/azure/app-service/overview). Aby rozpocząć pracę, zobacz [instrukcje](deploy-Jenkins-app-service-plugin.md).