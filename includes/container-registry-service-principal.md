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
ms.openlocfilehash: 1a8a31b34981b0e2b68caf3f5c4952d0ee59ac8e
ms.sourcegitcommit: 7862449050a220133e5316f0030a259b1c6e3004
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/22/2018
ms.locfileid: "53784488"
---
## <a name="create-a-service-principal"></a>Tworzenie nazwy głównej usługi

Aby utworzyć nazwę główną usługi z dostępem do rejestru kontenerów, uruchom następujący skrypt [usługi Azure Cloud Shell](../articles/cloud-shell/overview.md) lub lokalnej instalacji [wiersza polecenia platformy Azure](/cli/azure/install-azure-cli). Skrypt jest sformatowany do powłoki Bash.

Przed uruchomieniem skryptu, należy zaktualizować `ACR_NAME` zmiennej z nazwą rejestru kontenerów. `SERVICE_PRINCIPAL_NAME` Wartość musi być unikatowa w ramach dzierżawy usługi Azure Active Directory. Jeśli zostanie wyświetlony "`'http://acr-service-principal' already exists.`" błąd, podaj inną nazwę dla jednostki usługi.

Opcjonalnie możesz zmodyfikować `--role` wartość w [az ad sp create-for-rbac] [ az-ad-sp-create-for-rbac] polecenia, jeśli chcesz udzielić różne uprawnienia. Aby uzyskać pełną listę ról, zobacz [ACR role i uprawnienia](https://github.com/Azure/acr/blob/master/docs/roles-and-permissions.md).

Po uruchomieniu skryptu, zwróć uwagę na nazwy głównej usługi **identyfikator** i **hasło**. Po utworzeniu swoje poświadczenia, można skonfigurować aplikacje i usługi do uwierzytelniania usługi container registry jako nazwy głównej usługi.

<!-- https://github.com/Azure-Samples/azure-cli-samples/blob/master/container-registry/service-principal-create/service-principal-create.sh --> [!code-azurecli-interactive[acr-sp-create](~/cli_scripts/container-registry/service-principal-create/service-principal-create.sh)]

## <a name="use-an-existing-service-principal"></a>Użyj istniejącej nazwy głównej usługi

Aby przyznać dostęp do rejestru do istniejącej jednostki usługi, należy przypisać roli do jednostki usługi. Podobnie jak w przypadku tworzenia nowej usługi jednostki, można przyznać ściągania, wypychania i ściągania i dostęp właściciela, między innymi.

Poniższy skrypt [utworzenia przypisania roli az] [ az-role-assignment-create] polecenie, aby udzielić *ściągnięcia* uprawnienia dla jednostki usługi, możesz określić w `SERVICE_PRINCIPAL_ID` zmiennej. Dostosuj `--role` wartość, jeśli chcesz udzielić inny poziom dostępu.


<!-- https://github.com/Azure-Samples/azure-cli-samples/blob/master/container-registry/service-principal-assign-role/service-principal-assign-role.sh --> [!code-azurecli-interactive[acr-sp-role-assign](~/cli_scripts/container-registry/service-principal-assign-role/service-principal-assign-role.sh)]

<!-- LINKS - Internal -->
[az-ad-sp-create-for-rbac]: /cli/azure/ad/sp#az_ad_sp_create_for_rbac
[az-role-assignment-create]: /cli/azure/role/assignment#az_role_assignment_create
