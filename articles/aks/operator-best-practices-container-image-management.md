---
title: Najważniejsze rozwiązania dotyczące operatora — zarządzanie obrazami kontenerów w usługach Azure Kubernetes (AKS)
description: Zapoznaj się z najlepszymi rozwiązaniami operatorów klastra dotyczącymi zarządzania obrazami kontenerów i zabezpieczania ich w usłudze Azure Kubernetes (AKS)
services: container-service
ms.topic: conceptual
ms.date: 12/06/2018
ms.openlocfilehash: efe72157f598c336248e407c57bce92fe87da23a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77594750"
---
# <a name="best-practices-for-container-image-management-and-security-in-azure-kubernetes-service-aks"></a>Najważniejsze wskazówki dotyczące zarządzania obrazami kontenerów i zabezpieczeń w usłudze Azure Kubernetes (AKS)

Podczas tworzenia i uruchamiania aplikacji w usłudze Azure Kubernetes Service (AKS) bezpieczeństwo kontenerów i obrazów kontenerów jest kluczowym czynnikiem. Kontenery, które zawierają nieaktualne obrazy bazowe lub niezałatane środowiska wykonawcze aplikacji, wprowadzają zagrożenie bezpieczeństwa i możliwy wektor ataku. Aby zminimalizować te zagrożenia, należy zintegrować narzędzia, które skanują i korygują problemy w kontenerach w czasie kompilacji, a także w czasie wykonywania. Im wcześniej w procesie zostanie przechwycona luka lub nieaktualny obraz podstawowy, tym bardziej bezpieczny jest klaster. W tym artykule *kontenery* oznacza zarówno obrazy kontenerów przechowywane w rejestrze kontenerów, jak i uruchomione kontenery.

W tym artykule skupiono się na tym, jak zabezpieczyć kontenery w układzie AKS. Omawiane kwestie:

> [!div class="checklist"]
> * Skanowanie w poszukiwaniu i korygowanie luk w zabezpieczeniach obrazu
> * Automatyczne wyzwalanie i ponowne wdrażanie obrazów kontenerów po zaktualizowaniu obrazu podstawowego

Można również zapoznać się z najlepszymi rozwiązaniami w zakresie [zabezpieczeń klastra][best-practices-cluster-security] i [zabezpieczeń zasobników][best-practices-pod-security].

Można również użyć [zabezpieczeń kontenera w usłudze Security Center,][security-center-containers] aby ułatwić skanowanie kontenerów w poszukiwaniu luk w zabezpieczeniach.  Istnieje również [integracja usługi Azure Container Registry][security-center-acr] z usługą Security Center, aby chronić obrazy i rejestr przed lukami w zabezpieczeniach.

## <a name="secure-the-images-and-run-time"></a>Zabezpieczanie obrazów i czas wykonywania

**Wskazówki dotyczące najlepszych rozwiązań** — skanowanie obrazów kontenerów w poszukiwaniu luk w zabezpieczeniach i wdrażanie tylko obrazów, które przeszły weryfikację. Regularnie aktualizuj obrazy podstawowe i środowisko wykonawcze aplikacji, a następnie ponownie rozmieszcza obciążenia w klastrze AKS.

Jedną z obaw związanych z przyjęciem obciążeń opartych na kontenerach jest weryfikowanie zabezpieczeń obrazów i środowiska wykonawczego używanego do tworzenia własnych aplikacji. Jak upewnić się, że nie wprowadzasz luk w zabezpieczeniach do swoich wdrożeń? Przepływ pracy wdrażania powinien obejmować proces skanowania obrazów kontenerów przy użyciu narzędzi, takich jak [Twistlock][twistlock] lub [Aqua,][aqua]a następnie zezwalać tylko na wdrażanie zweryfikowanych obrazów.

![Skanowanie i korygowanie obrazów kontenerów, sprawdzanie poprawności i wdrażanie](media/operator-best-practices-container-security/scan-container-images-simplified.png)

W przykładzie rzeczywistym można użyć potoku ciągłej integracji i ciągłego wdrażania (CI/CD) do automatyzacji skanowania obrazu, weryfikacji i wdrożeń. Usługa Azure Container Registry zawiera te możliwości skanowania luk w zabezpieczeniach.

## <a name="automatically-build-new-images-on-base-image-update"></a>Automatyczne tworzenie nowych obrazów na podstawie aktualizacji obrazu podstawowego

**Wskazówki dotyczące najlepszych praktyk** — podczas używania obrazów podstawowych dla obrazów aplikacji należy używać automatyzacji do tworzenia nowych obrazów po zaktualizowaniu obrazu podstawowego. Ponieważ te obrazy podstawowe zazwyczaj zawierają poprawki zabezpieczeń, należy zaktualizować wszystkie obrazy kontenerów aplikacji podrzędnych.

Za każdym razem, gdy obraz podstawowy jest aktualizowany, wszystkie obrazy kontenerów podrzędnych powinny być również aktualizowane. Ten proces kompilacji powinny być zintegrowane z potoków sprawdzania poprawności i wdrażania, takich jak [potoki platformy Azure][azure-pipelines] lub usługi Jenkins. Te potoki upewnia się, że aplikacje nadal działać na zaktualizowanych obrazów opartych. Po weryfikacji obrazów kontenera aplikacji wdrożenia usługi AKS można zaktualizować w celu uruchomienia najnowszych, bezpiecznych obrazów.

Zadania rejestru kontenerów platformy Azure można również automatycznie aktualizować obrazy kontenerów po zaktualizowaniu obrazu podstawowego. Ta funkcja pozwala na tworzenie niewielkiej liczby obrazów podstawowych i regularnie aktualizować je za pomocą poprawek błędów i zabezpieczeń.

Aby uzyskać więcej informacji na temat aktualizacji obrazów podstawowych, zobacz [Automatyzacja kompilacji obrazów na podstawie aktualizacji obrazu podstawowego za pomocą zadań rejestru kontenerów platformy Azure][acr-base-image-update].

## <a name="next-steps"></a>Następne kroki

W tym artykule skupiono się na tym, jak zabezpieczyć kontenery. Aby zaimplementować niektóre z tych obszarów, zobacz następujące artykuły:

* [Automatyzacja kompilacji obrazu na podstawie podstawowej aktualizacji obrazu za pomocą zadań rejestru kontenerów platformy Azure][acr-base-image-update]

<!-- EXTERNAL LINKS -->
[azure-pipelines]: /azure/devops/pipelines/?view=vsts
[twistlock]: https://www.twistlock.com/
[aqua]: https://www.aquasec.com/

<!-- INTERNAL LINKS -->
[best-practices-cluster-security]: operator-best-practices-cluster-security.md
[best-practices-pod-security]: developer-best-practices-pod-security.md
[acr-base-image-update]: ../container-registry/container-registry-tutorial-base-image-update.md
[security-center-containers]: /azure/security-center/container-security
[security-center-acr]: /azure/security-center/azure-container-registry-integration