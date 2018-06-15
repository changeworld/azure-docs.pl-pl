---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: container-registry
author: mmacy
ms.service: container-registry
ms.topic: include
ms.date: 04/23/2018
ms.author: marsma
ms.custom: include file
ms.openlocfilehash: 6ed114ea6162c9d4888b6f86998cfb422a3944e8
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2018
ms.locfileid: "32198230"
---
## <a name="create-a-service-principal"></a>Tworzenie nazwy głównej usługi

Można utworzyć nazwy głównej usługi z dostępem do rejestru kontenera, możesz użyć następujących skryptu. Aktualizacja `ACR_NAME` zmiennej o nazwie rejestru kontenera i opcjonalnie `--role` wartość w [az ad sp utworzyć do rbac] [ az-ad-sp-create-for-rbac] polecenie, aby przydzielić inne uprawnienia. Musi mieć [interfejsu wiersza polecenia Azure](/cli/azure/install-azure-cli) zainstalowana tak, aby użyć tego skryptu.

Po uruchomieniu skryptu należy wziąć pod uwagę nazwy głównej usługi **identyfikator** i **hasło**. Po utworzeniu swoje poświadczenia, można skonfigurować aplikacji i usług, do uwierzytelniania do rejestru kontenera jako nazwy głównej usługi.

[!code-azurecli-interactive[acr-sp-create](~/cli_scripts/container-registry/service-principal-create/service-principal-create.sh)]

## <a name="use-an-existing-service-principal"></a>Użyj istniejącej nazwy głównej usługi

Aby udzielić dostępu do rejestru do istniejącej nazwy głównej usługi, należy przypisać do nazwy głównej usługi roli. Podobnie jak w przypadku tworzenia nowej usługi podmiotu zabezpieczeń, można przyznać ściągania, wypychania i ściągania i dostępu do właściciela.

Poniższy skrypt używa [utworzenia przypisania roli az] [ az-role-assignment-create] polecenie, aby udzielić *ściągania* uprawnień do nazwy głównej usługi można określić w `SERVICE_PRINCIPAL_ID` zmiennej. Dostosuj `--role` wartość, jeśli chcesz udzielić różne poziomy dostępu.

[!code-azurecli-interactive[acr-sp-role-assign](~/cli_scripts/container-registry/service-principal-assign-role/service-principal-assign-role.sh)]

<!-- LINKS - Internal -->
[az-ad-sp-create-for-rbac]: /cli/azure/ad/sp#az_ad_sp_create_for_rbac
[az-role-assignment-create]: /cli/azure/role/assignment#az_role_assignment_create
