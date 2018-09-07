---
title: Jak przypisać tożsamości usługi Zarządzanej dostępu do zasobów platformy Azure przy użyciu wiersza polecenia platformy Azure
description: Krok po kroku instrukcje dotyczące przypisywania Instalatora MSI dla jednego zasobu, uzyskać dostęp do innego zasobu przy użyciu wiersza polecenia platformy Azure.
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
ms.openlocfilehash: a5da06eac7f4680282aad305f57cb9ca1c9d5730
ms.sourcegitcommit: d211f1d24c669b459a3910761b5cacb4b4f46ac9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/06/2018
ms.locfileid: "44028358"
---
# <a name="assign-a-managed-service-identity-msi-access-to-a-resource-using-azure-cli"></a>Przypisywanie dostępu tożsamości usługi zarządzanej (MSI) do zasobów przy użyciu wiersza polecenia platformy Azure

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Po skonfigurowaniu zasobu platformy Azure przy użyciu pliku MSI, można zapewnić dostęp MSI do innego zasobu, tak jak dowolnego podmiotu zabezpieczeń. W tym przykładzie pokazano, jak przyznać maszynie wirtualnej platformy Azure lub dostępu tożsamości usługi Zarządzanej zestaw skalowania maszyn wirtualnych do konta usługi Azure storage przy użyciu wiersza polecenia platformy Azure.

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [msi-qs-configure-prereqs](../../../includes/active-directory-msi-qs-configure-prereqs.md)]

Aby uruchomić przykłady skryptów interfejsu wiersza polecenia, masz trzy opcje:

- Użyj [usługi Azure Cloud Shell](../../cloud-shell/overview.md) w witrynie Azure portal (patrz następny rozdział).
- Użyj osadzonego usługi Azure Cloud Shell za pomocą "Try It" przycisk znajdujący się w prawym górnym rogu każdego bloku kodu.
- [Zainstaluj najnowszą wersję interfejsu wiersza polecenia 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli) (2.0.13 lub nowszej), jeśli wolisz używać lokalnej konsoli interfejsu wiersza polecenia. 

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="use-rbac-to-assign-the-msi-access-to-another-resource"></a>Przypisywanie dostępu tożsamości usługi Zarządzanej do innego zasobu przy użyciu kontroli RBAC

Po włączeniu tożsamości usługi Zarządzanej w obrębie zasobu platformy Azure, takich jak [maszyny wirtualnej platformy Azure](qs-configure-cli-windows-vm.md) lub [zestawu skalowania maszyn wirtualnych platformy Azure](qs-configure-cli-windows-vmss.md): 

1. Jeśli używasz interfejsu wiersza polecenia platformy Azure w konsoli lokalnej, najpierw zaloguj się do platformy Azure za pomocą polecenia [az login](/cli/azure/reference-index#az-login). Użyj konta które jest skojarzone z subskrypcją platformy Azure w ramach której chcesz wdrożyć zestaw skalowania maszyny Wirtualnej lub maszyny wirtualnej:

   ```azurecli-interactive
   az login
   ```

2. W tym przykładzie udostępniamy możliwość dostępu do maszyny wirtualnej platformy Azure na koncie magazynu. Najpierw używamy [az resource list](/cli/azure/resource/#az-resource-list) można pobrać nazwy głównej usługi dla maszyny wirtualnej o nazwie "myVM":

   ```azurecli-interactive
   spID=$(az resource list -n myVM --query [*].identity.principalId --out tsv)
   ```
   Dla zestawu skalowania maszyn wirtualnych platformy Azure polecenie jest taka sama, z wyjątkiem tego miejsca, możesz uzyskać nazwy głównej usługi dla zestawu skalowania maszyn wirtualnych o nazwie "DevTestVMSS":
   
   ```azurecli-interactive
   spID=$(az resource list -n DevTestVMSS --query [*].identity.principalId --out tsv)
   ```

3. Po utworzeniu identyfikator jednostki usługi, użyj [utworzenia przypisania roli az](/cli/azure/role/assignment#az-role-assignment-create) nadać maszyny wirtualnej lub skalowania maszyn wirtualnych należy ustawić na konto magazynu o nazwie "myStorageAcct" access "Czytelnik":

   ```azurecli-interactive
   az role assignment create --assignee $spID --role 'Reader' --scope /subscriptions/<mySubscriptionID>/resourceGroups/<myResourceGroup>/providers/Microsoft.Storage/storageAccounts/myStorageAcct
   ```

## <a name="troubleshooting"></a>Rozwiązywanie problemów

Plik MSI dla zasobu nie jest wyświetlany na liście dostępnych tożsamości, sprawdź, czy poprawnie włączono pliku MSI. W naszym przypadku możemy przejść z powrotem do maszyny wirtualnej platformy Azure lub zestawu skalowania maszyn wirtualnych w [witryny Azure portal](https://portal.azure.com) oraz:

- Spójrz na stronie "Konfiguracja" i upewnij się, włączoną tożsamością usługi Zarządzanej = "Yes".
- Spójrz na stronie "Rozszerzenia" i upewnij się, z rozszerzeniem MSI zostało pomyślnie wdrożone (**rozszerzenia** strona nie jest dostępna dla zestawu skalowania maszyn wirtualnych platformy Azure).

Jeśli jest albo nieprawidłowa, może być konieczne ponownie Wdróż ponownie MSI Twojego zasobu i rozwiązywanie problemów błędu wdrożenia.

## <a name="related-content"></a>Powiązana zawartość

- Aby uzyskać omówienie MSI, zobacz [Przegląd tożsamości usługi zarządzanej](overview.md).
- Aby włączyć MSI w maszynie wirtualnej platformy Azure, zobacz [konfigurowania Azure VM tożsamość usługi zarządzanej (MSI) przy użyciu wiersza polecenia platformy Azure](qs-configure-cli-windows-vm.md).
- Aby włączyć tożsamości usługi Zarządzanej w zestawie skalowania maszyn wirtualnych platformy Azure, zobacz [Konfigurowanie usługi Azure Virtual Machine skalowania Ustaw tożsamość usługi zarządzanej (MSI) przy użyciu witryny Azure portal](qs-configure-portal-windows-vmss.md)

W poniższej sekcji komentarzy umożliwia opinią i Pomóż nam analizy i połącz kształt naszej zawartości.

