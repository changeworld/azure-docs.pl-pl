---
title: Role i uprawnienia RBAC
description: Użyj kontroli dostępu opartej na rolach (RBAC) na platformie Azure, a w celu zapewnienia szczegółowych uprawnień do zasobów w usłudze Azure Container Registry.
ms.topic: article
ms.date: 03/20/2019
ms.openlocfilehash: 8ef4f26dfd59c7b3b177ef58fa23e08f7e66d328
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/24/2019
ms.locfileid: "74456241"
---
# <a name="azure-container-registry-roles-and-permissions"></a>Azure Container Registry ról i uprawnień

Usługa Azure Container Registry obsługuje zestaw ról platformy Azure, które zapewniają różne poziomy uprawnień do usługi Azure Container Registry. Za pomocą [kontroli dostępu opartej na rolach](../role-based-access-control/index.yml) (RBAC) na platformie Azure można przypisywać określone uprawnienia użytkownikom lub podmiotom usługi, które muszą korzystać z rejestru.

| Rola/uprawnienie       | [Menedżer zasobów dostępu](#access-resource-manager) | [Utwórz/usuń rejestr](#create-and-delete-registry) | [Obraz wypychany](#push-image) | [Obraz ściągania](#pull-image) | [Usuń dane obrazu](#delete-image-data) | [Zmień zasady](#change-policies) |   [Podpisz obrazy](#sign-images)  |
| ---------| --------- | --------- | --------- | --------- | --------- | --------- | --------- |
| Właściciel | X | X | X | X | X | X |  |  
| Współautor | X | X | X |  X | X | X |  |  
| Czytelnik | X |  |  | X |  |  |  |
| AcrPush |  |  | X | X | |  |  |  
| AcrPull |  |  |  | X |  |  |  |  
| AcrDelete |  |  |  |  | X |  |  |
| AcrImageSigner |  |  |  |  |  |  | X |

## <a name="differentiate-users-and-services"></a>Odróżnianie użytkowników i usług

Wszystkie uprawnienia są stosowane, najlepszym rozwiązaniem jest zapewnienie najbardziej ograniczonego zestawu uprawnień dla osoby lub usługi, w celu wykonania zadania. Następujące zbiory uprawnień reprezentują zestaw możliwości, które mogą być używane przez ludzi i usługi bezobsługowe.

### <a name="cicd-solutions"></a>Rozwiązania CI/CD

W przypadku automatyzowania `docker build` poleceń z rozwiązań ciągłej integracji/ciągłego wdrażania wymagane są `docker push` możliwości. W przypadku tych scenariuszy usługi bezobsługowe sugerujemy przypisanie roli **AcrPush** . Ta rola, w przeciwieństwie do szerszej roli **współautor** , uniemożliwia kontu wykonywanie innych operacji rejestru lub uzyskiwanie dostępu do Azure Resource Manager.

### <a name="container-host-nodes"></a>Węzły hosta kontenera

Podobnie węzły korzystające z kontenerów wymagają roli **AcrPull** , ale nie wymagają możliwości **czytnika** .

### <a name="visual-studio-code-docker-extension"></a>Visual Studio Code rozszerzenie platformy Docker

Dla narzędzi, takich jak Visual Studio Code [Docker Extension](https://code.visualstudio.com/docs/azure/docker), wymagany jest dodatkowy dostęp dostawcy zasobów, aby wyświetlić listę dostępnych rejestrów kontenerów platformy Azure. W takim przypadku należy zapewnić użytkownikom dostęp do roli **czytelnik** lub **współautor** . Role te zezwalają na `docker pull`, `docker push`, `az acr list`, `az acr build`i inne możliwości. 

## <a name="access-resource-manager"></a>Menedżer zasobów dostępu

Do zarządzania Azure Portal i rejestrem za pomocą [interfejsu wiersza polecenia platformy Azure](/cli/azure/)jest wymagany dostęp Azure Resource Manager. Na przykład aby uzyskać listę rejestrów przy użyciu polecenia `az acr list`, musisz mieć ten zestaw uprawnień. 

## <a name="create-and-delete-registry"></a>Tworzenie i usuwanie rejestru

Możliwość tworzenia i usuwania rejestrów kontenerów platformy Azure.

## <a name="push-image"></a>Obraz wypychany

Możliwość `docker push` obrazu lub wypchnięcia innego [obsługiwanego artefaktu](container-registry-image-formats.md) , takiego jak wykres Helm, do rejestru. Wymaga [uwierzytelniania](container-registry-authentication.md) z rejestrem przy użyciu autoryzowanej tożsamości. 

## <a name="pull-image"></a>Obraz ściągania

Możliwość `docker pull` obrazu nieobjętego kwarantanną lub ściągnięcia innego [obsługiwanego artefaktu](container-registry-image-formats.md) , takiego jak wykres Helm, z rejestru. Wymaga [uwierzytelniania](container-registry-authentication.md) z rejestrem przy użyciu autoryzowanej tożsamości.

## <a name="delete-image-data"></a>Usuń dane obrazu

Możliwość [usuwania obrazów kontenera](container-registry-delete.md)lub usuwania innych [obsługiwanych artefaktów](container-registry-image-formats.md) , takich jak wykresy Helm, z rejestru.

## <a name="change-policies"></a>Zmień zasady

Możliwość konfigurowania zasad w rejestrze. Zasady obejmują przeczyszczanie obrazu, Włączanie kwarantanny i podpisywanie obrazu.

## <a name="sign-images"></a>Podpisz obrazy

Możliwość podpisywania obrazów, zwykle przypisywanych do zautomatyzowanego procesu, który mógłby korzystać z nazwy głównej usługi. To uprawnienie jest zwykle połączone z [obrazem push](#push-image) , aby umożliwić wypychanie zaufanego obrazu do rejestru. Aby uzyskać szczegółowe informacje, zobacz [zaufanie zawartości w Azure Container Registry](container-registry-content-trust.md).

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej na temat przypisywania ról RBAC do tożsamości platformy Azure przy użyciu [Azure Portal](../role-based-access-control/role-assignments-portal.md), [interfejsu wiersza polecenia platformy Azure](../role-based-access-control/role-assignments-cli.md)lub innych narzędzi platformy Azure.

* Dowiedz się więcej na temat [opcji uwierzytelniania](container-registry-authentication.md) dla Azure Container Registry.
