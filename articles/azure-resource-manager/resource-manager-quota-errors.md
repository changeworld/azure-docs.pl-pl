---
title: Błędy przydziału usługi Azure | Dokumentacja firmy Microsoft
description: Opisuje sposób rozwiązywania błędy limitu przydziału zasobów.
services: azure-resource-manager
documentationcenter: ''
author: tfitzmac
manager: timlt
editor: ''
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 03/09/2018
ms.author: tomfitz
ms.openlocfilehash: 7938f2c47e4af8d8804191fbb9e55b379f9554ef
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60390231"
---
# <a name="resolve-errors-for-resource-quotas"></a>Rozwiązywanie błędów dla limity przydziałów zasobów

W tym artykule opisano błędy limitu przydziału, które mogą wystąpić podczas wdrażania zasobów.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="symptom"></a>Objaw

W przypadku wdrożenia szablonu, która tworzy zasoby, które przekraczają limity przydziału platformy Azure, otrzymasz błąd wdrażania, który wygląda następująco:

```
Code=OperationNotAllowed
Message=Operation results in exceeding quota limits of Core.
Maximum allowed: 4, Current in use: 4, Additional requested: 2.
```

Lub może zostać wyświetlony:

```
Code=ResourceQuotaExceeded
Message=Creating the resource of type <resource-type> would exceed the quota of <number>
resources of type <resource-type> per resource group. The current resource count is <number>,
please delete some resources of this type before creating a new one.
```

## <a name="cause"></a>Przyczyna

Przydziały są stosowane dla grupy zasobów, subskrypcji, kont i inne zakresy. Na przykład Twoja subskrypcja może być skonfigurowany do ograniczenia liczby rdzeni w regionie. Jeśli spróbujesz wdrożyć maszynę wirtualną za pomocą większej liczby rdzeni niż dozwolone kwotę, otrzymasz komunikat o błędzie informujący, że została przekroczona.
Przydział pełne informacji można uzyskać [subskrypcji platformy Azure i limity, przydziały i ograniczenia](../azure-subscription-service-limits.md).

## <a name="troubleshooting"></a>Rozwiązywanie problemów

### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

W przypadku wiersza polecenia platformy Azure, użyj `az vm list-usage` polecenie, aby znaleźć przydziały maszyny wirtualnej.

```azurecli
az vm list-usage --location "South Central US"
```

Zwraca:

```azurecli
[
  {
    "currentValue": 0,
    "limit": 2000,
    "name": {
      "localizedValue": "Availability Sets",
      "value": "availabilitySets"
    }
  },
  ...
]
```

### <a name="powershell"></a>PowerShell

W przypadku programu PowerShell, użyj **Get AzVMUsage** polecenie, aby znaleźć przydziały maszyny wirtualnej.

```powershell
Get-AzVMUsage -Location "South Central US"
```

Zwraca:

```powershell
Name                             Current Value Limit  Unit
----                             ------------- -----  ----
Availability Sets                            0  2000 Count
Total Regional Cores                         0   100 Count
Virtual Machines                             0 10000 Count
```

## <a name="solution"></a>Rozwiązanie

Aby zażądać zwiększenia limitu przydziału, przejdź do portalu i pliku problemu wymagającego pomocy technicznej. W przypadku problemu wymagającego pomocy technicznej poprosić o zwiększenie limitu przydziału w regionie, w której chcesz wdrożyć.

> [!NOTE]
> Należy pamiętać, że dla grup zasobów, limitu przydziału dla poszczególnych regionów, nie dla całej subskrypcji. Jeśli zajdzie potrzeba wdrożenia 30 rdzeni w regionie zachodnie stany USA, musisz poprosić o 30 rdzeni Menedżera zasobów w regionie zachodnie stany USA. Jeśli zajdzie potrzeba wdrożenia 30 rdzeni we wszystkich regionach, do którego masz dostęp, należy poprosić 30 rdzeni Menedżera zasobów we wszystkich regionach.
>
>

1. Wybierz pozycję **Subskrypcje**.

   ![Subscriptions](./media/resource-manager-quota-errors/subscriptions.png)

2. Wybierz subskrypcję, która wymaga zwiększenia limitu przydziału.

   ![Wybieranie subskrypcji](./media/resource-manager-quota-errors/select-subscription.png)

3. Wybierz **użycie i przydziały**

   ![Wybierz opcję użycie i przydziały](./media/resource-manager-quota-errors/select-usage-quotas.png)

4. W prawym górnym rogu wybierz **zażądać zwiększenia**.

   ![Zażądać zwiększenia](./media/resource-manager-quota-errors/request-increase.png)

5. Wypełnij formularze dotyczące typu przydziału, który chcesz zwiększyć.

   ![Wypełnij formularz](./media/resource-manager-quota-errors/forms.png)