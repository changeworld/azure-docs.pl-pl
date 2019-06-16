---
title: Usługa Azure Container Registry — role i uprawnienia
description: Użyj kontroli dostępu opartej na rolach na platformie Azure (RBAC) i zarządzania tożsamościami i dostępem (IAM), aby dostarczyć szczegółowe uprawnienia do zasobów w usłudze Azure container registry.
services: container-registry
author: dlepow
manager: jeconnoc
ms.service: container-registry
ms.topic: article
ms.date: 03/20/2019
ms.author: danlep
ms.openlocfilehash: d62dd6c65975d63a0127bb5dd1c62cd741b59ac6
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "67067996"
---
# <a name="azure-container-registry-roles-and-permissions"></a>Usługa Azure Container Registry role i uprawnienia

Usługa Azure Container Registry obsługuje zestaw ról platformy Azure, które zapewnia różne poziomy uprawnień do usługi Azure container registry. Korzystanie z systemu Azure [kontroli dostępu opartej na rolach](../role-based-access-control/index.yml) (RBAC), aby przypisać uprawnienia do użytkowników lub usług podmiotów zabezpieczeń, które muszą wchodzić w interakcje z rejestru.

| Uprawnienia roli /       | [Dostęp do Menedżera zasobów](#access-resource-manager) | [Tworzenie/usuwanie rejestru](#create-and-delete-registry) | [Wypychanie obrazu](#push-image) | [Ściągnij obraz](#pull-image) | [Usuń obraz danych](#delete-image-data) | [Zmiany zasad](#change-policies) |   [Obrazy logowania](#sign-images)  |
| ---------| --------- | --------- | --------- | --------- | --------- | --------- | --------- |
| Właściciel | X | X | X | X | X | X |  |  
| Współautor | X | X | X |  X | X | X |  |  
| Czytelnik | X |  |  |  |  |  |  |
| AcrPush |  |  | X | X | |  |  |  
| AcrPull |  |  |  | X |  |  |  |  
| AcrDelete |  |  |  |  | X |  |  |
| AcrImageSigner |  |  |  |  |  |  | X |

## <a name="differentiate-users-and-services"></a>Rozróżniania użytkowników i usług

Wszystkie uprawnienia czasu są stosowane, najlepszym rozwiązaniem jest zapewnienie najbardziej ograniczony zestaw uprawnień dla osoby lub usługi, do wykonania zadania. Następujące zestawy uprawnień reprezentują zestaw możliwości, które mogą być używane przez ludzi i bezobsługowego usługi.

### <a name="cicd-solutions"></a>Ciągła Integracja/ciągłe dostarczanie rozwiązań

Podczas automatyzacji `docker build` poleceń z rozwiązań do ciągłej integracji/ciągłego wdrażania, potrzebujesz `docker push` możliwości. Dla tych scenariuszy usługi bezobsługowego zalecamy przypisanie **AcrPush** roli. Tej roli, w odróżnieniu od szerszym **Współautor** roli, uniemożliwia konta wykonywania innych operacji rejestru lub ograniczyć dostęp do usługi Azure Resource Manager.

### <a name="container-host-nodes"></a>Węzły hosta kontenera

Podobnie, potrzebujesz węzły kontenerów **AcrPull** roli, ale nie należy wymagać **czytnika** możliwości.

### <a name="visual-studio-code-docker-extension"></a>Rozszerzenie programu Visual Studio Code Docker

Dla narzędzi, takich jak Visual Studio Code [rozszerzenia platformy Docker](https://code.visualstudio.com/docs/azure/docker), wymagany jest dostęp dostawcy dodatkowych zasobów, aby wyświetlić listę rejestrów dostępnych kontenerów platformy Azure. W tym przypadku zapewnić użytkownikom dostęp do **czytnika** lub **Współautor** roli. Zezwalaj na te role `docker pull`, `docker push`, `az acr list`, `az acr build`i innych funkcji. 

## <a name="access-resource-manager"></a>Dostęp do Menedżera zasobów

Dostęp usługi Azure Resource Manager jest wymagany dla platformy Azure management portal i rejestru za pomocą [wiersza polecenia platformy Azure](/cli/azure/). Na przykład, aby uzyskać listę rejestrów przy użyciu `az acr list` polecenia, musisz mieć uprawnienie tego zestawu. 

## <a name="create-and-delete-registry"></a>Tworzenie i usuwanie rejestru

Możliwość tworzenia i usuwania rejestry kontenerów platformy Azure.

## <a name="push-image"></a>Wypychanie obrazu

Możliwość `docker push` obrazu, lub wypchnąć innego [obsługiwane artefaktu](container-registry-image-formats.md) takie jak wykres Helm do rejestru. Wymaga [uwierzytelniania](container-registry-authentication.md) za pomocą rejestru przy użyciu tożsamości autoryzowanych. 

## <a name="pull-image"></a>Ściągnij obraz

Możliwość `docker pull` innych niż-poddane kwarantannie obrazu lub Ściągaj je innym [obsługiwane artefaktu](container-registry-image-formats.md) takie jak wykres Helm z rejestru. Wymaga [uwierzytelniania](container-registry-authentication.md) za pomocą rejestru przy użyciu tożsamości autoryzowanych.

## <a name="delete-image-data"></a>Usuń obraz danych

Możliwość [usuwać obrazów kontenera](container-registry-delete.md), lub usunąć inne [obsługiwane artefaktów](container-registry-image-formats.md) np. wykresów rozwiązania Helm, z rejestru.

## <a name="change-policies"></a>Zmiany zasad

Możliwość konfigurowania zasad w rejestrze. Zasady zawierają przeczyszczanie obraz włączania kwarantanny i podpisywania obrazu.

## <a name="sign-images"></a>Obrazy logowania

Umożliwia ona zalogowanie się obrazy, zwykle przypisane do zautomatyzowanego procesu, którego będzie używać nazwy głównej usługi. To uprawnienie jest zwykle w połączeniu z [wypychania obraz](#push-image) umożliwia wypychanie zaufanych obrazu do rejestru. Aby uzyskać więcej informacji, zobacz [zawartości zaufania w usłudze Azure Container Registry](container-registry-content-trust.md).

## <a name="next-steps"></a>Kolejne kroki

* Dowiedz się więcej na temat przypisywania ról RBAC do tożsamości usługi systemu Azure za pomocą [witryny Azure portal](../role-based-access-control/role-assignments-portal.md), [wiersza polecenia platformy Azure](../role-based-access-control/role-assignments-cli.md), lub innych narzędziach platformy Azure.

* Dowiedz się więcej o [opcje uwierzytelniania](container-registry-authentication.md) usługi Azure Container Registry.
