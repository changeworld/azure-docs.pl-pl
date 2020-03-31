---
title: Role i uprawnienia RBAC
description: Użyj kontroli dostępu opartej na rolach platformy Azure (RBAC) oraz zarządzania tożsamościami i dostępem (IAM), aby zapewnić szczegółowe uprawnienia do zasobów w rejestrze kontenerów platformy Azure.
ms.topic: article
ms.date: 12/02/2019
ms.openlocfilehash: 3fb103ac4c4dac736b3c0fc99b2cf49f01e9e005
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74893488"
---
# <a name="azure-container-registry-roles-and-permissions"></a>Role i uprawnienia rejestru kontenerów platformy Azure

Usługa Azure Container Registry obsługuje zestaw [wbudowanych ról platformy Azure,](../role-based-access-control/built-in-roles.md) które zapewniają różne poziomy uprawnień do rejestru kontenerów platformy Azure. Użyj [kontroli dostępu opartej na rolach](../role-based-access-control/index.yml) platformy Azure (RBAC), aby przypisać określone uprawnienia użytkownikom, jednostkom usługi lub innym tożsamościom, które muszą wchodzić w interakcje z rejestrem. 

| Rola/uprawnienie       | [Menedżer zasobów programu Access](#access-resource-manager) | [Tworzenie/usuwanie rejestru](#create-and-delete-registry) | [Obraz wypychania](#push-image) | [Pociągnij obraz](#pull-image) | [Usuwanie danych obrazu](#delete-image-data) | [Zmienianie zasad](#change-policies) |   [Podpisywanie obrazów](#sign-images)  |
| ---------| --------- | --------- | --------- | --------- | --------- | --------- | --------- |
| Właściciel | X | X | X | X | X | X |  |  
| Współautor | X | X | X |  X | X | X |  |  
| Czytelnik | X |  |  | X |  |  |  |
| Acrpush ( Acrpush ) |  |  | X | X | |  |  |  
| AcrPull ( AcrPull ) |  |  |  | X |  |  |  |  
| AcrDelete (AcrDelete) |  |  |  |  | X |  |  |
| AcrImageSigner (Znak AcrImage) |  |  |  |  |  |  | X |

## <a name="differentiate-users-and-services"></a>Różnicowanie użytkowników i usług

Za każdym razem, gdy uprawnienia są stosowane, najlepszym rozwiązaniem jest zapewnienie najbardziej ograniczonego zestawu uprawnień dla osoby lub usługi, aby wykonać zadanie. Następujące zestawy uprawnień reprezentują zestaw możliwości, które mogą być używane przez ludzi i usługi bezgłowy.

### <a name="cicd-solutions"></a>Rozwiązania w zakresie ciągłej integracji/ciągłego wdrażania

Podczas automatyzacji `docker build` poleceń z rozwiązań ciągłej integracji/ciągłego wdrażania potrzebne są `docker push` funkcje. W przypadku tych scenariuszy usługi bezgłowy zalecamy przypisanie roli **AcrPush.** Ta rola, w przeciwieństwie do szerszej roli **współautora,** uniemożliwia kontu wykonywanie innych operacji rejestru lub uzyskiwanie dostępu do usługi Azure Resource Manager.

### <a name="container-host-nodes"></a>Węzły hosta kontenerów

Podobnie węzły z uruchomionymi kontenerami wymagają roli **AcrPull,** ale nie powinny wymagać funkcji **programu Reader.**

### <a name="visual-studio-code-docker-extension"></a>Rozszerzenie platformy Docker kodu programu Visual Studio

W przypadku narzędzi, takich jak [rozszerzenie platformy Docker](https://code.visualstudio.com/docs/azure/docker)kodu programu Visual Studio, wymagany jest dodatkowy dostęp dostawcy zasobów do listy dostępnych rejestrów kontenerów platformy Azure. W takim przypadku zapewnij użytkownikom dostęp do roli **Czytnik** lub **Współautor.** Te role `docker pull` `docker push`umożliwiają `az acr list` `az acr build`, , , i inne możliwości. 

## <a name="access-resource-manager"></a>Menedżer zasobów programu Access

Dostęp usługi Azure Resource Manager jest wymagany dla portalu Azure i zarządzania rejestrami za pomocą [interfejsu wiersza polecenia platformy Azure.](/cli/azure/) Na przykład, aby uzyskać listę rejestrów `az acr list` za pomocą polecenia, należy tego zestawu uprawnień. 

## <a name="create-and-delete-registry"></a>Tworzenie i usuwanie rejestru

Możliwość tworzenia i usuwania rejestrów kontenerów platformy Azure.

## <a name="push-image"></a>Obraz wypychania

Możliwość `docker push` obraza lub wypchnięcia innego [obsługiwanego artefaktu,](container-registry-image-formats.md) takiego jak wykres Helm, do rejestru. Wymaga [uwierzytelnienia](container-registry-authentication.md) w rejestrze przy użyciu autoryzowanej tożsamości. 

## <a name="pull-image"></a>Pociągnij obraz

Możliwość obrazu `docker pull` nie poddanego kwarantannie lub wyciągnięcia innego [obsługiwanego artefaktu,](container-registry-image-formats.md) takiego jak wykres Helm, z rejestru. Wymaga [uwierzytelnienia](container-registry-authentication.md) w rejestrze przy użyciu autoryzowanej tożsamości.

## <a name="delete-image-data"></a>Usuwanie danych obrazu

Możliwość [usuwania obrazów kontenerów](container-registry-delete.md)lub usuwania innych [obsługiwanych artefaktów,](container-registry-image-formats.md) takich jak wykresy helm, z rejestru.

## <a name="change-policies"></a>Zmienianie zasad

Możliwość konfigurowania zasad w rejestrze. Zasady obejmują czyszczenie obrazu, włączanie kwarantanny i podpisywanie obrazów.

## <a name="sign-images"></a>Podpisywanie obrazów

Możliwość podpisywania obrazów, zwykle przypisane do zautomatyzowanego procesu, który będzie używać jednostki usługi. To uprawnienie jest zazwyczaj połączone z [obrazem wypychanym,](#push-image) aby umożliwić wypychanie zaufanego obrazu do rejestru. Aby uzyskać szczegółowe informacje, zobacz [Zaufanie zawartości w rejestrze kontenerów platformy Azure](container-registry-content-trust.md).

## <a name="custom-roles"></a>Role niestandardowe

Podobnie jak w przypadku innych zasobów platformy Azure, można utworzyć własne [role niestandardowe](../role-based-access-control/custom-roles.md) z uprawnieniami szczegółowe do usługi Azure Container Registry. Następnie przypisz role niestandardowe do użytkowników, podmiotów usługi lub innych tożsamości, które muszą wchodzić w interakcje z rejestrem. 

Aby ustalić, które uprawnienia mają być stosowane do roli niestandardowej, zobacz listę [akcji](../role-based-access-control/resource-provider-operations.md#microsoftcontainerregistry)Microsoft.ContainerRegistry , przejrzyj dozwolone akcje [wbudowanych ról ACR](../role-based-access-control/built-in-roles.md)lub uruchom następujące polecenie:

```azurecli
az provider operation show --namespace Microsoft.ContainerRegistry
```

Aby zdefiniować rolę niestandardową, zobacz [Kroki tworzenia roli niestandardowej](../role-based-access-control/custom-roles.md#steps-to-create-a-custom-role).

> [!IMPORTANT]
> W roli niestandardowej usługa Azure Container Registry nie obsługuje `Microsoft.ContainerRegistry/*` obecnie `Microsoft.ContainerRegistry/registries/*` symboli wieloznacznych, takich jak lub które udzielają dostępu do wszystkich pasujących akcji. Określ dowolną wymaganą akcję indywidualnie w roli.

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o przypisywaniu ról RBAC do tożsamości platformy Azure przy użyciu [witryny Azure portal](../role-based-access-control/role-assignments-portal.md), [interfejsu wiersza polecenia platformy Azure](../role-based-access-control/role-assignments-cli.md)lub innych narzędzi platformy Azure.

* Dowiedz się więcej o [opcjach uwierzytelniania](container-registry-authentication.md) dla rejestru kontenerów platformy Azure.

* Dowiedz się więcej o włączaniu [uprawnień o zakresie repozytorium](container-registry-repository-scoped-permissions.md) (w wersji zapoznawczej) w rejestrze kontenerów.