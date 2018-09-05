---
title: Wprowadzenie do usługi Azure Kubernetes Service
description: Usługa Azure Kubernetes Service ułatwia wdrażanie aplikacji opartych na kontenerach i zarządzanie nimi na platformie Azure.
services: container-service
author: iainfoulds
manager: jeconnoc
ms.service: container-service
ms.topic: overview
ms.date: 06/13/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: 161ea6698ecc46e50d1c70f922e2fb505e78278c
ms.sourcegitcommit: f6e2a03076679d53b550a24828141c4fb978dcf9
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/27/2018
ms.locfileid: "43087708"
---
# <a name="azure-kubernetes-service-aks"></a>Azure Kubernetes Service (AKS)

Usługa Azure Service Kubernetes (AKS) ułatwia wdrażanie zarządzanego klastra Kubernetes na platformie Azure. Usługa AKS zmniejsza złożoność i nakłady operacyjne związane z zarządzaniem rozwiązaniem Kubernetes, przenosząc znaczną część tej odpowiedzialności na platformę Azure. Jako hostowana usługa Kubernetes, platforma Azure obsługuje krytyczne zadania, takie jak monitorowanie kondycji i konserwacja. Ponadto usługa jest bezpłatna, płacisz tylko za węzły agentów w swoich klastrach, a nie za wzorce.

Ten dokument zawiera omówienie funkcji usługi Azure Kubernetes Service (AKS).

## <a name="flexible-deployment-options"></a>Elastyczne opcje wdrożenia

Usługa Azure Kubernetes Service oferuje opcje wdrożenia bazujące na portalu, wierszu polecenia i szablonach (szablony usługi Resource Manager i Terraform). Podczas wdrażania klastra usługi AKS wzorzec Kubernetes i wszystkie węzły są wdrażane i konfigurowane automatycznie. Dodatkowe funkcje, takie jak zaawansowana łączność sieciowa, integracja z usługą Azure Active Directory i monitorowanie, także można skonfigurować podczas procesu wdrażania.

Aby uzyskać więcej informacji, zobacz [Szybki start z usługą AKS][aks-portal] i [Szybki start z interfejsem wiersza polecenia usługi AKS][aks-cli].

## <a name="identity-and-security-management"></a>Zarządzanie tożsamościami i zabezpieczeniami

Klastry usługi AKS obsługują [kontrolę dostępu opartą na rolach (RBAC)][kubernetes-rbac]. Można też skonfigurować integrację klastra usługi AKS z usługą Azure Active Directory. W takiej konfiguracji dostęp do platformy Kubernetes można skonfigurować w oparciu o tożsamość w usłudze Azure Active Directory i członkostwo w grupie.

Aby uzyskać więcej informacji, zobacz [Integrowanie usługi Azure Active Directory z usługą AKS][aks-aad].

## <a name="integrated-logging-and-monitoring"></a>Zintegrowane rejestrowanie i monitorowanie

Kondycja kontenera informuje o wydajności. Jest określana przez gromadzenie metryk pamięci i procesora z kontenerów, węzłów oraz kontrolerów. Gromadzone są też dzienniki kontenerów. Te dane są przechowywane w obszarze roboczym usługi Log Analytics i są dostępne za pośrednictwem witryny Azure Portal, interfejsu wiersza polecenia platformy Azure lub punktu końcowego REST.

Aby uzyskać więcej informacji, zobacz [Monitorowanie kondycji kontenera usługi Azure Kubernetes Service][container-health].

## <a name="cluster-node-scaling"></a>Skalowanie węzłów klastra

W miarę zwiększania się zapotrzebowanie na zasoby węzły klastra usługi AKS można odpowiednio skalować na zewnątrz. Gdy zapotrzebowanie na zasoby spadnie, węzły można usunąć, skalując klaster do wewnątrz. Operacje skalowania usługi AKS można wykonywać przy użyciu witryny Azure Portal lub interfejsu wiersza polecenia platformy Azure.

Aby uzyskać więcej informacji, zobacz [Skalowanie klastra usługi Azure Kubernetes Service (AKS)][aks-scale].

## <a name="cluster-node-upgrades"></a>Uaktualnienia węzłów klastra

Usługa Azure Kubernetes Service oferuje wiele wersji platformy Kubernetes. Po udostępnieniu nowych wersji w usłudze AKS klaster można uaktualnić przy użyciu witryny Azure Portal lub interfejsu wiersza polecenia platformy Azure. Podczas procesu uaktualniania węzły są dokładnie odizolowywane i opróżniane, aby zminimalizować zakłócenia dla działających aplikacji.

Aby uzyskać więcej informacji, zobacz [Uaktualnianie klastra usługi Azure Kubernetes Service (AKS)][aks-upgrade].

## <a name="http-application-routing"></a>Routing aplikacji protokołu HTTP

Rozwiązanie Routing aplikacji protokołu HTTP ułatwia dostęp do aplikacji wdrożonych w klastrze usługi AKS. Po włączeniu konfiguruje ono kontroler ruchu przychodzącego w klastrze usługi AKS. Przy wdrażaniu aplikacji publicznie dostępne nazwy DNS są konfigurowane automatycznie.

Aby uzyskać więcej informacji, zobacz [Routing aplikacji protokołu HTTP][aks-http-routing].

## <a name="gpu-enabled-nodes"></a>Węzły obsługujące procesory GPU

Usługa AKS obsługuje tworzenie pul węzłów z obsługą procesorów GPU. Obecnie platforma Azure udostępnia maszyny wirtualne obsługujące jeden lub wielu procesorów GPU. Maszyny wirtualne obsługujące procesory GPU są zaprojektowane do przetwarzania obciążeń z dużą ilością obliczeń, dużą ilością grafiki i wizualizacji.

Aby uzyskać więcej informacji, zobacz [Korzystanie z procesorów GPU w usłudze AKS][aks-gpu].

## <a name="development-tooling-integration"></a>Integracja narzędzi deweloperskich

Platforma Kubernetes ma rozbudowany ekosystem narzędzi do programowania i zarządzania, takich jak Helm, Draft i rozszerzenie Kubernetes dla programu Visual Studio Code. Te narzędzia bezproblemowo współpracują z usługą Azure Kubernetes Service.

Dodatkowo usługa Azure Dev Spaces udostępnia środowisko do szybkiego, iteracyjnego programowania na platformie Kubernetes dla zespołów. Wykonując minimum czynności konfiguracyjnych, możesz uruchamiać i debugować kontenery bezpośrednio w usłudze Azure Kubernetes Service (AKS).

Aby uzyskać więcej informacji, zobacz [Azure Dev Spaces][azure-dev-spaces].

Projekt DevOps platformy Azure udostępnia proste rozwiązanie do przeniesienia istniejącego kodu i repozytorium Git na platformę Azure. Projekt DevOps automatycznie tworzy zasoby platformy Azure, takie jak usługa AKS i potok wydania w usłudze VSTS zawierający definicję kompilacji dla ciągłej integracji, konfiguruje definicję wydania dla ciągłego dostarczania, a następnie tworzy zasób usługi Azure Application Insights na potrzeby monitorowania.

Aby uzyskać więcej informacji, zobacz [Projekt DevOps platformy Azure][azure-devops].

## <a name="virtual-network-integration"></a>Integracja sieci wirtualnej

Klaster usługi AKS można wdrożyć w istniejącej sieci wirtualnej. W takiej konfiguracji każdy zasobnik w klastrze ma przypisany adres IP w sieci wirtualnej i może bezpośrednio komunikować się z innymi zasobnikami w klastrze oraz innymi węzłami w sieci wirtualnej. Zasobniki mogą też łączyć się z innymi usługami w równorzędnych sieciach wirtualnych i z sieciami lokalnymi za pośrednictwem usługi ExpressRoute i połączeń sieci VPN typu lokacja-lokacja (S2S).

Aby uzyskać więcej informacji, zobacz temat [Omówienie łączności sieciowej usługi AKS][aks-networking].

## <a name="private-container-registry"></a>Prywatny rejestr kontenerów

Integracja z usługą Azure Container Registry (ACR) pozwala uzyskać prywatny magazyn obrazów platformy Docker.

Aby uzyskać więcej informacji, zobacz [Azure Container Registry (ACR)][acr-docs].

## <a name="storage-volume-support"></a>Obsługa woluminów magazynu

Usługa Azure Kubernetes Service (AKS) obsługuje instalowanie woluminów magazynu dla danych trwałych. Klastry usługi AKS są tworzone z obsługą usług Azure Files i Azure Disks.

Aby uzyskać więcej informacji, zobacz [Azure Files][azure-files] i [Azure Disks][azure-disk].

## <a name="docker-image-support"></a>Obsługa obrazów platformy Docker

Usługa Azure Kubernetes Service (AKS) obsługuje format obrazów platformy Docker.

## <a name="kubernetes-certification"></a>Certyfikat platformy Kubernetes

Usługa Azure Kubernetes Service (AKS) ma certyfikat CNCF zgodności z platformą Kubernetes.

## <a name="regulatory-compliance"></a>Zgodność z przepisami

Usługa Azure Kubernetes Service (AKS) jest zgodna z regulacjami SOC i ISO.

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej na temat wdrażania usługi AKS i zarządzania nią z przewodnika Szybki start po usłudze AKS.

> [!div class="nextstepaction"]
> [Szybki start z usługą AKS][aks-cli]

<!-- LINKS - external -->
[acs-engine]: https://github.com/Azure/acs-engine
[draft]: https://github.com/Azure/draft
[helm]: https://helm.sh/
[kubectl-overview]: https://kubernetes.io/docs/user-guide/kubectl-overview/
[kubernetes-rbac]: https://kubernetes.io/docs/reference/access-authn-authz/rbac/

<!-- LINKS - internal -->
[acr-docs]: ../container-registry/container-registry-intro.md
[aks-aad]: ./aad-integration.md
[aks-cli]: ./kubernetes-walkthrough.md
[aks-gpu]: ./gpu-cluster.md
[aks-http-routing]: ./http-application-routing.md
[aks-networking]: ./networking-overview.md
[aks-portal]: ./kubernetes-walkthrough-portal.md
[aks-scale]: ./scale-cluster.md
[aks-upgrade]: ./upgrade-cluster.md
[azure-dev-spaces]: https://docs.microsoft.com/en-us/azure/dev-spaces/azure-dev-spaces
[azure-devops]: https://docs.microsoft.com/en-us/azure/devops-project/overview
[azure-disk]: ./azure-disks-dynamic-pv.md
[azure-files]: ./azure-files-dynamic-pv.md
[container-health]: ../monitoring/monitoring-container-health.md

