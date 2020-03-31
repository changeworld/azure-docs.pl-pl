---
title: Najważniejsze rozwiązania dotyczące operatora — izolacja klastra w usługach Azure Kubernetes (AKS)
description: Poznaj najważniejsze wskazówki dotyczące izolacji operatora klastra w usłudze Azure Kubernetes Service (AKS)
services: container-service
ms.topic: conceptual
ms.date: 11/26/2018
ms.openlocfilehash: 228b856d5c5ffa2bfac7df12094667e02f797690
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77594859"
---
# <a name="best-practices-for-cluster-isolation-in-azure-kubernetes-service-aks"></a>Najważniejsze wskazówki dotyczące izolacji klastra w usłudze Azure Kubernetes Service (AKS)

Podczas zarządzania klastrami w usłudze Azure Kubernetes Service (AKS) często należy izolować zespoły i obciążenia. Usługa AKS zapewnia elastyczność w sposobie uruchamiania klastrów wielodostępnych i izolowania zasobów. Aby zmaksymalizować inwestycję w kubernetes, te funkcje multi-najmu i izolacji powinny być rozumiane i implementowane.

Ten artykuł o najlepszych rozwiązaniach koncentruje się na izolacji dla operatorów klastrów. W tym artykule omówiono sposób wykonywania następujących zadań:

> [!div class="checklist"]
> * Planowanie klastrów wielodostępnych i rozdzielenie zasobów
> * Używanie izolacji logicznej lub fizycznej w klastrach usługi AKS

## <a name="design-clusters-for-multi-tenancy"></a>Projektowanie klastrów dla wielu dzierżaw

Platforma Kubernetes udostępnia funkcje, które umożliwiają logiczną izolację zespołów i obciążeń w tym samym klastrze. Liczba uprawnień powinna być jak najmniejsza, a same uprawnienia — ograniczone do zasobów używanych w danym zespole. [Obszar nazw][k8s-namespaces] w umiękań tworzy granicę izolacji logicznej. Dodatkowe funkcje i zagadnienia dotyczące izolacji i wielonajemalności obejmują następujące obszary:

* **Planowanie** obejmuje korzystanie z podstawowych funkcji, takich jak przydziały zasobów i budżety zakłóceń zasobników. Aby uzyskać więcej informacji na temat tych funkcji, zobacz [Najważniejsze wskazówki dotyczące podstawowych funkcji harmonogramu w ustroju AKS][aks-best-practices-scheduler].
  * Bardziej zaawansowane funkcje harmonogramu obejmują skazy i tolerancje, selektory węzłów oraz koligacji węzła i zasobnika lub antyko koligacji. Aby uzyskać więcej informacji na temat tych funkcji, zobacz [Najważniejsze wskazówki dotyczące zaawansowanych funkcji harmonogramu w ustroju AKS][aks-best-practices-advanced-scheduler].
* **Sieć** obejmuje korzystanie z zasad sieci do kontrolowania przepływu ruchu do i z zasobników.
* **Uwierzytelnianie i autoryzacja** obejmują użytkownika kontroli dostępu opartego na rolach (RBAC) i integracji usługi Azure Active Directory (AD), tożsamości zasobników i wpisów tajnych w usłudze Azure Key Vault. Aby uzyskać więcej informacji na temat tych funkcji, zobacz [Najważniejsze wskazówki dotyczące uwierzytelniania i autoryzacji w uzywce AKS][aks-best-practices-identity].
* **Kontenery** obejmują zasady zabezpieczeń zasobników, konteksty zabezpieczeń zasobników, skanowanie obrazów i środowiska wykonawcze w poszukiwaniu luk w zabezpieczeniach. Obejmuje również przy użyciu app armor lub Seccomp (Secure Computing) do ograniczenia dostępu kontenera do węzła źródłowego.

## <a name="logically-isolate-clusters"></a>Logicznie izolować klastry

**Wskazówki dotyczące najlepszych praktyk** — użyj izolacji logicznej do oddzielenia zespołów i projektów. Spróbuj zminimalizować liczbę fizycznych klastrów AKS, które można wdrożyć w celu izolowania zespołów lub aplikacji.

Z izolacją logiczną pojedynczy klaster AKS może być używany dla wielu obciążeń, zespołów lub środowisk. [Obszary nazw][k8s-namespaces] usługi Kubernetes tworzą logiczną granicę izolacji dla obciążeń i zasobów.

![Logiczna izolacja klastra Kubernetes w udziale usługi AKS](media/operator-best-practices-cluster-isolation/logical-isolation.png)

Logiczne oddzielenie klastrów zwykle zapewnia większą gęstość zasobników niż klastry izolowane fizycznie. W klastrze jest mniejsza nadmiar mocy obliczeniowej, która jest bezczynna. W połączeniu z skalowaniem automatycznym klastra Kubernetes można skalować liczbę węzłów w górę lub w dół, aby sprostać wymaganiom. To najlepsze rozwiązanie podejście do skalowania automatycznego umożliwia uruchamianie tylko liczby węzłów wymaganych i minimalizuje koszty.

Środowiska Kubernetes, w uzywu AKS lub gdzie indziej, nie są całkowicie bezpieczne dla wrogiego użycia wielu dzierżawców. W środowisku wielodostępnym wielu dzierżawców pracuje nad wspólną infrastrukturą współużytkową. W rezultacie, jeśli nie można ufać wszystkim dzierżawcom, należy wykonać dodatkowe planowanie, aby uniknąć wpływu jednej dzierżawy na zabezpieczenia i obsługę innej. Dodatkowe funkcje zabezpieczeń, takie jak *Zasady zabezpieczeń zasobnika* i bardziej szczegółowe kontroli dostępu oparte na rolach (RBAC) dla węzłów utrudniają exploity. Jednak dla prawdziwego zabezpieczenia podczas uruchamiania wrogich obciążeń wielodostępnych hypervisor jest jedynym poziomem zabezpieczeń, który należy zaufać. Domena zabezpieczeń dla usługi Kubernetes staje się całym klastrem, a nie pojedynczym węzłem. Dla tych typów wrogich obciążeń wielodostępnych należy użyć fizycznie izolowane klastry.

## <a name="physically-isolate-clusters"></a>Fizyczne izolowanie klastrów

**Wskazówki dotyczące najlepszych praktyk** — minimalizowanie użycia izolacji fizycznej dla każdego wdrożenia oddzielnego zespołu lub aplikacji. Zamiast tego należy użyć *izolacji logicznej,* jak omówiono w poprzedniej sekcji.

Typowym podejściem do izolacji klastra jest użycie fizycznie oddzielnych klastrów AKS. W tym modelu izolacji zespoły lub obciążenia są przypisywane własnemu klastrowi AKS. Takie podejście często wygląda na najprostszy sposób izolowania obciążeń lub zespołów, ale dodaje dodatkowe zarządzanie i obciążenie finansowe. Teraz musisz zachować te wiele klastrów i indywidualnie zapewnić dostęp i przypisać uprawnienia. Są również rozliczane dla wszystkich poszczególnych węzłów.

![Fizyczna izolacja poszczególnych klastrów Kubernetes w AKS](media/operator-best-practices-cluster-isolation/physical-isolation.png)

Fizycznie oddzielne klastry zwykle mają niską gęstość zasobników. Ponieważ każdy zespół lub obciążenie ma swój własny klaster AKS, klaster jest często nadmiernie aprowizacji z zasobów obliczeniowych. Często niewielka liczba zasobników są planowane w tych węzłach. Nieużywane pojemności w węzłach nie może służyć do aplikacji lub usług w rozwoju przez inne zespoły. Te nadwyżki zasobów przyczyniają się do dodatkowych kosztów w fizycznie oddzielnych klastrach.

## <a name="next-steps"></a>Następne kroki

W tym artykule skupiono się na izolacji klastra. Aby uzyskać więcej informacji na temat operacji klastra w uzywce AKS, zobacz następujące najlepsze rozwiązania:

* [Podstawowe funkcje harmonogramu kubernetes][aks-best-practices-scheduler]
* [Zaawansowane funkcje harmonogramu Kubernetes][aks-best-practices-advanced-scheduler]
* [Uwierzytelnianie i autoryzacja][aks-best-practices-identity]

<!-- EXTERNAL LINKS -->

<!-- INTERNAL LINKS -->
[k8s-namespaces]: concepts-clusters-workloads.md#namespaces
[aks-best-practices-scheduler]: operator-best-practices-scheduler.md
[aks-best-practices-advanced-scheduler]: operator-best-practices-advanced-scheduler.md
[aks-best-practices-identity]: operator-best-practices-identity.md
