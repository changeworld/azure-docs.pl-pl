---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: include
ms.date: 12/14/2018
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: 9e4f2e355240ba8682cbe9f86f2be94e7dd0d92d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "70032345"
---
## <a name="create-a-service-principal"></a>Tworzenie nazwy głównej usługi

Aby utworzyć jednostkę usługi z dostępem do rejestru kontenerów, uruchom następujący skrypt w [usłudze Azure Cloud Shell](../articles/cloud-shell/overview.md) lub lokalnej instalacji [interfejsu wiersza polecenia platformy Azure.](/cli/azure/install-azure-cli) Skrypt jest sformatowany dla powłoki Bash.

Przed uruchomieniem skryptu `ACR_NAME` należy zaktualizować zmienną o nazwie rejestru kontenerów. Wartość `SERVICE_PRINCIPAL_NAME` musi być unikatowa w dzierżawie usługi Azure Active Directory. Jeśli zostanie wyświetlony`'http://acr-service-principal' already exists.`błąd " ", określ inną nazwę jednostki usługi.

Opcjonalnie można zmodyfikować `--role` wartość w poleceniu [az ad sp create-for-rbac,][az-ad-sp-create-for-rbac] jeśli chcesz udzielić różnych uprawnień. Aby uzyskać pełną listę ról, zobacz [Role i uprawnienia ACR](https://github.com/Azure/acr/blob/master/docs/roles-and-permissions.md).

Po uruchomieniu skryptu należy zanotować **identyfikator** i **hasło**jednostki usługi. Po uzyskaniu jego poświadczeń można skonfigurować aplikacje i usługi do uwierzytelniania w rejestrze kontenerów jako podmiotu usługi.

<!-- https://github.com/Azure-Samples/azure-cli-samples/blob/master/container-registry/service-principal-create/service-principal-create.sh -->
[!code-azurecli-interactive[acr-sp-create](~/cli_scripts/container-registry/service-principal-create/service-principal-create.sh)]

### <a name="use-an-existing-service-principal"></a>Używanie istniejącego podmiotu usługi

Aby udzielić rejestru dostęp do istniejącej jednostki usługi, należy przypisać nową rolę do jednostki usługi. Podobnie jak w przypadku tworzenia nowego podmiotu usługi, można przyznać ściąganie, wypychanie i ściąganie oraz dostęp do właściciela, między innymi.

Poniższy skrypt używa [polecenia tworzenia przypisania roli az][az-role-assignment-create] w celu przyznania `SERVICE_PRINCIPAL_ID` uprawnień *ściągania* podmiotowi usługi określonemu w zmiennej. Dostosuj `--role` wartość, jeśli chcesz udzielić innego poziomu dostępu.


<!-- https://github.com/Azure-Samples/azure-cli-samples/blob/master/container-registry/service-principal-assign-role/service-principal-assign-role.sh -->
[!code-azurecli-interactive[acr-sp-role-assign](~/cli_scripts/container-registry/service-principal-assign-role/service-principal-assign-role.sh)]

<!-- LINKS - Internal -->
[az-ad-sp-create-for-rbac]: /cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
