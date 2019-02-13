---
title: Usługa Azure Container Registry — role i uprawnienia
description: Użyj kontroli dostępu opartej na rolach na platformie Azure (RBAC) i zarządzania tożsamościami i dostępem (IAM), aby dostarczyć szczegółowe uprawnienia do zasobów w usłudze Azure container registry.
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: article
ms.date: 12/17/2018
ms.author: danlep
ms.openlocfilehash: a4b9b382755e73b6218432624c471346e9698752
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/13/2019
ms.locfileid: "56193394"
---
# <a name="azure-container-registry-roles-and-permissions"></a>Usługa Azure Container Registry role i uprawnienia

Usługa Azure Container Registry obsługuje zestaw ról platformy Azure, które zapewnia różne poziomy uprawnień do usługi Azure container registry. Korzystanie z systemu Azure [kontroli dostępu opartej na rolach](../role-based-access-control/index.yml) (RBAC), aby przypisać uprawnienia do użytkowników lub usług podmiotów zabezpieczeń, które muszą wchodzić w interakcje z rejestru.

| Uprawnienia roli /       | [Dostęp do Menedżera zasobów](#access-resource-manager)| [Tworzenie/usuwanie rejestru](#create-and-delete-registry) | [Wypychanie obrazu](#push-image) | [Ściągnij obraz](#pull-image) | [Zmiany zasad](#change-policies) |   [Obrazy logowania](#sign-images)  |
| ---------| --------- | --------- | --------- | --------- | --------- | --------- |
| Właściciel | X | X | X | X | X |  |  
| Współautor | X | X | X | X | X |  |  
| Czytelnik | X |  |  | X |  |  | 
| AcrPush |  |  | X | X |  |  |  
| AcrPull |  |  |  | X |  |  |  
| AcrImageSigner |  |  |  |  |  | X |

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

## <a name="change-policies"></a>Zmiany zasad

Możliwość konfigurowania zasad w rejestrze. Zasady zawierają przeczyszczanie obraz włączania kwarantanny i podpisywania obrazu.

## <a name="sign-images"></a>Obrazy logowania

Umożliwia ona zalogowanie się obrazy, zwykle przypisane do zautomatyzowanego procesu, którego będzie używać nazwy głównej usługi. To uprawnienie jest zwykle w połączeniu z [wypychania obraz](#push-image) umożliwia wypychanie zaufanych obrazu do rejestru. Aby uzyskać więcej informacji, zobacz [zawartości zaufania w usłudze Azure Container Registry](container-registry-content-trust.md).

## <a name="next-steps"></a>Kolejne kroki

* Dowiedz się więcej na temat przypisywania ról RBAC do tożsamości usługi systemu Azure za pomocą [witryny Azure portal](../role-based-access-control/role-assignments-portal.md), [wiersza polecenia platformy Azure](../role-based-access-control/role-assignments-cli.md), lub innych narzędziach platformy Azure.

* Dowiedz się więcej o [opcje uwierzytelniania](container-registry-authentication.md) usługi Azure Container Registry.
