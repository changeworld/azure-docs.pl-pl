---
title: Zarządzanie rolami w obszarze roboczym
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak uzyskać dostęp do obszaru roboczego usługi Azure Machine Learning przy użyciu kontroli dostępu opartej na rolach (RBAC).
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: larryfr
author: Blackmist
ms.date: 03/06/2020
ms.custom: seodec18
ms.openlocfilehash: 127a0a2b7f7573db91df9347169e90de3e14c4c9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79270097"
---
# <a name="manage-access-to-an-azure-machine-learning-workspace"></a>Zarządzanie dostępem do obszaru roboczego usługi Azure Machine Learning
[!INCLUDE [aml-applies-to-basic-enterprise-sku](../../includes/aml-applies-to-basic-enterprise-sku.md)]

W tym artykule dowiesz się, jak zarządzać dostępem do obszaru roboczego usługi Azure Machine Learning. [Kontrola dostępu oparta na rolach (RBAC)](/azure/role-based-access-control/overview) służy do zarządzania dostępem do zasobów platformy Azure. Użytkownikom usługi Azure Active Directory są przypisywane określone role, które udzielają dostępu do zasobów. Platforma Azure zapewnia zarówno wbudowane role, jak i możliwość tworzenia ról niestandardowych.

## <a name="default-roles"></a>Role domyślne

Obszar roboczy usługi Azure Machine Learning jest zasobem platformy Azure. Podobnie jak inne zasoby platformy Azure, po utworzeniu nowego obszaru roboczego usługi Azure Machine Learning jest wyposażony w trzy role domyślne. Można dodać użytkowników do obszaru roboczego i przypisać ich do jednej z tych wbudowanych ról.

| Rola | Poziom dostępu |
| --- | --- |
| **Czytelnik** | Akcje tylko do odczytu w obszarze roboczym. Czytelnicy mogą wyświetlać i wyświetlać zasoby w obszarze roboczym, ale nie mogą tworzyć ani aktualizować tych zasobów. |
| **Współautor** | Umożliwia wyświetlanie, tworzenie, edytowanie lub usuwanie (w stosownych przypadkach) zasobów w obszarze roboczym. Na przykład współautorzy mogą utworzyć eksperyment, utworzyć lub dołączyć klaster obliczeniowy, przesłać przebieg i wdrożyć usługę sieci web. |
| **Właściciel** | Pełny dostęp do obszaru roboczego, w tym możliwość wyświetlania, tworzenia, edytowania lub usuwania (w stosownych przypadkach) zasobów w obszarze roboczym. Ponadto można zmienić przypisania ról. |

> [!IMPORTANT]
> Dostęp do ról może być ograniczony do wielu poziomów na platformie Azure. Na przykład osoba z dostępem właściciela do obszaru roboczego może nie mieć dostępu właściciela do grupy zasobów zawierającej obszar roboczy. Aby uzyskać więcej informacji, zobacz [Jak działa RBAC](/azure/role-based-access-control/overview#how-rbac-works).

Aby uzyskać więcej informacji na temat określonych ról wbudowanych, zobacz [Wbudowane role platformy Azure.](/azure/role-based-access-control/built-in-roles)

## <a name="manage-workspace-access"></a>Zarządzanie dostępem do obszaru roboczego

Jeśli jesteś właścicielem obszaru roboczego, możesz dodawać i usuwać role obszaru roboczego. Można również przypisać role do użytkowników. Aby dowiedzieć się, jak zarządzać dostępem, skorzystaj z następujących łączy:
- [Interfejs użytkownika witryny portalu azure](/azure/role-based-access-control/role-assignments-portal)
- [Powershell](/azure/role-based-access-control/role-assignments-powershell)
- [Interfejs wiersza polecenia platformy Azure](/azure/role-based-access-control/role-assignments-cli)
- [INTERFEJS API ODPOCZYNKU](/azure/role-based-access-control/role-assignments-rest)
- [Szablony usługi Azure Resource Manager](/azure/role-based-access-control/role-assignments-template)

Jeśli zainstalowano [interfejsu wiersza polecenia uczenia maszynowego usługi Azure,](reference-azure-machine-learning-cli.md)można również użyć polecenia interfejsu wiersza polecenia, aby przypisać role do użytkowników.

```azurecli-interactive 
az ml workspace share -w <workspace_name> -g <resource_group_name> --role <role_name> --user <user_corp_email_address>
```

To `user` pole jest adresem e-mail istniejącego użytkownika w wystąpieniu usługi Azure Active Directory, w którym znajduje się nadrzędna subskrypcja obszaru roboczego. Oto przykład użycia tego polecenia:

```azurecli-interactive 
az ml workspace share -w my_workspace -g my_resource_group --role Contributor --user jdoe@contoson.com
```

## <a name="create-custom-role"></a>Tworzenie roli niestandardowej

Jeśli wbudowane role są niewystarczające, można utworzyć role niestandardowe. Role niestandardowe mogły mieć uprawnienia do odczytu, zapisu, usuwania i obliczania zasobów w tym obszarze roboczym. Można udostępnić rolę na określonym poziomie obszaru roboczego, na poziomie określonej grupy zasobów lub na określonym poziomie subskrypcji.

> [!NOTE]
> Aby utworzyć niestandardowe role w tym zasobie, musisz być właścicielem zasobu na tym poziomie.

Aby utworzyć rolę niestandardową, należy najpierw skonstruować plik JSON definicji roli, który określa uprawnienie i zakres roli. Poniższy przykład definiuje niestandardową rolę o nazwie "Data Scientist" o zakresie na określonym poziomie obszaru roboczego:

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

Można zmienić `AssignableScopes` to pole, aby ustawić zakres tej roli niestandardowej na poziomie subskrypcji, na poziomie grupy zasobów lub określonym poziomie obszaru roboczego.

Ta rola niestandardowa może wykonywać wszystkie czynności w obszarze roboczym, z wyjątkiem następujących akcji:

- Nie można utworzyć ani zaktualizować zasobu obliczeniowego.
- Nie można usunąć zasobu obliczeniowego.
- Nie można dodawać, usuwać ani zmieniać przypisań ról.
- Nie można usunąć obszaru roboczego.

Aby wdrożyć tę rolę niestandardową, należy użyć następującego polecenia interfejsu wiersza polecenia platformy Azure:

```azurecli-interactive 
az role definition create --role-definition data_scientist_role.json
```

Po wdrożeniu ta rola staje się dostępna w określonym obszarze roboczym. Teraz możesz dodać i przypisać tę rolę w witrynie Azure portal. Można też przypisać tę rolę do użytkownika `az ml workspace share` za pomocą polecenia CLI:

```azurecli-interactive
az ml workspace share -w my_workspace -g my_resource_group --role "Data Scientist" --user jdoe@contoson.com
```

Aby uzyskać więcej informacji na temat ról niestandardowych, zobacz [Role niestandardowe dla zasobów platformy Azure](/azure/role-based-access-control/custom-roles).

Aby uzyskać więcej informacji na temat operacji (akcji) nadanych rolami niestandardowymi, zobacz [Operacje dostawcy zasobów](/azure/role-based-access-control/resource-provider-operations#microsoftmachinelearningservices).


## <a name="frequently-asked-questions"></a>Często zadawane pytania


### <a name="q-what-are-the-permissions-needed-to-perform-various-actions-in-the-azure-machine-learning-service"></a>PYTANIE: Jakie uprawnienia są potrzebne do wykonywania różnych akcji w usłudze Azure Machine Learning?

Poniższa tabela zawiera podsumowanie działań usługi Azure Machine Learning i uprawnień wymaganych do ich wykonania w jak najmniejszym zakresie. Na przykład, jeśli działanie można wykonać z zakresu obszaru roboczego (kolumna 4), a następnie wszystkie wyższe zakres z tym uprawnieniem będzie również działać automatycznie. Wszystkie ścieżki w tej tabeli `Microsoft.MachineLearningServices/`są **ścieżkami względnymi** do .

| Działanie | Zakres na poziomie subskrypcji | Zakres na poziomie grupy zasobów | Zakres na poziomie obszaru roboczego |
|---|---|---|---|
| Tworzenie nowego obszaru roboczego | Niewymagane | Właściciel lub współautor | Nie dotyczy (staje się właścicielem lub dziedziczy rolę wyższego zakresu po utworzeniu) |
| Tworzenie nowego klastra obliczeniowego | Niewymagane | Niewymagane | Rola właściciela, współautora lub niestandardowa umożliwiająca:`workspaces/computes/write` |
| Tworzenie nowej maszyny Wirtualnej notesu | Niewymagane | Właściciel lub współautor | Niemożliwe |
| Tworzenie nowego wystąpienia obliczeń | Niewymagane | Niewymagane | Rola właściciela, współautora lub niestandardowa umożliwiająca:`workspaces/computes/write` |
| Działanie płaszczyzny danych, takie jak przesyłanie przebiegu, uzyskiwanie dostępu do danych, wdrażanie modelu lub publikowanie potoku | Niewymagane | Niewymagane | Rola właściciela, współautora lub niestandardowa umożliwiająca:`workspaces/*/write` <br/> Należy również pamiętać, że potrzebny jest również magazyn danych zarejestrowany w obszarze roboczym, aby umożliwić msi dostęp do danych na koncie magazynu. |


### <a name="q-how-do-i-list-all-the-custom-roles-in-my-subscription"></a>PYTANIE: Jak wyświetlić listę wszystkich ról niestandardowych w mojej subskrypcji?

W wierszu polecenia platformy Azure uruchom następujące polecenie.

```azurecli-interactive
az role definition list --subscription <sub-id> --custom-role-only true
```

### <a name="q-how-do-i-find-the-role-definition-for-a-role-in-my-subscription"></a>PYTANIE: Jak znaleźć definicję roli roli w mojej subskrypcji?

W wierszu polecenia platformy Azure uruchom następujące polecenie. Należy `<role-name>` zauważyć, że powinien być w tym samym formacie zwrócone przez polecenie powyżej.

```azurecli-interactive
az role definition list -n <role-name> --subscription <sub-id>
```

### <a name="q-how-do-i-update-a-role-definition"></a>PYTANIE: Jak zaktualizować definicję roli?

W wierszu polecenia platformy Azure uruchom następujące polecenie.

```azurecli-interactive
az role definition update --role-definition update_def.json --subscription <sub-id>
```

Należy zauważyć, że musisz mieć uprawnienia do całego zakresu nowej definicji roli. Na przykład, jeśli ta nowa rola ma zakres w trzech subskrypcji, należy mieć uprawnienia do wszystkich trzech subskrypcji. 

> [!NOTE]
> Aktualizacje ról może potrwać od 15 minut do godziny, aby zastosować we wszystkich przydziałów ról w tym zakresie.
### <a name="q-can-i-define-a-role-that-prevents-updating-the-workspace-edition"></a>PYTANIE: Czy można zdefiniować rolę, która uniemożliwia aktualizowanie wersji obszaru roboczego? 

Tak, można zdefiniować rolę, która uniemożliwia aktualizowanie wersji obszaru roboczego. Ponieważ aktualizacja obszaru roboczego jest wywołaniem PATCH na obiekcie obszaru roboczego, można to zrobić, umieszczając następującą akcję w `"NotActions"` tablicy w definicji JSON: 

`"Microsoft.MachineLearningServices/workspaces/write"`

### <a name="q-what-permissions-are-needed-to-perform-quota-operations-in-a-workspace"></a>PYTANIE: Jakie uprawnienia są potrzebne do wykonywania operacji przydziału w obszarze roboczym? 

Aby wykonać dowolną operację związaną z przydziałem w obszarze roboczym, potrzebne są uprawnienia na poziomie subskrypcji. Oznacza to, że ustawienie przydziału na poziomie subskrypcji lub przydziału na poziomie obszaru roboczego dla zarządzanych zasobów obliczeniowych może się zdarzyć tylko wtedy, gdy masz uprawnienia do zapisu w zakresie subskrypcji. 


## <a name="next-steps"></a>Następne kroki

- [Przegląd zabezpieczeń przedsiębiorstwa](concept-enterprise-security.md)
- [Bezpieczne uruchamianie eksperymentów i wnioskowanie/zdobywanie punktów wewnątrz sieci wirtualnej](how-to-enable-virtual-network.md)
- [Samouczek: Modele pociągów](tutorial-train-models-with-aml.md)
- [Operacje dostawcy zasobów](/azure/role-based-access-control/resource-provider-operations#microsoftmachinelearningservices)
