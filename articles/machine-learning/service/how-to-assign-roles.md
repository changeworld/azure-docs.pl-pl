---
title: Zarządzanie rolami w obszarze roboczym
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak uzyskać dostęp do obszaru roboczego Azure Machine Learning przy użyciu kontroli dostępu opartej na rolach (RBAC).
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: larryfr
author: Blackmist
ms.date: 11/06/2019
ms.custom: seodec18
ms.openlocfilehash: b5f42ac015ff492b67aa73c799ec1d952b468ed0
ms.sourcegitcommit: a10074461cf112a00fec7e14ba700435173cd3ef
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/12/2019
ms.locfileid: "73932177"
---
# <a name="manage-access-to-an-azure-machine-learning-workspace"></a>Zarządzanie dostępem do obszaru roboczego Azure Machine Learning
[!INCLUDE [aml-applies-to-enterprise-sku](../../../includes/aml-applies-to-enterprise-sku.md)]

W tym artykule dowiesz się, jak zarządzać dostępem do obszaru roboczego Azure Machine Learning. [Kontrola dostępu oparta na rolach (RBAC)](/azure/role-based-access-control/overview) służy do zarządzania dostępem do zasobów platformy Azure. Użytkownicy w Azure Active Directory są przypisani do określonych ról, które przyznają dostęp do zasobów. Platforma Azure udostępnia wbudowane role i możliwość tworzenia ról niestandardowych.

## <a name="default-roles"></a>Role domyślne

Obszar roboczy Azure Machine Learning jest zasobem platformy Azure. Podobnie jak w przypadku innych zasobów platformy Azure, podczas tworzenia nowego obszaru roboczego Azure Machine Learning dostępne są trzy domyślne role. Możesz dodać użytkowników do obszaru roboczego i przypisać je do jednej z tych wbudowanych ról.

| Rola | Poziom dostępu |
| --- | --- |
| **Czytelnik** | Akcje tylko do odczytu w obszarze roboczym. Czytelnicy mogą wyświetlać i przeglądać zasoby w obszarze roboczym, ale nie mogą tworzyć ani aktualizować tych zasobów. |
| **Współautor** | Wyświetlanie, tworzenie, edytowanie lub usuwanie (jeśli dotyczy) zasobów w obszarze roboczym. Można na przykład utworzyć eksperyment, utworzyć lub dołączyć klaster obliczeniowy, przesłać przebieg i wdrożyć usługę sieci Web. |
| **Właściciel** | Pełny dostęp do obszaru roboczego, w tym możliwość wyświetlania, tworzenia, edytowania lub usuwania (jeśli dotyczy) zasobów w obszarze roboczym. Ponadto można zmienić przypisania ról. |

> [!IMPORTANT]
> Dostęp do roli można ograniczyć do wielu poziomów na platformie Azure. Na przykład ktoś z dostępem właściciela do obszaru roboczego może nie mieć dostępu właściciela do grupy zasobów, która zawiera obszar roboczy. Aby uzyskać więcej informacji, zobacz [jak działa RBAC](/azure/role-based-access-control/overview#how-rbac-works).

Aby uzyskać więcej informacji na temat określonych ról wbudowanych, zobacz [role wbudowane dla platformy Azure](/azure/role-based-access-control/built-in-roles).

## <a name="manage-workspace-access"></a>Zarządzanie dostępem do obszaru roboczego

Jeśli jesteś właścicielem obszaru roboczego, możesz dodawać i usuwać role dla obszaru roboczego. Możesz również przypisywać role do użytkowników. Skorzystaj z poniższych linków, aby dowiedzieć się, jak zarządzać dostępem:
- [Interfejs użytkownika Azure Portal](/azure/role-based-access-control/role-assignments-portal)
- [Program PowerShell](/azure/role-based-access-control/role-assignments-powershell)
- [Interfejs wiersza polecenia platformy Azure](/azure/role-based-access-control/role-assignments-cli)
- [Interfejs API REST](/azure/role-based-access-control/role-assignments-rest)
- [Szablony Azure Resource Manager](/azure/role-based-access-control/role-assignments-template)

Jeśli zainstalowano [interfejs wiersza polecenia Azure Machine Learning](reference-azure-machine-learning-cli.md), można także przypisać role do użytkowników za pomocą interfejsu CLI.

```azurecli-interactive 
az ml workspace share -w <workspace_name> -g <resource_group_name> --role <role_name> --user <user_corp_email_address>
```

Pole `user` jest adresem e-mail istniejącego użytkownika w wystąpieniu Azure Active Directory, w którym znajduje się subskrypcja nadrzędna obszaru roboczego. Oto przykład użycia tego polecenia:

```azurecli-interactive 
az ml workspace share -w my_workspace -g my_resource_group --role Contributor --user jdoe@contoson.com
```

## <a name="create-custom-role"></a>Tworzenie roli niestandardowej

Jeśli wbudowane role są niewystarczające, można utworzyć role niestandardowe. Role niestandardowe mogą mieć uprawnienia do odczytu, zapisu, usuwania i zasobów obliczeniowych w tym obszarze roboczym. Rolę można udostępnić na określonym poziomie obszaru roboczego, na poziomie określonego grupy zasobów lub na określonym poziomie subskrypcji.

> [!NOTE]
> Aby utworzyć role niestandardowe w ramach tego zasobu, musisz być właścicielem zasobu na tym poziomie.

Aby utworzyć rolę niestandardową, należy najpierw skonstruować plik JSON definicji roli, który określa uprawnienie i zakres roli. W poniższym przykładzie zdefiniowano rolę niestandardową o nazwie "analityk danych" objętą zakresem na określonym poziomie obszaru roboczego:

`data_scientist_role.json`:
```json
{
    "Name": "Data Scientist",
    "IsCustom": true,
    "Description": "Can run experiment but can't create or delete compute.",
    "Actions": ["*"],
    "NotActions": [
        "Microsoft.MachineLearningServices/workspaces/*/delete",
        "Microsoft.MachineLearningServices/workspaces/computes/*/write",
        "Microsoft.MachineLearningServices/workspaces/computes/*/delete", 
        "Microsoft.Authorization/*/write"
    ],
    "AssignableScopes": [
        "/subscriptions/<subscription_id>/resourceGroups/<resource_group_name>/providers/Microsoft.MachineLearningServices/workspaces/<workspace_name>"
    ]
}
```

Można zmienić pole `AssignableScopes`, aby ustawić zakres tej roli niestandardowej na poziomie subskrypcji, na poziomie grupy zasobów lub na określonym poziomie obszaru roboczego.

Ta rola niestandardowa może wykonywać wszystkie czynności w obszarze roboczym, z wyjątkiem następujących akcji:

- Nie można utworzyć ani zaktualizować zasobu obliczeniowego.
- Nie można usunąć zasobu obliczeniowego.
- Nie można dodać, usunąć ani zmienić przypisań ról.
- Nie można usunąć obszaru roboczego.

Aby wdrożyć tę rolę niestandardową, użyj następującego polecenia platformy Azure CLI:

```azurecli-interactive 
az role definition create --role-definition data_scientist_role.json
```

Po wdrożeniu ta rola zostanie udostępniona w określonym obszarze roboczym. Teraz możesz dodać tę rolę i przypisać ją do Azure Portal. Można też przypisać tę rolę do użytkownika przy użyciu polecenia interfejsu wiersza `az ml workspace share`:

```azurecli-interactive
az ml workspace share -w my_workspace -g my_resource_group --role "Data Scientist" --user jdoe@contoson.com
```

Aby uzyskać więcej informacji na temat ról niestandardowych, zobacz [role niestandardowe dla zasobów platformy Azure](/azure/role-based-access-control/custom-roles).

Aby uzyskać więcej informacji o operacjach (działania), których można użyć z rolami niestandardowymi, zobacz [operacje dostawcy zasobów](/azure/role-based-access-control/resource-provider-operations#microsoftmachinelearningservices).

## <a name="next-steps"></a>Następne kroki

- [Omówienie zabezpieczeń przedsiębiorstwa](concept-enterprise-security.md)
- [Bezpieczne uruchamianie eksperymentów i wnioskowania/wyniku w sieci wirtualnej](how-to-enable-virtual-network.md)
- [Samouczek: uczenie modeli](tutorial-train-models-with-aml.md)
- [Operacje dostawcy zasobów](/azure/role-based-access-control/resource-provider-operations#microsoftmachinelearningservices)