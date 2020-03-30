---
title: Błędy przydziału
description: W tym artykule opisano sposób rozwiązywania błędów przydziału zasobów podczas wdrażania zasobów za pomocą usługi Azure Resource Manager.
ms.topic: troubleshooting
ms.date: 03/09/2018
ms.openlocfilehash: 410b086b39d63d03491d390364f4aec6300fc7c1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79273789"
---
# <a name="resolve-errors-for-resource-quotas"></a>Resolve errors for resource quotas (Rozwiązywanie błędów z limitami przydziałów zasobów)

W tym artykule opisano błędy przydziału, które mogą wystąpić podczas wdrażania zasobów.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="symptom"></a>Objaw

Jeśli wdrożysz szablon, który tworzy zasoby, które przekraczają przydziały platformy Azure, pojawi się błąd wdrożenia, który wygląda następująco:

```
Code=OperationNotAllowed
Message=Operation results in exceeding quota limits of Core.
Maximum allowed: 4, Current in use: 4, Additional requested: 2.
```

Możesz też zobaczyć:

```
Code=ResourceQuotaExceeded
Message=Creating the resource of type <resource-type> would exceed the quota of <number>
resources of type <resource-type> per resource group. The current resource count is <number>,
please delete some resources of this type before creating a new one.
```

## <a name="cause"></a>Przyczyna

Limity przydziału są stosowane dla każdej grupy zasobów, subskrypcji, konta i innych zakresów. Na przykład subskrypcja może być skonfigurowana w taki sposób, aby ograniczyć liczbę rdzeni dla regionu. Jeśli spróbujesz wdrożyć maszynę wirtualną o liczbie rdzeni większej niż dozwolona, otrzymasz błąd informujący o przekroczeniu limitu przydziału.
Aby uzyskać pełne informacje o przydziałach, zobacz [Limity subskrypcji i usług platformy Azure, przydziały i ograniczenia](../../azure-resource-manager/management/azure-subscription-service-limits.md).

## <a name="troubleshooting"></a>Rozwiązywanie problemów

### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

W przypadku interfejsu `az vm list-usage` wiersza polecenia platformy Azure użyj tego polecenia, aby znaleźć przydziały maszyn wirtualnych.

```azurecli
az vm list-usage --location "South Central US"
```

Który zwraca:

```output
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

W przypadku programu PowerShell użyj polecenia **Get-AzVMUsage,** aby znaleźć przydziały maszyny wirtualnej.

```powershell
Get-AzVMUsage -Location "South Central US"
```

Który zwraca:

```output
Name                             Current Value Limit  Unit
----                             ------------- -----  ----
Availability Sets                            0  2000 Count
Total Regional Cores                         0   100 Count
Virtual Machines                             0 10000 Count
```

## <a name="solution"></a>Rozwiązanie

Aby zażądać zwiększenia przydziału, przejdź do portalu i złóż problem z pomocą techniczną. W przypadku problemu z pomocą techniczną poproś o zwiększenie przydziału dla regionu, w którym chcesz wdrożyć.

> [!NOTE]
> Należy pamiętać, że dla grup zasobów przydział jest dla każdego regionu, a nie dla całej subskrypcji. Jeśli musisz wdrożyć 30 rdzeni w zachodnie stany USA, musisz poprosić o 30 rdzeni Menedżera zasobów w zachodnie stany USA. Jeśli musisz wdrożyć 30 rdzeni w dowolnym z regionów, do których masz dostęp, należy poprosić o 30 rdzeni Menedżera zasobów we wszystkich regionach.
>
>

1. Wybierz pozycję **Subskrypcje**.

   ![Subskrypcje](./media/error-resource-quota/subscriptions.png)

2. Wybierz subskrypcję, która wymaga zwiększenia limitu przydziału.

   ![Wybieranie subskrypcji](./media/error-resource-quota/select-subscription.png)

3. Wybierz **użycie + przydziały**

   ![Wybieranie użycia i przydziałów](./media/error-resource-quota/select-usage-quotas.png)

4. W prawym górnym rogu wybierz pozycję **Poproś o zwiększenie**.

   ![Zwiększenie żądania](./media/error-resource-quota/request-increase.png)

5. Wypełnij formularze dotyczące typu przydziału, który chcesz zwiększyć.

   ![Wypełnij formularz](./media/error-resource-quota/forms.png)