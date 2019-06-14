---
title: Operator najlepsze rozwiązania — izolacja klastra usługi Kubernetes w usłudze Azure (AKS)
description: Poznaj klastra operator najlepsze rozwiązania dotyczące izolacji w usłudze Azure Kubernetes Service (AKS)
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: conceptual
ms.date: 11/26/2018
ms.author: iainfou
ms.openlocfilehash: 94aaa72497a8a5f171d6b42f59a3c5b507c71492
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60465310"
---
# <a name="best-practices-for-cluster-isolation-in-azure-kubernetes-service-aks"></a>Najlepsze rozwiązania dotyczące izolacji klastra w usłudze Azure Kubernetes Service (AKS)

W przypadku zarządzania klastrów w usłudze Azure Kubernetes Service (AKS), często jest konieczne do izolowania obciążeń i zespołów. AKS zapewnia większą elastyczność w sposób uruchamiaj klastry z wieloma dzierżawami i izolowanie zasobów. Aby zmaksymalizować korzyści z inwestycji w rozwiązania Kubernetes, tych funkcji obsługi wielu dzierżawców i izolacji należy rozumieć i zaimplementowane.

Najlepsze rozwiązania dotyczące tej koncentruje się na izolację operatorów klastra. W tym artykule omówiono sposób wykonywania następujących zadań:

> [!div class="checklist"]
> * Planowanie klastrów z wieloma dzierżawami i oddzielenie zasobów
> * Użyj logiczną lub fizyczną izolację w klastrach usługi AKS

## <a name="design-clusters-for-multi-tenancy"></a>Klastry projektu dla wielu dzierżawców

Kubernetes zapewnia funkcje, które pozwalają logicznie izolowania zespołów i obciążeń w tym samym klastrze. Celem powinno być zapewnienie najmniejszej liczby uprawnień, zakres zasobów, każdy zespół potrzebuje. A [Namespace] [ k8s-namespaces] w usłudze Kubernetes tworzy granicę izolacji logicznej. Funkcje dodatkowe usługi kubernetes i zagadnienia dotyczące izolacji i wielodostępność obejmują następujące obszary:

* **Planowanie** obejmuje korzystanie z podstawowych funkcji takich jak limity przydziałów zasobów i budżetu przerw w działaniu zasobników. Aby uzyskać więcej informacji o tych funkcjach, zobacz [najlepsze rozwiązania dotyczące harmonogramu podstawowe funkcje w usłudze AKS][aks-best-practices-scheduler].
  * Bardziej zaawansowane funkcje usługi scheduler obejmują, nasłonecznieniem i tolerations, selektory węzła i koligacji węzła i zasobnika lub konfiguracji zapobiegającej koligacji. Aby uzyskać więcej informacji o tych funkcjach, zobacz [najlepsze rozwiązania dotyczące harmonogramu zaawansowanych funkcji w usłudze AKS][aks-best-practices-advanced-scheduler].
* **Sieć** obejmuje korzystanie z zasad sieciowych, aby kontrolować przepływ ruchu i z zasobników.
* **Uwierzytelnianie i autoryzacja** uwzględnić użytkownika kontroli dostępu opartej na rolach (RBAC) oraz integracji usługi Azure Active Directory (AD), pod tożsamości i kluczy tajnych w usłudze Azure Key Vault. Aby uzyskać więcej informacji o tych funkcjach, zobacz [najlepsze rozwiązania dotyczące uwierzytelniania i autoryzacji w usłudze AKS][aks-best-practices-identity].
* **Kontenery** obejmują zasady zabezpieczeń pod, konteksty zabezpieczeń pod, skanowanie obrazów i środowisk wykonawczych dla luki w zabezpieczeniach. Również polega na użyciu Moto aplikację lub funkcję Seccomp (bezpiecznego przetwarzania danych) do ograniczania dostępu do kontenera do węzła podstawowego.

## <a name="logically-isolate-clusters"></a>Logicznie izolowanie klastrów

**Najważniejsze wskazówki** — stosowana jest izolacja logiczna do oddzielenia zespołów i projektów. Należy zminimalizować liczbę fizycznych klastrów AKS wdrażanie do izolowania aplikacji lub zespołów.

Za pomocą izolacji logicznej pojedynczy klaster AKS może służyć do wielu obciążeń, zespoły lub środowisk. Kubernetes [przestrzenie nazw] [ k8s-namespaces] stanowią granicę izolacji logicznej, w przypadku obciążeń i zasobów.

![Izolacji logicznej klastra Kubernetes w usłudze AKS](media/operator-best-practices-cluster-isolation/logical-isolation.png)

Separacji logicznej klastrów zwykle zapewnia zwiększenie gęstości zasobnika niż fizycznie izolowane klastrów. Mniej nadmiarowe moc obliczeniową, która jest bezczynny w klastrze. W połączeniu z skalowanie klastra usługi Kubernetes, możesz skalować liczbę węzłów w górę lub w dół, aby spełniać wymagania. Tego najlepszego podejścia praktycznego do automatycznego skalowania pozwala na uruchamianie tylko liczbę węzłów, wymagane i minimalizuje koszty.

Środowisk Kubernetes w usłudze AKS, lub w innych miejscach, nie są całkowicie bezpieczne dla szkodliwy użycie wielu obcych dzierżaw. Funkcje dodatkowe zabezpieczenia, takie jak *zasady zabezpieczeń zasobnika* i więcej kontroli dostępu w zakresie opartej na rolach (RBAC) dla węzłów utrudniają luki w zabezpieczeniach. Wartość true, zabezpieczeń przy uruchamianiu obciążeń liczonych w szkodliwy wielodostępne, funkcja hypervisor to tylko poziom zabezpieczeń, które należy ufać. Domeny zabezpieczeń dla rozwiązania Kubernetes staje się całego klastra, a nie oddzielnego węzła. Dla tych typów szkodliwy obciążenia z wieloma dzierżawami należy użyć fizycznie izolowane klastrów.

## <a name="physically-isolate-clusters"></a>Fizycznie izolowanie klastrów

**Najważniejsze wskazówki** — należy zminimalizować użycie fizycznego izolacji dla każdego oddzielnego zespołu lub wdrażania aplikacji. Zamiast tego należy użyć *logiczne* oddzielnie, zgodnie z opisem w poprzedniej sekcji.

Typowym rozwiązaniem w przypadku izolacji klastra ma używać fizycznie oddzielnymi klastrami usługi AKS. W tym modelu izolacji obciążeń lub zespołów są przypisywane ich własnych klastra AKS. To podejście często wygląda Najprostszym sposobem do izolowania obciążeń lub zespołów, ale dodaje koszty finansowe i dodatkowego zarządzania. Możesz teraz muszą obsługiwać te wielu klastrów i indywidualnie zapewniają dostęp i przypisać uprawnienia. Możesz również są naliczane dla poszczególnych węzłów.

![Fizyczne odizolowanie Kubernetes poszczególnych klastrów w usłudze AKS](media/operator-best-practices-cluster-isolation/physical-isolation.png)

Fizycznie oddzielnymi klastrami zazwyczaj ma pod niskiej gęstości. Każdy zespół lub Obciążenie ma swoje własne klastra AKS, klaster często jest nadmiernie aprowizowane przy użyciu zasobów obliczeniowych. Często niewielka liczba zasobników jest zaplanowane w ramach tych węzłów. Nieużywane miejsce na węzłach nie można używać dla aplikacji lub usług w rozwoju przez inne zespoły. Te zasoby nadmiarowe przyczyniają się do dodatkowych kosztów w fizycznie oddzielnymi klastrami.

## <a name="next-steps"></a>Kolejne kroki

Ten artykuł koncentruje się na izolację klastra. Aby uzyskać więcej informacji na temat operacji klastra w usłudze AKS zobacz poniższe najlepsze rozwiązania:

* [Podstawowe funkcje usługi scheduler rozwiązania Kubernetes][aks-best-practices-scheduler]
* [Zaawansowane funkcje usługi scheduler rozwiązania Kubernetes][aks-best-practices-advanced-scheduler]
* [Uwierzytelnianie i autoryzacja][aks-best-practices-identity]

<!-- EXTERNAL LINKS -->

<!-- INTERNAL LINKS -->
[k8s-namespaces]: concepts-clusters-workloads.md#namespaces
[aks-best-practices-scheduler]: operator-best-practices-scheduler.md
[aks-best-practices-advanced-scheduler]: operator-best-practices-advanced-scheduler.md
[aks-best-practices-identity]: operator-best-practices-identity.md
