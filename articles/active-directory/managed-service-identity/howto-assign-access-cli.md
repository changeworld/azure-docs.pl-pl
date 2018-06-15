---
title: Jak przypisać MSI dostępu do zasobów platformy Azure przy użyciu wiersza polecenia platformy Azure
description: Krok po kroku instrukcje dotyczące przypisywania MSI dla jednego zasobu, dostęp do innego zasobu, przy użyciu wiersza polecenia platformy Azure.
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: msi
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/25/2017
ms.author: daveba
ms.openlocfilehash: 947e0140c7943954be5eb285bb7ec514b74e9022
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/10/2018
ms.locfileid: "33929646"
---
# <a name="assign-a-managed-service-identity-msi-access-to-a-resource-using-azure-cli"></a>Przypisywanie dostępu zarządzane tożsamości usługi (MSI) do zasobów przy użyciu wiersza polecenia platformy Azure

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Po skonfigurowaniu zasobów platformy Azure z Instalatora MSI, możesz zapewnić dostęp MSI do innego zasobu, podobnie jak wszelkie podmiotu zabezpieczeń. Ten przykład przedstawia sposób udzielić dostępu MSI zestaw skali maszyny wirtualnej lub maszyny wirtualnej platformy Azure na konto magazynu platformy Azure przy użyciu wiersza polecenia platformy Azure.

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [msi-qs-configure-prereqs](../../../includes/active-directory-msi-qs-configure-prereqs.md)]

Aby uruchomić przykłady skryptów interfejsu wiersza polecenia, masz trzy opcje:

- Użyj [powłoki chmury Azure](../../cloud-shell/overview.md) z portalu Azure (zobacz następną sekcję).
- Użyć osadzonego powłoki chmury Azure za pośrednictwem "Spróbuj on" przycisk, znajdujący się w prawym górnym rogu każdej blok kodu.
- [Zainstaluj najnowszą wersję interfejsu wiersza polecenia 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli) (2.0.13 lub nowsza), jeśli wolisz korzystać z konsoli lokalnej interfejsu wiersza polecenia. 

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="use-rbac-to-assign-the-msi-access-to-another-resource"></a>Użycie funkcji RBAC można przypisać MSI dostęp do innego zasobu

Po włączeniu MSI na zasobów platformy Azure, takich jak [maszyny wirtualnej platformy Azure](qs-configure-cli-windows-vm.md) lub [zestaw skali maszyny wirtualnej platformy Azure](qs-configure-cli-windows-vmss.md): 

1. Jeśli używasz interfejsu wiersza polecenia Azure w lokalnej konsoli, najpierw zaloguj się do platformy Azure przy użyciu [logowania az](/cli/azure/reference-index#az_login). Użyj konta, które jest skojarzone z subskrypcją platformy Azure, w którym chcesz wdrożyć zestaw skali maszyny Wirtualnej lub maszyny wirtualnej:

   ```azurecli-interactive
   az login
   ```

2. W tym przykładzie udostępniamy możliwość sprawowania Azure maszynie wirtualnej dostęp do konta magazynu. Najpierw używamy [listę zasobów az](/cli/azure/resource/#az_resource_list) można uzyskać nazwy głównej usługi dla maszyny wirtualnej o nazwie "myVM":

   ```azurecli-interactive
   spID=$(az resource list -n myVM --query [*].identity.principalId --out tsv)
   ```
   Dla zestawu skalowania maszyny wirtualnej platformy Azure polecenie jest taki sam, z wyjątkiem tutaj, uzyskać nazwę główną usługi dla zestawu skalowania maszyny wirtualnej o nazwie "DevTestVMSS":
   
   ```azurecli-interactive
   spID=$(az resource list -n DevTestVMSS --query [*].identity.principalId --out tsv)
   ```

3. Po utworzeniu Identyfikatora podmiotu zabezpieczeń usługi, użyj [utworzenia przypisania roli az](/cli/azure/role/assignment#az_role_assignment_create) do maszyny wirtualnej lub skalowania maszyny wirtualnej Ustaw "Czytnika" dostęp do konta magazynu o nazwie "myStorageAcct":

   ```azurecli-interactive
   az role assignment create --assignee $spID --role 'Reader' --scope /subscriptions/<mySubscriptionID>/resourceGroups/<myResourceGroup>/providers/Microsoft.Storage/storageAccounts/myStorageAcct
   ```

## <a name="troubleshooting"></a>Rozwiązywanie problemów

Jeśli plik MSI dla zasobu nie występuje na liście dostępnych tożsamości, sprawdź, czy MSI została prawidłowo włączona. W tym przypadku firma Microsoft wrócić do maszyny wirtualnej platformy Azure lub skalowania maszyny wirtualnej w [portalu Azure](https://portal.azure.com) oraz:

- Szukaj na stronie "Konfiguracja" i upewnij się, MSI włączone = "Yes".
- Przyjrzyj się Strona "Rozszerzenia" i upewnij się, z rozszerzeniem MSI pomyślnie wdrożone (**rozszerzenia** strona nie jest dostępna dla zestawu skalowania maszyny wirtualnej platformy Azure).

Jeśli jest albo nieprawidłowa, może być konieczne ponownie Wdróż ponownie MSI na zasobie oraz rozwiązywania problemów dotyczących niepowodzenia wdrożenia.

## <a name="related-content"></a>Zawartość pokrewna

- Omówienie MSI, zobacz [omówienie zarządzane tożsamość usługi](overview.md).
- Aby włączyć MSI w maszynie wirtualnej platformy Azure, zobacz [skonfigurować Azure VM zarządzane usługi tożsamości (MSI) przy użyciu interfejsu wiersza polecenia Azure](qs-configure-cli-windows-vm.md).
- Aby włączyć MSI na podstawie zestawu skali maszyny wirtualnej platformy Azure, zobacz [skonfigurować Azure maszyny wirtualnej skali ustawić zarządzane usługi tożsamości (MSI) przy użyciu portalu Azure](qs-configure-portal-windows-vmss.md)

W poniższej sekcji komentarzy umożliwia wyrazić swoją opinię i pomóc nam dostosować i kształtu zawartość.

