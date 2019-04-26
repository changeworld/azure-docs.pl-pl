---
title: Najlepsze rozwiązania dla usługi Azure Kubernetes Service (AKS)
description: Zbiór klastra operatora i deweloperów najlepszych rozwiązań do tworzenia aplikacji i zarządzanie nimi w usłudze Azure Kubernetes Service (AKS)
services: container-service
author: rockboyfor
ms.service: container-service
ms.topic: article
origin.date: 12/07/2018
ms.date: 03/04/2019
ms.author: v-yeche
ms.openlocfilehash: c73f2fea808944847fab6a675914ee57e537560b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60467157"
---
# <a name="cluster-operator-and-developer-best-practices-to-build-and-manage-applications-on-azure-kubernetes-service-aks"></a>Operator klastra i dla deweloperów najważniejsze wskazówki dotyczące tworzenia aplikacji i zarządzanie nimi w usłudze Azure Kubernetes Service (AKS)

Aby skompilować i uruchomić aplikacje pomyślnie w usłudze Azure Kubernetes Service (AKS), istnieją niektóre najważniejsze kwestie do zrozumienia i zaimplementowania. Te obszary zawierają wielodostępu i funkcje harmonogramu, klastra oraz zabezpieczeń pod lub ciągłość prowadzenia działalności biznesowej i odzyskiwania po awarii. Poniższe najlepsze rozwiązania są grupowane w celu operatorów klastra i deweloperów zrozumienie zagadnień dotyczących każdego z tych obszarów i zaimplementować odpowiednie funkcje.

Te najlepsze rozwiązania i artykuły koncepcyjne został napisany w połączeniu z grupy produktów usługi AKS, inżynierii, zespoły i zespoły pola tym globalnego pasy czarny (GBBs).

## <a name="cluster-operator-best-practices"></a>Najważniejsze wskazówki dotyczące klastra — operator

Jako operatora klastra współdziałały właścicieli aplikacji i deweloperów, aby zrozumieć ich potrzeb. Poniższe najlepsze rozwiązania można następnie użyć skonfigurować klastry usługi AKS, zgodnie z potrzebami.

**Wielodostęp**

* [Najlepsze rozwiązania dotyczące izolacji klastra](operator-best-practices-cluster-isolation.md)
    * Zawiera składniki podstawowe wielodostępu i izolacji logicznej z przestrzeniami nazw.
* [Najlepsze rozwiązania dotyczące harmonogramu podstawowe funkcje](operator-best-practices-scheduler.md)
    * Obejmuje przy użyciu limity przydziałów zasobów i zasobnika przerw w działaniu budżetu.
* [Najlepsze rozwiązania dotyczące harmonogramu zaawansowane funkcje](operator-best-practices-advanced-scheduler.md)
    * Obejmuje przy użyciu nasłonecznieniem i tolerations, selektory węzła i koligacji i koligację między zasobnika i konfiguracji zapobiegającej koligacji.
* [Najlepsze rozwiązania dotyczące uwierzytelniania i autoryzacji](operator-best-practices-identity.md)
    * Obejmuje integrację z usługą Azure Active Directory przy użyciu kontroli dostępu opartej na rolach (RBAC) i tożsamości zasobników.

**Bezpieczeństwo**

* [Najlepsze rozwiązania dotyczące zabezpieczeń klastra i uaktualnienia](operator-best-practices-cluster-security.md)
    * Obejmuje zabezpieczanie dostępu do serwera interfejsu API, ograniczanie dostępu do kontenera oraz zarządzania nimi, uaktualnienia i ponownym uruchomieniu węzła.
* [Najlepsze rozwiązania dotyczące zarządzania obrazami kontenera i zabezpieczeń](operator-best-practices-container-image-management.md)
    * Obejmuje zabezpieczanie obrazu i środowisk wykonawczych, przy użyciu rejestrów zaufanych i kompilacje automatyczne aktualizacje obrazu podstawowego...
* [Najlepsze rozwiązania dotyczące zabezpieczeń zasobników](developer-best-practices-pod-security.md)
    * Obejmuje zabezpieczanie dostępu do zasobów, ograniczanie widoczności poświadczeń i przy użyciu tożsamości zasobników i cyfrowe magazynów kluczy.

**Sieci i magazynu**

* [Najlepsze rozwiązania dotyczące łączności sieciowej](operator-best-practices-network.md)
    * Obejmuje modele innej sieci, przy użyciu zapory aplikacji sieci web i transferu danych przychodzących (WAF) i zabezpieczania dostępu SSH z węzłem.
* [Najlepsze rozwiązania dotyczące magazynu i kopii zapasowych](operator-best-practices-storage.md)
    * Obejmuje, wybierając odpowiedni typ i języka node rozmiar magazynu, dynamiczne aprowizowanie woluminy i kopie zapasowe danych.

**Uruchamianiu obciążeń liczonych w przedsiębiorstwach**

* [Najlepsze rozwiązania dotyczące firm ciągłości działania i odzyskiwania po awarii](operator-best-practices-multi-region.md)
    * Obejmuje przy użyciu par regionów, wiele klastrów za pomocą usługi Azure Traffic Manager i replikacja geograficzna obrazów kontenerów.

## <a name="developer-best-practices"></a>Najlepsze rozwiązania dla deweloperów

Jako deweloper lub właściciela aplikacji, można uproszczenie środowiska programowania i zdefiniować wymagają potrzeb związanych z wydajnością aplikacji.

* [Najlepsze rozwiązania dla deweloperów aplikacji do zarządzania zasobami](developer-best-practices-resource-management.md)
    * Obejmuje Definiowanie zasobników żądania zasobów i limity, Konfigurowanie narzędzi do tworzenia i sprawdzania pod kątem problemów z aplikacjami.
* [Najlepsze rozwiązania dotyczące zabezpieczeń zasobników](developer-best-practices-pod-security.md)
    * Obejmuje zabezpieczanie dostępu do zasobów, ograniczanie widoczności poświadczeń i przy użyciu tożsamości zasobników i cyfrowe magazynów kluczy.

## <a name="kubernetes--aks-concepts"></a>Kubernetes / pojęcia usługi AKS

Aby ułatwić zrozumienie niektórych funkcji i składników tych najlepszych rozwiązań, widać również następujące artykuły koncepcyjne dotyczące klastrów w usłudze Azure Kubernetes Service (AKS):

* [Kubernetes podstawowe pojęcia](concepts-clusters-workloads.md)
* [Dostęp i tożsamość](concepts-identity.md)
* [Pojęcia dotyczące zabezpieczeń](concepts-security.md)
* [Pojęcia dotyczące sieci](concepts-network.md)
* [Opcje magazynu](concepts-storage.md)
* [Opcje skalowania](concepts-scale.md)

## <a name="next-steps"></a>Kolejne kroki

Jeśli potrzebujesz zacząć korzystać z usługi AKS, wykonaj jedną z przewodników Szybki Start do wdrożenia klastra Azure Kubernetes Service (AKS) przy użyciu [wiersza polecenia platformy Azure](kubernetes-walkthrough.md) lub [witryny Azure portal](kubernetes-walkthrough-portal.md).