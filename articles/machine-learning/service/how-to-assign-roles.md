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
ms.openlocfilehash: f6c74da2e9189c5dddb88cb80f04422f49cfaee2
ms.sourcegitcommit: a8948ddcbaaa22bccbb6f187b20720eba7a17edc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/21/2019
ms.locfileid: "56594257"
---
# <a name="manage-users-and-roles-in-an-azure-machine-learning-workspace"></a>Zarządzanie użytkownikami i rolami w obszarze roboczym usługi Azure Machine Learning

W tym artykule dowiesz się, jak dodać użytkowników do obszaru roboczego usługi Azure Machine Learning i przypisywać je przy użyciu różnych ról. Poznasz również sposób tworzenia ról niestandardowych.

## <a name="built-in-roles"></a>Wbudowane role
Usługa Azure Machine Learning obszar roboczy jest zasobem platformy Azure. I dowolnych zasobów platformy Azure, np. gdy zostanie utworzony nowy obszar roboczy usługi Azure Machine Learning, chodzi przy użyciu trzech ról domyślne. Można dodać użytkowników do obszaru roboczego i przypisać je do jednej z tych wbudowanych ról.

- **Czytelnik**
    
    Ta rola zezwala na akcje tylko do odczytu w obszarze roboczym. Z tą rolą, możesz wyświetlić listę zasobów widoku w obszarze roboczym, takie jak eksperymentów, przebiegów, obliczania, modele, usługi sieci web opublikowane potoków i tak dalej Jednak nie możesz utworzyć lub zaktualizować te zasoby.

- **Współautor**
    
    Ta rola pozwala użytkownikom na wyświetlanie, tworzenie, edytowanie lub usuwanie (jeśli dotyczy) zasobów w obszarze roboczym. Na przykład z tą rolą można utworzyć eksperyment, Utwórz lub Dołącz klastra obliczeniowego przesłać modelu wykonywania, rejestrowanie i wdrażanie usługi sieci web.

- **Właściciel**
    
    Ta rola zapewnia pełny dostęp do obszaru roboczego, co umożliwia wyświetlanie, tworzenie, edytowanie lub usuwanie (jeśli dotyczy) zasobów w obszarze roboczym. Ponadto użytkownik może również Dodaj lub usuń użytkowników i zmień swoje przypisania roli.

## <a name="add-or-remove-users"></a>Dodawanie lub usuwanie użytkowników
Jeśli jesteś właścicielem obszaru roboczego, możesz dodać użytkowników do lub usuwanie użytkowników z obszaru roboczego, wybierając dowolną z następujących metod do wykonania tej akcji:
- [Interfejs użytkownika witryny Azure portal](/azure/role-based-access-control/role-assignments-portal)
- [Program PowerShell](/azure/role-based-access-control/role-assignments-powershell)
- [Interfejs wiersza polecenia platformy Azure](/azure/role-based-access-control/role-assignments-cli)
- [Interfejs API REST](/azure/role-based-access-control/role-assignments-rest)
- [Szablony usługi Azure Resource Management](/azure/role-based-access-control/role-assignments-template).

Alternatywnie Jeśli zainstalowano [interfejsu wiersza polecenia usługi Azure Machine Learning](reference-azure-machine-learning-cli.md), umożliwiają wygodne polecenia interfejsu wiersza polecenia można dodać użytkownika do obszaru roboczego.

```azurecli-interactive 
az ml workspace share -n <workspace_name> -g <resource_group_name> --role <role_name> --user <user_corp_email_address>
```

Należy pamiętać, że `user` pole jest adres e-mail istniejącego użytkownika w tej samej usługi Azure Active Directory lokalizacji nadrzędnej subskrypcji platformy Azure, w obszarze roboczym. Poniżej przedstawiono przykładowy sposób użycia tego polecenia:

```azurecli-interactive 
az ml workspace share -n my_workspace -g my_resource_group --role Contributor --user jdoe@contoson.com
```

## <a name="create-custom-role"></a>Utwórz rolę niestandardową
Wbudowane role są niewystarczające do własnych potrzeb, można również utworzyć niestandardowe role. Role niestandardowe mogą mieć odczytu, zapisu lub usuwania uprawnień obszaru roboczego i zasobu obliczeniowego, w tym obszarze roboczym. I możesz udostępnić rolę w poziomie określonego obszaru roboczego, na poziomie grupy określonego zasobu lub na poziomie określonej subskrypcji. 

> [!NOTE]
> Aby można było utworzyć niestandardowe role w ramach tego zasobu, musisz być właścicielem zasobu na tym samym poziomie.

Aby utworzyć rolę niestandardową, najpierw należy utworzyć plik JSON definicji roli, określając uprawnienia oraz zakres, który chcesz, aby dla roli. Na przykład poniżej jest plik definicji roli dla roli niestandardowej o nazwie "Analityk danych" w zakresie na poziomie określonego obszaru roboczego.

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

Tę rolę można zrobić wszystko w obszarze roboczym, z wyjątkiem następujących czynności:
- Go nie można utworzyć lub zaktualizować zasobów obliczeniowych.
- Nie można go usunąć zasób obliczeniowy.
- Go nie można dodać, usunąć użytkownika lub zmienić przypisania roli użytkownika.
- Nie można go usunąć obszar roboczy.

Aby wdrożyć tę rolę niestandardową, użyj następującego polecenia wiersza polecenia platformy Azure:

```azurecli-interactive 
az role definition create --role-definition data_scientist_role.json
```

Po wdrożeniu tej roli w obszarze roboczym określonego staje się dostępna. Teraz możesz dodać użytkowników i tę rolę można przypisać do nich w witrynie Azure portal. Lub można dodać użytkownika przy użyciu tej roli `az ml workspace share` interfejsu wiersza polecenia:

```azurecli-interactive
az ml workspace share -n my_workspace -g my_resource_group --role "Data Scientist" --user jdoe@contoson.com
```

Możesz również zmienić `AssignableScopes` pola, aby ustawić zakres tę rolę niestandardową, na poziomie subskrypcji względem poziomu grupy zasobów lub na poziomie określonego obszaru roboczego (jak pokazano).

Aby uzyskać więcej informacji jak niestandardowe role działają na platformie Azure, zapoznaj się [w tym dokumencie](/azure/role-based-access-control/custom-roles).

## <a name="next-steps"></a>Kolejne kroki

Postępuj zgodnie z samouczka kompleksowo przedstawiono sposób tworzenia, uczenia i wdrażania modeli przy użyciu usługi Azure Machine Learning za pomocą obszaru roboczego.

> [!div class="nextstepaction"]
> [Samouczek: Szkolenie modeli](tutorial-train-models-with-aml.md)
