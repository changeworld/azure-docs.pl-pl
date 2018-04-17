---
title: Skonfiguruj MSI skali maszyny wirtualnej platformy Azure, ustawić za pomocą wiersza polecenia platformy Azure
description: Krok kroku instrukcje dotyczące konfigurowania zarządzane tożsamości usługi (MSI) na platformie Azure zestawu skalowania maszyn wirtualnych, przy użyciu wiersza polecenia platformy Azure.
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/15/2018
ms.author: daveba
ms.openlocfilehash: 9cdf5225f2d87fffa2290e3edd09d4ae829aee21
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
---
# <a name="configure-a-virtual-machine-scale-set-managed-service-identity-msi-using-azure-cli"></a>Konfigurowanie maszyny wirtualnej zestawu skalowania zarządzane usługi tożsamości (MSI) przy użyciu wiersza polecenia platformy Azure

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Tożsamość usługi zarządzanej zapewnia usług platformy Azure przy użyciu tożsamości automatycznie zarządzane w usłudze Azure Active Directory. Ta tożsamość służy do uwierzytelniania do dowolnej usługi obsługującej uwierzytelniania usługi Azure AD, bez konieczności poświadczeń w kodzie. 

W tym artykule dowiesz się, jak włączyć i usunąć MSI skali maszyny wirtualnej platformy Azure, ustawić za pomocą wiersza polecenia platformy Azure.

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [msi-qs-configure-prereqs](../../../includes/active-directory-msi-qs-configure-prereqs.md)]

Aby uruchomić przykłady skryptów interfejsu wiersza polecenia, masz trzy opcje:

- Użyj [powłoki chmury Azure](../../cloud-shell/overview.md) z portalu Azure (zobacz następną sekcję).
- Użyć osadzonego powłoki chmury Azure za pośrednictwem "Spróbuj on" przycisk, znajdujący się w prawym górnym rogu każdej blok kodu.
- [Zainstaluj najnowszą wersję interfejsu wiersza polecenia 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli) (2.0.13 lub nowsza), jeśli wolisz korzystać z konsoli lokalnej interfejsu wiersza polecenia. 

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="enable-msi-during-creation-of-an-azure-virtual-machine-scale-set"></a>Włącz MSI podczas tworzenia zestawu skali maszyny wirtualnej platformy Azure

Aby utworzyć skalowania maszyn wirtualnych z obsługą MSI zestaw:

1. Jeśli używasz interfejsu wiersza polecenia Azure w lokalnej konsoli, najpierw zaloguj się do platformy Azure przy użyciu [logowania az](/cli/azure/reference-index#az_login). Użyj konta, które jest skojarzone z subskrypcją platformy Azure, w którym chcesz wdrożyć zestaw skali maszyny wirtualnej:

   ```azurecli-interactive
   az login
   ```

2. Utwórz [grupy zasobów](../../azure-resource-manager/resource-group-overview.md#terminology) zawierania i wdrażania zestawu skali maszyny wirtualnej i powiązanych zasobów, przy użyciu [Tworzenie grupy az](/cli/azure/group/#az_group_create). Ten krok można pominąć, jeśli masz już grupę zasobów, które chcesz użyć:

   ```azurecli-interactive 
   az group create --name myResourceGroup --location westus
   ```

3. Utwórz skali maszyny wirtualnej ustawić za pomocą [az vmss utworzyć](/cli/azure/vmss/#az_vmss_create) . Poniższy przykład tworzy skalowania maszyny wirtualnej, ustaw nazwane *myVMSS* msi, zgodnie z żądaniem `--assign-identity` parametru. `--admin-username` i `--admin-password` parametry Określ konto użytkownika administracyjnego nazwę i hasło do logowania w maszynie wirtualnej. Zaktualizować te wartości jako odpowiednie dla danego środowiska: 

   ```azurecli-interactive 
   az vmss create --resource-group myResourceGroup --name myVMSS --image win2016datacenter --upgrade-policy-mode automatic --custom-data cloud-init.txt --admin-username azureuser --admin-password myPassword12 --assign-identity --generate-ssh-keys
   ```

## <a name="enable-msi-on-an-existing-azure-virtual-machine-scale-set"></a>Włącz MSI na podstawie istniejącego zestawu skalowania maszyny wirtualnej platformy Azure

Jeśli musisz włączyć MSI na podstawie istniejącego zestawu skalowania maszyny wirtualnej platformy Azure:

1. Jeśli używasz interfejsu wiersza polecenia Azure w lokalnej konsoli, najpierw zaloguj się do platformy Azure przy użyciu [logowania az](/cli/azure/reference-index#az_login). Użyj konta, które jest skojarzone z subskrypcją platformy Azure, która zawiera zestaw skali maszyny wirtualnej.

   ```azurecli-interactive
   az login
   ```

2. Użyj [przypisać tożsamość vmss az](/cli/azure/vmss/identity/#az_vmss_identity_assign) polecenie, aby dodać instalatora MSI do istniejącej maszyny Wirtualnej:

   ```azurecli-interactive
   az vmss identity assign -g myResourceGroup -n myVMSS
   ```

## <a name="remove-msi-from-an-azure-virtual-machine-scale-set"></a>Usuń MSI z zestawu skalowania maszyny wirtualnej platformy Azure

Jeśli masz zestaw skali maszyny wirtualnej, który nie będzie już potrzebował MSI:

1. Jeśli używasz interfejsu wiersza polecenia Azure w lokalnej konsoli, najpierw zaloguj się do platformy Azure przy użyciu [logowania az](/cli/azure/reference-index#az_login). Użyj konta, które jest skojarzone z subskrypcją platformy Azure, która zawiera zestaw skali maszyny wirtualnej.

   ```azurecli-interactive
   az login
   ```

2. Użyj [usunąć tożsamości vmss az](/cli/azure/vmss/identity/#az_vmss_remove_identity) polecenie, aby usunąć plik MSI:

   ```azurecli-interactive
   az vmss identity remove -g myResourceGroup -n myVMSS --identities readerID writerID
   ```

## <a name="next-steps"></a>Kolejne kroki

- [Omówienie tożsamość usługi zarządzanej](overview.md)
- Pełna Azure Tworzenie szybkiego startu zestawu skalowania maszyny wirtualnej, zobacz: 

  - [Utwórz zestaw skali maszyny wirtualnej z interfejsu wiersza polecenia](../../virtual-machines/linux/tutorial-create-vmss.md#create-a-scale-set)

W poniższej sekcji komentarzy umożliwia wyrazić swoją opinię i pomóc nam dostosować i kształtu zawartość.
















