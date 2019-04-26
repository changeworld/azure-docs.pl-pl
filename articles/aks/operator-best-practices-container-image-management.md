---
title: Operator najlepsze rozwiązania — Zarządzanie obrazami kontenerów usługi Kubernetes w usłudze Azure (AKS)
description: Poznaj klastra operator najlepsze rozwiązania dotyczące zarządzanie i zabezpieczanie obrazów kontenerów w usłudze Azure Kubernetes Service (AKS)
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: iainfou
ms.openlocfilehash: 1cc91f55d3895f06176875cb9ae620685dc09a26
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60464821"
---
# <a name="best-practices-for-container-image-management-and-security-in-azure-kubernetes-service-aks"></a>Najlepsze rozwiązania dotyczące zarządzania obrazami kontenera i zabezpieczeń w usłudze Azure Kubernetes Service (AKS)

Podczas tworzenia i uruchamiania aplikacji w usłudze Azure Kubernetes Service (AKS), zabezpieczenia kontenerów i obrazy kontenera jest klucza. Kontenerów, między innymi nieaktualna oprzeć obrazów lub środowisk wykonawczych aplikacji bez wprowadzenia ryzyko związane z zabezpieczeniami i możliwości ataku. Aby ograniczyć te zagrożenia, należy zintegrować narzędzia skanowania w poszukiwaniu i Korygowanie problemów z kontenerów w czasie kompilacji, a także środowiska uruchomieniowego. Wcześniej w procesie, który zostanie przechwycony luk w zabezpieczeniach lub nieaktualne obrazu podstawowego czyli bardziej bezpiecznego klastra. W tym artykule *kontenery* oznacza, że oba obrazy kontenerów przechowywane w rejestrze kontenerów i uruchomione kontenery.

Ten artykuł koncentruje się na sposób zabezpieczania kontenerów w usłudze AKS. Omawiane kwestie:

> [!div class="checklist"]
> * Wyszukiwanie i korygowanie luk w zabezpieczeniach obrazu
> * Zaufany rejestru za pomocą obrazów kontenerów podpisane cyfrowo
> * Automatyczne wyzwalanie i ponowne wdrażanie obrazów kontenerów, po zaktualizowaniu obrazu podstawowego

Można również przeczytać najlepsze rozwiązania dotyczące [klastra zabezpieczeń] [ best-practices-cluster-security] i [zasobnika zabezpieczeń][best-practices-pod-security].

## <a name="secure-the-images-and-run-time"></a>Zabezpieczanie obrazów i w czasie wykonywania

**Najważniejsze wskazówki** — skanowania obrazów kontenerów dla luki w zabezpieczeniach i wdrażać tylko obrazy, które przeszły sprawdzania poprawności. Regularnie aktualizować obrazy podstawowe i środowisko uruchomieniowe aplikacji, a następnie ponownie wdrożyć obciążenia w klastrze AKS.

Jeden problem z przyjęciem obciążeń opartych na kontenerach jest sprawdzanie zabezpieczeń obrazów i środowisko uruchomieniowe używane do tworzenia własnych aplikacji. Jak należy upewnić się, że do wdrożenia, nie spowodują luk w zabezpieczeniach? Wdrażanie przepływu pracy powinna zawierać procesu do skanowania obrazów kontenerów przy użyciu narzędzi takich jak [Twistlock] [ twistlock] lub [Akwamaryna][aqua], a następnie Zezwalaj tylko na zweryfikowanych obrazy mają zostać wdrożone.

![Skanowanie i skorygować obrazów kontenerów, sprawdzanie poprawności i wdrażanie](media/operator-best-practices-container-security/scan-container-images-simplified.png)

W przykładzie rzeczywistych umożliwia ciągłej integracji i ciągłego wdrażania (CI/CD) potoku Automatyzowanie skanowania obrazu, weryfikacji i wdrożeń. Usługa Azure Container Registry obejmuje te luki w zabezpieczeniach funkcją skanowania.

## <a name="use-a-trusted-registry"></a>Za pomocą zaufanego rejestru

**Najważniejsze wskazówki** — Ogranicz rejestry obrazów, które zasobników i wdrożeń można użyć. Zezwalaj tylko zaufane rejestrów, gdy sprawdzanie poprawności i kontrolować obrazy, które są dostępne.

Poziom zabezpieczeń można także cyfrowe podpisywanie obrazów kontenerów, tak samo, jak można cyfrowego podpisywania kodu aplikacji. Następnie tylko udostępnisz AKS do wdrażania obrazów podpisem. Ten proces zapewnia dodatkową warstwę zabezpieczeń, w tym ograniczyć AKS do tylko ściągania obrazów cyfrowo podpisane i zaufane przez Ciebie, nie tylko obrazy, zaliczonych wyboru luk w zabezpieczeniach. Możesz również upewnij się, że obraz kontenera nie zostało naruszone i zastąpione przez obraz o dokładnie takiej samej nazwie.

Zaufany rejestrów, zawierających obrazy kontenerów podpisanych cyfrowo zwiększenia złożoności środowiska, ale mogą być wymagane dla niektórych zasad lub zgodność z przepisami. Usługa Azure Container Registry obsługuje korzystanie z rejestrów zaufanych i podpisuje obrazów.

Aby uzyskać więcej informacji o obrazach podpisanych cyfrowo, zobacz [zawartości zaufania w usłudze Azure Container Registry][acr-content-trust].

## <a name="automatically-build-new-images-on-base-image-update"></a>Automatyczne tworzenie nowych obrazów w aktualizacji obrazów podstawowych

**Najważniejsze wskazówki** — Użyj obrazy podstawowe dla aplikacji, obrazów, użyj automatyzacji do tworzenia nowych obrazów po zaktualizowaniu obrazu podstawowego. Jak te obrazy podstawowe zwykle zawierają poprawki zabezpieczeń, należy zaktualizować wszystkie obrazy kontenerów aplikacji.

Każdorazowo, gdy zostanie zaktualizowany obraz podstawowy, również należy uaktualnić wszystkie obrazy kontenerów podrzędnych. Ten proces kompilacji, należy włączyć do sprawdzania poprawności i potoków wdrażania, takie jak [potoki Azure] [ azure-pipelines] lub systemu Jenkins. Te potoki gwarantuje, że aplikacje w dalszym ciągu uruchamiać na zaktualizowane na podstawie obrazów. Po zweryfikowaniu obrazów kontenerów aplikacji wdrożeń usługi AKS następnie może zostać zaktualizowana do uruchomienia obrazów najnowsze i bezpieczne.

Zadania usługi Azure rejestru kontenerów można również aktualizować obrazy kontenerów po zaktualizowaniu obrazu podstawowego. Ta funkcja umożliwia tworzenie małej liczby obrazy podstawowe i regularnie je zaktualizować przy użyciu poprawki usterek i zabezpieczeń.

Aby uzyskać więcej informacji na temat aktualizacji obrazu podstawowego zobacz [Automatyzacja obrazu jest oparta na aktualizacji obrazów podstawowych zadań rejestru kontenera platformy Azure][acr-base-image-update].

## <a name="next-steps"></a>Kolejne kroki

Ten artykuł koncentruje się na sposób zabezpieczania kontenerów. Aby zaimplementować, niektóre z tych obszarów, zobacz następujące artykuły:

* [Automatyzowanie kompilacji obrazów po aktualizacji obraz podstawowy z zadaniami rejestru kontenera platformy Azure][acr-base-image-update]
* [Zaufaj zawartości w usłudze Azure Container Registry][acr-content-trust]

<!-- EXTERNAL LINKS -->
[azure-pipelines]: /azure/devops/pipelines/?view=vsts
[twistlock]: https://www.twistlock.com/
[aqua]: https://www.aquasec.com/

<!-- INTERNAL LINKS -->
[best-practices-cluster-security]: operator-best-practices-cluster-security.md
[best-practices-pod-security]: developer-best-practices-pod-security.md
[acr-content-trust]: ../container-registry/container-registry-content-trust.md
[acr-base-image-update]: ../container-registry/container-registry-tutorial-base-image-update.md
