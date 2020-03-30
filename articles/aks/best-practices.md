---
title: Najważniejsze wskazówki dotyczące usługi Azure Kubernetes (AKS)
description: Zbieranie najlepszych rozwiązań operator klastra i deweloperów do tworzenia aplikacji i zarządzania nimi w usłudze Azure Kubernetes (AKS)
services: container-service
ms.topic: article
ms.date: 12/07/2018
ms.openlocfilehash: 627eeffd18a479486e5a682da06bf89cd5f8f0e1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77596321"
---
# <a name="cluster-operator-and-developer-best-practices-to-build-and-manage-applications-on-azure-kubernetes-service-aks"></a>Operator klastra i narzędzia dla deweloperów dotyczące tworzenia aplikacji i zarządzania nimi w usłudze Azure Kubernetes (AKS)

Aby pomyślnie utworzyć i uruchomić aplikacje w usłudze Azure Kubernetes Service (AKS), istnieją pewne kluczowe zagadnienia do zrozumienia i zaimplementowania. Obszary te obejmują funkcje wielu dzierżaw i harmonogramów, zabezpieczenia klastra i zasobników lub ciągłość działania i odzyskiwanie po awarii. Poniższe najlepsze rozwiązania są pogrupowane, aby pomóc operatorom klastrów i deweloperom zrozumieć zagadnienia dotyczące każdego z tych obszarów i zaimplementować odpowiednie funkcje.

Te najlepsze rozwiązania i artykuły koncepcyjne zostały napisane we współpracy z grupą produktów AKS, zespołami inżynierskimi i zespołami terenowymi, w tym globalnymi czarnymi pasami (GBBs).

## <a name="cluster-operator-best-practices"></a>Najważniejsze wskazówki dotyczące operatora klastra

Jako operator klastra współpracować z właścicielami aplikacji i deweloperami, aby zrozumieć ich potrzeby. Następnie można użyć następujących najlepszych rozwiązań, aby skonfigurować klastry AKS w razie potrzeby.

**Wielodostęp**

* [Najlepsze rozwiązania związane z izolacją klastra](operator-best-practices-cluster-isolation.md)
    * Zawiera podstawowe komponenty wielonajemalności i izolację logiczną z obszarami nazw.
* [Najlepsze rozwiązania dotyczące podstawowych funkcji usługi Scheduler](operator-best-practices-scheduler.md)
    * Obejmuje korzystanie z przydziałów zasobów i budżetów zakłóceń zasobników.
* [Najlepsze rozwiązania dotyczące zaawansowanych funkcji usługi Scheduler](operator-best-practices-advanced-scheduler.md)
    * Obejmuje używanie skazy i tolerancji, selektorów węzłów i koligacji oraz koligacji i koligacji.
* [Najlepsze rozwiązania dotyczące uwierzytelniania i autoryzacji](operator-best-practices-identity.md)
    * Obejmuje integrację z usługą Azure Active Directory przy użyciu kontroli dostępu opartych na rolach (RBAC) i tożsamości zasobników.

**Zabezpieczenia**

* [Najlepsze rozwiązania dotyczące zabezpieczeń i uaktualnień klastra](operator-best-practices-cluster-security.md)
    * Obejmuje zabezpieczanie dostępu do serwera interfejsu API, ograniczanie dostępu do kontenerów oraz zarządzanie uaktualnieniami i ponownym uruchomieniem węzła.
* [Najważniejsze wskazówki dotyczące zarządzania obrazami kontenerów i zabezpieczeń](operator-best-practices-container-image-management.md)
    * Obejmuje zabezpieczanie obrazu i środowiska wykonawczego oraz automatyczne kompilacje na podstawie aktualizacji obrazu podstawowego.
* [Najważniejsze wskazówki dotyczące zabezpieczeń zasobników](developer-best-practices-pod-security.md)
    * Obejmuje zabezpieczenie dostępu do zasobów, ograniczenie narażenia poświadczeń oraz używanie tożsamości zasobników i cyfrowych magazynów kluczy.

**Sieć i pamięć masowa**

* [Najważniejsze wskazówki dotyczące łączności sieciowej](operator-best-practices-network.md)
    * Obejmuje różne modele sieci, przy użyciu przychodzących i zapory aplikacji sieci web (WAF) i zabezpieczania dostępu SSH węzła.
* [Najlepsze rozwiązania dotyczące przechowywania i tworzenia kopii zapasowych](operator-best-practices-storage.md)
    * Obejmuje wybór odpowiedniego typu magazynu i rozmiaru węzła, dynamiczne inicjowanie obsługi administracyjnej woluminów i kopie zapasowe danych.

**Uruchamianie obciążeń gotowych do pracy w przedsiębiorstwie**

* [Najlepsze praktyki dotyczące ciągłości działania i odzyskiwania po awarii](operator-best-practices-multi-region.md)
    * Obejmuje korzystanie z par regionów, wielu klastrów z usługą Azure Traffic Manager i replikacji geograficznej obrazów kontenerów.

## <a name="developer-best-practices"></a>Najważniejsze wskazówki dotyczące programistów

Jako deweloper lub właściciel aplikacji możesz uprościć środowisko programistyczne i zdefiniować wymagać wymagań wydajności aplikacji.

* [Najlepsze rozwiązania dotyczące zarządzania zasobami dla deweloperów aplikacji](developer-best-practices-resource-management.md)
    * Obejmuje definiowanie żądań zasobu zasobu zasobu zasobu zasobu zasobu zasobu zasobu, konfigurowanie narzędzi programistycznych i sprawdzanie problemów z aplikacją.
* [Najważniejsze wskazówki dotyczące zabezpieczeń zasobników](developer-best-practices-pod-security.md)
    * Obejmuje zabezpieczenie dostępu do zasobów, ograniczenie narażenia poświadczeń oraz używanie tożsamości zasobników i cyfrowych magazynów kluczy.

## <a name="kubernetes--aks-concepts"></a>Koncepcje Kubernetes / AKS

Aby ułatwić zrozumienie niektórych funkcji i składników tych najlepszych rozwiązań, można również zobaczyć następujące artykuły koncepcyjne dotyczące klastrów w usłudze Azure Kubernetes Service (AKS):

* [Podstawowe pojęcia kubernetes](concepts-clusters-workloads.md)
* [Dostęp i tożsamość](concepts-identity.md)
* [Pojęcia dotyczące zabezpieczeń](concepts-security.md)
* [Koncepcje sieci](concepts-network.md)
* [Opcje magazynu](concepts-storage.md)
* [Opcje skalowania](concepts-scale.md)

## <a name="next-steps"></a>Następne kroki

Jeśli chcesz rozpocząć pracę z usługą AKS, postępuj zgodnie z jednym z przewodników Szybki start, aby wdrożyć klaster usługi Azure Kubernetes (AKS) przy użyciu interfejsu [wiersza polecenia platformy Azure](kubernetes-walkthrough.md) lub [witryny Azure portal.](kubernetes-walkthrough-portal.md)
