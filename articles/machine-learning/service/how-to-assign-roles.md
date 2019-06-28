---
title: Zarządzanie rolami w obszarze roboczym usługi Azure Machine Learning
titleSuffix: Azure Machine Learning service
description: Dowiedz się, jak uzyskać dostęp do obszaru roboczego usługi Azure Machine Learning, za pomocą kontroli dostępu opartej na rolach (RBAC).
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: larryfr
author: Blackmist
ms.date: 02/20/2019
ms.custom: seodec18
ms.openlocfilehash: e062fd73f2baeb4948430b13e0caa1f5c0b3f066
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/24/2019
ms.locfileid: "67341113"
---
# <a name="manage-access-to-an-azure-machine-learning-workspace"></a>Zarządzanie dostępem do obszaru roboczego usługi Azure Machine Learning

W tym artykule dowiesz się, jak zarządzać dostępem do obszaru roboczego usługi Azure Machine Learning. [Kontrola dostępu oparta na rolach (RBAC)](/azure/role-based-access-control/overview) służy do zarządzania dostępem do zasobów platformy Azure. Użytkownicy w usłudze Azure Active Directory są przypisywane określonych ról, które udzielać dostępu do zasobów. Platforma Azure udostępnia zarówno role wbudowane, jak i możliwość tworzenia ról niestandardowych.

## <a name="default-roles"></a>Domyślne role

Obszar roboczy usługi uczenie maszynowe Azure jest zasobem platformy Azure. Podobnie jak inne zasoby platformy Azure gdy zostanie utworzony nowy obszar roboczy usługi Azure Machine Learning, chodzi przy użyciu trzech ról domyślne. Można dodać użytkowników do obszaru roboczego i przypisać je do jednej z tych wbudowanych ról.

| Rola | Poziom dostępu |
| --- | --- |
| **Czytelnik** | Akcje tylko do odczytu w obszarze roboczym. Czytelnicy mogą listy i wyświetlenie zasobów w obszarze roboczym, ale nie można utworzyć lub zaktualizować te zasoby. |
| **Współautor** | Wyświetlanie, tworzenie, edytowanie lub usuwanie (jeśli dotyczy) zasobów w obszarze roboczym. Na przykład współautorzy mogą utworzyć eksperyment, tworzenie lub dołączyć klastra obliczeniowego, przesłać przebiegu i wdrażanie usługi sieci web. |
| **Właściciel** | Pełny dostęp do obszaru roboczego, w tym możliwość wyświetlania, tworzenia, edytować lub usunąć (jeśli dotyczy) zasobów w obszarze roboczym. Ponadto można zmienić przypisania roli. |

> [!IMPORTANT]
> Dostęp roli może należeć do różnych poziomach na platformie Azure zakresu. Na przykład ktoś z uprawnieniami właściciela do obszaru roboczego nie może mieć dostęp właściciela do grupy zasobów, która zawiera obszar roboczy. Aby uzyskać więcej informacji, zobacz [działa jak RBAC](/azure/role-based-access-control/overview#how-rbac-works).

Aby uzyskać więcej informacji na temat konkretnych ról wbudowanych, zobacz [wbudowanych ról na platformie Azure](/azure/role-based-access-control/built-in-roles).

## <a name="manage-workspace-access"></a>Zarządzanie dostępem do obszaru roboczego

Jeśli jesteś właścicielem obszaru roboczego, można dodać i usunąć role dla obszaru roboczego. Można także przypisać role do użytkowników. Aby dowiedzieć się, jak zarządzać dostępem, należy użyć następujących łączy:
- [Interfejs użytkownika witryny Azure portal](/azure/role-based-access-control/role-assignments-portal)
- [PowerShell](/azure/role-based-access-control/role-assignments-powershell)
- [Interfejs wiersza polecenia platformy Azure](/azure/role-based-access-control/role-assignments-cli)
- [REST API](/azure/role-based-access-control/role-assignments-rest)
- [Szablony usługi Azure Resource Manager](/azure/role-based-access-control/role-assignments-template)

Jeśli zainstalowano [interfejsu wiersza polecenia usługi Azure Machine Learning](reference-azure-machine-learning-cli.md), umożliwia także polecenia interfejsu wiersza polecenia do przypisywania ról do użytkowników.

```azurecli-interactive 
az ml workspace share -w <workspace_name> -g <resource_group_name> --role <role_name> --user <user_corp_email_address>
```

`user` Pole jest adres e-mail istniejącego użytkownika w wystąpieniu usługi Azure Active Directory lokalizacji obszaru roboczego subskrypcji nadrzędnej. Poniżej przedstawiono przykładowy sposób użycia tego polecenia:

```azurecli-interactive 
az ml workspace share -w my_workspace -g my_resource_group --role Contributor --user jdoe@contoson.com
```

## <a name="create-custom-role"></a>Tworzenie roli niestandardowej

Jeśli wbudowane role są niewystarczające, możesz utworzyć niestandardowe role. Role niestandardowe mogą mieć odczytu, zapisu, usuwanie oraz obliczenia uprawnień dotyczących zasobów w tym obszarze roboczym. Rolę można udostępnić w poziomie określonego obszaru roboczego, na poziomie grupy określonego zasobu lub na poziomie określonej subskrypcji.

> [!NOTE]
> Musisz być właścicielem zasobu na tym poziomie można tworzyć niestandardowe role w ramach tego zasobu.

Aby utworzyć rolę niestandardową, należy najpierw skonstruuj pliku JSON definicji roli, który określa zakres roli i uprawnień. W poniższym przykładzie zdefiniowano niestandardową rolę o nazwie "Analityk danych" w zakresie na poziomie określonego obszaru roboczego:

`data_scientist_role.json` :
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

Możesz zmienić `AssignableScopes` pola, aby ustawić zakres tę rolę niestandardową na poziomie subskrypcji, względem poziomu grupy zasobów lub na poziomie określonego obszaru roboczego.

Tę rolę niestandardową można zrobić wszystko w obszarze roboczym, z wyjątkiem następujących czynności:

- Go nie można utworzyć lub zaktualizować zasobów obliczeniowych.
- Nie można go usunąć zasób obliczeniowy.
- Go nie można dodać, usunąć lub zmienić przypisania roli.
- Nie można go usunąć obszar roboczy.

Aby wdrożyć tę rolę niestandardową, użyj następującego polecenia wiersza polecenia platformy Azure:

```azurecli-interactive 
az role definition create --role-definition data_scientist_role.json
```

Po wdrożeniu tej roli staje się dostępna w określonym obszarze roboczym. Teraz możesz dodać i przypisać tę rolę w witrynie Azure portal. Lub tę rolę można przypisać do użytkownika, za pomocą `az ml workspace share` interfejsu wiersza polecenia:

```azurecli-interactive
az ml workspace share -n my_workspace -g my_resource_group --role "Data Scientist" --user jdoe@contoson.com
```


Aby uzyskać więcej informacji, zobacz [niestandardowych ról dla zasobów platformy Azure](/azure/role-based-access-control/custom-roles).

## <a name="next-steps"></a>Kolejne kroki

- [Omówienie zabezpieczeń przedsiębiorstwa](concept-enterprise-security.md)
- [Bezpiecznego uruchamiania eksperymentów oraz wnioskowania/wynik wewnątrz sieci wirtualnej](how-to-enable-virtual-network.md)
- [Samouczek: Szkolenie modeli](tutorial-train-models-with-aml.md)
