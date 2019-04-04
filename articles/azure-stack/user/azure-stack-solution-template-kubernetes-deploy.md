---
title: Wdrażanie usługi Kubernetes na korzystanie z kontenerów usługi Azure Stack | Dokumentacja firmy Microsoft
description: Dowiedz się, jak wdrożyć rozwiązanie Kubernetes i używać kontenerów za pomocą usługi Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/27/2019
ms.author: mabrigg
ms.reviewer: waltero
ms.lastreviewed: 01/16/2019
ms.openlocfilehash: 77275ec274a9c76918874007cfe564eea09e6de5
ms.sourcegitcommit: a60a55278f645f5d6cda95bcf9895441ade04629
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2019
ms.locfileid: "58877197"
---
# <a name="deploy-kubernetes-to-use-containers-with-azure-stack"></a>Wdrażanie rozwiązania Kubernetes, aby używać kontenerów za pomocą usługi Azure Stack

*Dotyczy Zintegrowane usługi Azure Stack, systemy i usługi Azure Stack Development Kit*

> [!Note]  
> Rozwiązanie Kubernetes w usłudze Azure Stack jest w wersji zapoznawczej. Odłączony scenariusza użycia usługi Azure Stack nie jest obecnie obsługiwane przez wersję zapoznawczą.

Możesz wykonać kroki opisane w tym artykule, aby wdrożyć i skonfigurować zasoby dla rozwiązania Kubernetes w jednej, skoordynowanej operacji. W krokach używany szablon rozwiązania usługi Azure Resource Manager. Można będzie konieczne do zbierania wymaganych informacji o instalacji programu Azure Stack Generowanie szablonu, a następnie wdrożyć z Twoją chmurą. Szablon usługi Azure Stack nie używa tego samego zarządzanych usług AKS oferowanych na platformie Azure globalnego.

## <a name="kubernetes-and-containers"></a>Kubernetes i kontenery

Można zainstalować usługi Kubernetes przy użyciu szablonów usługi Azure Resource Manager, generowane przez aparat usługi ACS w usłudze Azure Stack. [Kubernetes](https://kubernetes.io) to system typu open source do automatyzowania wdrażania, skalowania i zarządzania nimi aplikacji w kontenerach. A [kontenera](https://www.docker.com/what-container) obraz. Obraz kontenera jest podobne do maszyny Wirtualnej, jednak w przeciwieństwie do maszyny Wirtualnej, kontener zawiera tylko zasoby potrzebne do uruchomienia aplikacji, takich jak kod, środowisko uruchomieniowe do wykonania kodu, określonych bibliotek i ustawień.

Rozwiązanie Kubernetes można użyć:

- Twórz wysoce skalowalne, można uaktualnić, aplikacje, które można wdrożyć w kilka sekund. 
- Uproszczenie projektowania aplikacji i zwiększyć jej niezawodność przez różne aplikacje narzędzia Helm. [Polecenie Helm](https://github.com/kubernetes/helm) to narzędzie open source pakietu, które pomaga zainstalować i zarządzanie cyklem życia aplikacji platformy Kubernetes.
- Łatwe monitorowanie i diagnozowanie kondycji aplikacji.

Użytkownik zostanie naliczona tylko za wykorzystanie mocy obliczeniowej, wymagane przez węzły obsługi klastra. Aby uzyskać więcej informacji, zobacz [użycie i rozliczenia w usłudze Azure Stack](https://docs.microsoft.com/azure/azure-stack/azure-stack-billing-and-chargeback).

## <a name="deploy-kubernetes-to-use-containers"></a>Wdrażanie usługi Kubernetes na korzystanie z kontenerów

Kroki wdrażania klastra Kubernetes w usłudze Azure Stack będzie zależeć od usługi zarządzania tożsamościami. Sprawdź, Zarządzanie tożsamościami, używane przez instalację usługi Azure Stack. Skontaktuj się z administratorem usługi Azure Stack, aby sprawdzić swoje Usługa zarządzania tożsamościami.

- **Azure Active Directory (Azure AD)**  
Aby uzyskać instrukcje na temat instalowania klastra przy użyciu usługi Azure AD, zobacz [wdrażanie platformy Kubernetes w usłudze Azure Stack przy użyciu usługi Azure Active Directory (Azure AD)](azure-stack-solution-template-kubernetes-azuread.md).

- **Active Directory Federated Services (AD FS)**  
Aby uzyskać instrukcje na temat instalowania klastra, korzystając z usług AD FS, zobacz [wdrażanie Kubernetes do usługi Azure Stack, użycie usług Active Directory Sfederowana (AD FS)](azure-stack-solution-template-kubernetes-adfs.md).

## <a name="connect-to-your-cluster"></a>Połącz z klastrem

Teraz możesz nawiązać połączenie z klastrem. Wzorzec znajdują się w grupie zasobów klastra i nosi nazwę `k8s-master-<sequence-of-numbers>`. Użyj klienta SSH, aby nawiązać połączenie z wzorcem. We wzorcu, można użyć **kubectl**, klienta wiersza polecenia Kubernetes do zarządzania klastrem. Aby uzyskać instrukcje, zobacz [Kubernetes.io](https://kubernetes.io/docs/reference/kubectl/overview).

Można również znaleźć **Helm** Menedżera pakietów jest przydatne w przypadku instalowania i wdrażania aplikacji do klastra. Aby uzyskać instrukcje dotyczące instalowania i używania narzędzia Helm z klastrem, zobacz [helm.sh](https://helm.sh/).

## <a name="next-steps"></a>Kolejne kroki

[Włącz pulpit nawigacyjny platformy Kubernetes](azure-stack-solution-template-kubernetes-dashboard.md)

[Dodaj rozwiązania Kubernetes w portalu Marketplace (dla operatora infrastruktury Azure Stack)](../azure-stack-solution-template-kubernetes-cluster-add.md)

[Wdrażanie rozwiązania Kubernetes do usługi Azure Stack przy użyciu usługi Azure Active Directory (Azure AD)](azure-stack-solution-template-kubernetes-azuread.md)

[Wdrażanie usługi Kubernetes na użycie usług Active Directory Sfederowana (AD FS) w usłudze Azure Stack](azure-stack-solution-template-kubernetes-adfs.md)

[Usługa Kubernetes na platformie Azure](https://docs.microsoft.com/azure/container-service/kubernetes/container-service-kubernetes-walkthrough)
