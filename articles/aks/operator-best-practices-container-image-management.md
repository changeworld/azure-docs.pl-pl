---
title: Operator najlepsze rozwiązania — Zarządzanie obrazami kontenerów usługi Kubernetes w usłudze Azure (AKS)
description: Poznaj klastra operator najlepsze rozwiązania dotyczące zarządzanie i zabezpieczanie obrazów kontenerów w usłudze Azure Kubernetes Service (AKS)
services: container-service
author: mlearned
ms.service: container-service
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: mlearned
ms.openlocfilehash: 3feadaca361950df2a09f8da33fe380fc3763763
ms.sourcegitcommit: 0f54f1b067f588d50f787fbfac50854a3a64fff7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/12/2019
ms.locfileid: "67614828"
---
# <a name="best-practices-for-container-image-management-and-security-in-azure-kubernetes-service-aks"></a>Najlepsze rozwiązania dotyczące zarządzania obrazami kontenera i zabezpieczeń w usłudze Azure Kubernetes Service (AKS)

Podczas tworzenia i uruchamiania aplikacji w usłudze Azure Kubernetes Service (AKS), zabezpieczenia kontenerów i obrazy kontenera jest klucza. Kontenerów, między innymi nieaktualna oprzeć obrazów lub środowisk wykonawczych aplikacji bez wprowadzenia ryzyko związane z zabezpieczeniami i możliwości ataku. Aby ograniczyć te zagrożenia, należy zintegrować narzędzia skanowania w poszukiwaniu i Korygowanie problemów z kontenerów w czasie kompilacji, a także środowiska uruchomieniowego. Wcześniej w procesie, który zostanie przechwycony luk w zabezpieczeniach lub nieaktualne obrazu podstawowego czyli bardziej bezpiecznego klastra. W tym artykule *kontenery* oznacza, że oba obrazy kontenerów przechowywane w rejestrze kontenerów i uruchomione kontenery.

Ten artykuł koncentruje się na sposób zabezpieczania kontenerów w usłudze AKS. Omawiane kwestie:

> [!div class="checklist"]
> * Wyszukiwanie i korygowanie luk w zabezpieczeniach obrazu
> * Automatyczne wyzwalanie i ponowne wdrażanie obrazów kontenerów, po zaktualizowaniu obrazu podstawowego

Można także zapoznać się z najlepszymi rozwiązaniami dotyczącymi [zabezpieczeń klastra][best-practices-cluster-security] i [zabezpieczenia pod][best-practices-pod-security].

## <a name="secure-the-images-and-run-time"></a>Zabezpieczanie obrazów i w czasie wykonywania

**Najważniejsze wskazówki** — skanowania obrazów kontenerów dla luki w zabezpieczeniach i wdrażać tylko obrazy, które przeszły sprawdzania poprawności. Regularnie aktualizować obrazy podstawowe i środowisko uruchomieniowe aplikacji, a następnie ponownie wdrożyć obciążenia w klastrze AKS.

Jeden problem z przyjęciem obciążeń opartych na kontenerach jest sprawdzanie zabezpieczeń obrazów i środowisko uruchomieniowe używane do tworzenia własnych aplikacji. Jak należy upewnić się, że do wdrożenia, nie spowodują luk w zabezpieczeniach? Przepływ pracy wdrożenia powinien obejmować proces skanowania obrazów kontenera przy użyciu narzędzi, takich jak [TwistLock][twistlock] lub [akwamaryna][aqua], a następnie zezwala na wdrażanie tylko zweryfikowanych obrazów.

![Skanowanie i skorygować obrazów kontenerów, sprawdzanie poprawności i wdrażanie](media/operator-best-practices-container-security/scan-container-images-simplified.png)

W przykładzie rzeczywistych umożliwia ciągłej integracji i ciągłego wdrażania (CI/CD) potoku Automatyzowanie skanowania obrazu, weryfikacji i wdrożeń. Usługa Azure Container Registry obejmuje te luki w zabezpieczeniach funkcją skanowania.

## <a name="automatically-build-new-images-on-base-image-update"></a>Automatyczne tworzenie nowych obrazów w aktualizacji obrazów podstawowych

**Najważniejsze wskazówki** — Użyj obrazy podstawowe dla aplikacji, obrazów, użyj automatyzacji do tworzenia nowych obrazów po zaktualizowaniu obrazu podstawowego. Jak te obrazy podstawowe zwykle zawierają poprawki zabezpieczeń, należy zaktualizować wszystkie obrazy kontenerów aplikacji.

Każdorazowo, gdy zostanie zaktualizowany obraz podstawowy, również należy uaktualnić wszystkie obrazy kontenerów podrzędnych. Ten proces kompilacji należy zintegrować z potokami walidacji i wdrażania, takimi jak [Azure Pipelines][azure-pipelines] lub Jenkins. Te potoki gwarantuje, że aplikacje w dalszym ciągu uruchamiać na zaktualizowane na podstawie obrazów. Po zweryfikowaniu obrazów kontenerów aplikacji wdrożeń usługi AKS następnie może zostać zaktualizowana do uruchomienia obrazów najnowsze i bezpieczne.

Zadania usługi Azure rejestru kontenerów można również aktualizować obrazy kontenerów po zaktualizowaniu obrazu podstawowego. Ta funkcja umożliwia tworzenie małej liczby obrazy podstawowe i regularnie je zaktualizować przy użyciu poprawki usterek i zabezpieczeń.

Aby uzyskać więcej informacji o aktualizacjach obrazu podstawowego, zobacz Automatyzacja kompilowania obrazów [w podstawowej aktualizacji obrazu przy użyciu Azure Container Registry zadań][acr-base-image-update].

## <a name="next-steps"></a>Następne kroki

Ten artykuł koncentruje się na sposób zabezpieczania kontenerów. Aby zaimplementować, niektóre z tych obszarów, zobacz następujące artykuły:

* [Automatyzacja kompilacji obrazów w podstawowej aktualizacji obrazu przy użyciu Azure Container Registry zadań][acr-base-image-update]

<!-- EXTERNAL LINKS -->
[azure-pipelines]: /azure/devops/pipelines/?view=vsts
[twistlock]: https://www.twistlock.com/
[aqua]: https://www.aquasec.com/

<!-- INTERNAL LINKS -->
[best-practices-cluster-security]: operator-best-practices-cluster-security.md
[best-practices-pod-security]: developer-best-practices-pod-security.md
[acr-base-image-update]: ../container-registry/container-registry-tutorial-base-image-update.md
