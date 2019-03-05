---
title: Zarządzanie użytkownikami i rolami w obszarze roboczym usługi Azure Machine Learning
titleSuffix: Azure Machine Learning service
description: Dowiedz się, jak zarządzać użytkownikami i rolami w obszarze roboczym usługi Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
author: hning86
ms.author: haining
ms.date: 2/20/2019
ms.custom: seodec18
ms.openlocfilehash: 623aaff3cba86e8e523a86e4adcb0a359c339c45
ms.sourcegitcommit: 8b41b86841456deea26b0941e8ae3fcdb2d5c1e1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/05/2019
ms.locfileid: "57336468"
---
# <a name="manage-users-and-roles-in-an-azure-machine-learning-workspace"></a>Zarządzanie użytkownikami i rolami w obszarze roboczym usługi Azure Machine Learning

W tym artykule dowiesz się, jak dodać użytkowników do obszaru roboczego usługi Azure Machine Learning. Poznasz również sposób przypisywania użytkowników do różnych ról i tworzenie ról niestandardowych.

## <a name="built-in-roles"></a>Wbudowane role
Obszar roboczy usługi uczenie maszynowe Azure jest zasobem platformy Azure. Podobnie jak inne zasoby platformy Azure gdy zostanie utworzony nowy obszar roboczy usługi Azure Machine Learning, chodzi przy użyciu trzech ról domyślne. Można dodać użytkowników do obszaru roboczego i przypisać je do jednej z tych wbudowanych ról.

- **Czytelnik**
    
    Ta rola zezwala na akcje tylko do odczytu w obszarze roboczym. Czytelnicy mogą listy i wyświetlenie zasobów w obszarze roboczym, ale nie można utworzyć lub zaktualizować te zasoby.

- **Współautor**
    
    Ta rola pozwala użytkownikom na wyświetlanie, tworzenie, edytowanie lub usuwanie (jeśli dotyczy) zasobów w obszarze roboczym. Na przykład współautorzy mogą utworzyć eksperyment, tworzenie lub dołączyć klastra obliczeniowego, przesłać przebiegu i wdrażanie usługi sieci web.

- **Właściciel**
    
    Ta rola zapewnia użytkownikom pełny dostęp do obszaru roboczego, w tym możliwość wyświetlania, tworzenia, edytować lub usunąć (jeśli dotyczy) zasobów w obszarze roboczym. Ponadto można Dodaj lub usuń użytkowników i zmieniać przypisania ról.

## <a name="add-or-remove-users"></a>Dodawanie lub usuwanie użytkowników
Jeśli jesteś właścicielem obszaru roboczego, można dodawać i usuwać użytkowników z obszaru roboczego, wybierając jedną z następujących metod:
- [Interfejs użytkownika witryny Azure portal](/azure/role-based-access-control/role-assignments-portal)
- [Program PowerShell](/azure/role-based-access-control/role-assignments-powershell)
- [Interfejs wiersza polecenia platformy Azure](/azure/role-based-access-control/role-assignments-cli)
- [Interfejs API REST](/azure/role-based-access-control/role-assignments-rest)
- [Szablony usługi Azure Resource Manager](/azure/role-based-access-control/role-assignments-template)

Jeśli zainstalowano [interfejsu wiersza polecenia usługi Azure Machine Learning](reference-azure-machine-learning-cli.md), umożliwia także polecenia interfejsu wiersza polecenia można dodać użytkowników do obszaru roboczego.

```azurecli-interactive 
az ml workspace share -n <workspace_name> -g <resource_group_name> --role <role_name> --user <user_corp_email_address>
```

`user` Pole jest adres e-mail istniejącego użytkownika w wystąpieniu usługi Azure Active Directory lokalizacji obszaru roboczego subskrypcji nadrzędnej. Poniżej przedstawiono przykładowy sposób użycia tego polecenia:

```azurecli-interactive 
az ml workspace share -n my_workspace -g my_resource_group --role Contributor --user jdoe@contoson.com
```

## <a name="create-custom-role"></a>Utwórz rolę niestandardową
Jeśli wbudowane role są niewystarczające, możesz utworzyć niestandardowe role. Role niestandardowe mogą mieć odczytu, zapisu, usuwanie oraz obliczenia uprawnień dotyczących zasobów w tym obszarze roboczym. Rolę można udostępnić w poziomie określonego obszaru roboczego, na poziomie grupy określonego zasobu lub na poziomie określonej subskrypcji. 

> [!NOTE]
> Musisz być właścicielem zasobu na tym poziomie można tworzyć niestandardowe role w ramach tego zasobu.

Aby utworzyć rolę niestandardową, najpierw należy utworzyć plik JSON definicji roli, który określa uprawnienia i zakres dla roli. Na przykład poniżej przedstawiono plik definicji roli dla roli niestandardowej o nazwie "Analityk danych" w zakresie na poziomie określonego obszaru roboczego:

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

Po wdrożeniu tej roli staje się dostępna w określonym obszarze roboczym. Teraz możesz dodać i przypisać tę rolę w witrynie Azure portal. Lub można dodać użytkownika z tą rolą przy użyciu `az ml workspace share` interfejsu wiersza polecenia:

```azurecli-interactive
az ml workspace share -n my_workspace -g my_resource_group --role "Data Scientist" --user jdoe@contoson.com
```


Aby uzyskać więcej informacji o niestandardowych rolach na platformie Azure, zobacz [w tym dokumencie](/azure/role-based-access-control/custom-roles).

## <a name="next-steps"></a>Kolejne kroki

Postępuj zgodnie z samouczka kompleksowo przedstawiono sposób tworzenia, uczenia i wdrażania modeli przy użyciu usługi Azure Machine Learning za pomocą obszaru roboczego.

> [!div class="nextstepaction"]
> [Samouczek: Szkolenie modeli](tutorial-train-models-with-aml.md)
