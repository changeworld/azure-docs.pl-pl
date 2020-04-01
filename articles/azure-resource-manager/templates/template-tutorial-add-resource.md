---
title: Samouczek - Dodawanie zasobu do szablonu
description: W tym artykule opisano kroki tworzenia pierwszego szablonu usługi Azure Resource Manager. Dowiesz się więcej o składni pliku szablonu i o tym, jak wdrożyć konto magazynu.
author: mumian
ms.date: 03/27/2020
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: dcdbbb325e6589669abe6cf3d25ac5191e29118b
ms.sourcegitcommit: 27bbda320225c2c2a43ac370b604432679a6a7c0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/31/2020
ms.locfileid: "80411734"
---
# <a name="tutorial-add-a-resource-to-your-arm-template"></a>Samouczek: Dodawanie zasobu do szablonu ARM

W [poprzednim samouczku](template-tutorial-create-first-template.md)opisano, jak utworzyć pusty szablon i wdrożyć go. Teraz możesz przystąpić do wdrażania rzeczywistego zasobu. W tym samouczku należy dodać konto magazynu. Trwa około **9 minut,** aby zakończyć ten samouczek.

## <a name="prerequisites"></a>Wymagania wstępne

Zalecamy ukończenie [samouczka wprowadzającego na temat szablonów,](template-tutorial-create-first-template.md)ale nie jest to wymagane.

Musisz mieć program Visual Studio Code z rozszerzeniem Narzędzia Menedżera zasobów i azure powershell lub interfejsu wiersza polecenia platformy Azure. Aby uzyskać więcej informacji, zobacz [narzędzia szablonów](template-tutorial-create-first-template.md#get-tools).

## <a name="add-resource"></a>Dodawanie zasobu

Aby dodać definicję konta magazynu do istniejącego szablonu, spójrz na wyróżniony JSON w poniższym przykładzie. Zamiast próbować kopiować sekcje szablonu, skopiuj cały plik i zastąp szablon jego zawartością.

Zastąp **{provide-unique-name}** (łącznie z nawiasami klamrowymi) unikatową nazwą konta magazynu.

> [!IMPORTANT]
> Nazwa konta magazynu musi być unikatowa w obrębie platformy Azure. Nazwa musi zawierać tylko małe litery lub cyfry. Nie może być dłuższy niż 24 znaki. Możesz wypróbować wzorzec nazewnictwa, taki jak używanie **store1** jako prefiksu, a następnie dodanie inicjałów i dzisiejszej daty. Na przykład używana nazwa może wyglądać jak **store1abc09092019**.

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/add-storage/azuredeploy.json" range="1-19" highlight="5-17":::

Zgadywanie unikatowej nazwy konta magazynu nie jest łatwe i nie działa dobrze w przypadku automatyzacji dużych wdrożeń. W dalszej części tej serii samouczków użyjesz funkcji szablonu, które ułatwiają tworzenie unikatowej nazwy.

## <a name="resource-properties"></a>Właściwości zasobu

Możesz się zastanawiać, jak znaleźć właściwości do użycia dla każdego typu zasobu. Odwołanie do [szablonu ARM](/azure/templates/) służy do znajdowania typów zasobów, które chcesz wdrożyć.

Każdy wdrożony zasób ma co najmniej następujące trzy właściwości:

- **typ**: Typ zasobu. Ta wartość jest kombinacją obszaru nazw dostawcy zasobów i typu zasobu (na przykład Microsoft.Storage/storageAccounts).
- **apiVersion**: Wersja interfejsu API REST do użycia do tworzenia zasobu. Każdy dostawca zasobów opublikował własne wersje interfejsu API, więc ta wartość jest specyficzna dla typu.
- **nazwa**: Nazwa zasobu.

Większość zasobów ma również właściwość **lokalizacji,** która ustawia region, w którym zasób jest wdrażany.

Inne właściwości różnią się w zależności od typu zasobu i wersji interfejsu API. Ważne jest, aby zrozumieć połączenie między wersją interfejsu API a dostępnymi właściwościami, więc przejdźmy do bardziej szczegółowych informacji.

W tym samouczku dodano konto magazynu do szablonu. Możesz zobaczyć, że wersja interfejsu API na [storageAccounts 2019-04-01](/azure/templates/microsoft.storage/2019-04-01/storageaccounts). Należy zauważyć, że nie dodano wszystkie właściwości do szablonu. Wiele właściwości są opcjonalne. Dostawca zasobów magazynu Microsoft.Storage może zwolnić nową wersję interfejsu API, ale wdrażana wersja nie musi ulec zmianie. Możesz nadal korzystać z tej wersji i wiedzieć, że wyniki wdrożenia będą spójne.

Jeśli wyświetlisz starszą wersję interfejsu API, taką jak [storageAccounts 2016-05-01](/azure/templates/microsoft.storage/2016-05-01/storageaccounts), zobaczysz, że dostępny jest mniejszy zestaw właściwości.

Jeśli zdecydujesz się zmienić wersję interfejsu API dla zasobu, upewnij się, że oceniasz właściwości dla tej wersji i odpowiednio dostosuj szablon.

## <a name="deploy-template"></a>Wdrażanie szablonu

Można wdrożyć szablon, aby utworzyć konto magazynu. Nadaj wdrożeniu inną nazwę, aby można było łatwo znaleźć ją w historii.

Jeśli grupa zasobów nie została utworzona, zobacz [Tworzenie grupy zasobów](template-tutorial-create-first-template.md#create-resource-group). W przykładzie przyjęto założenie, że ustawiono **zmienną templateFile** na ścieżkę do pliku szablonu, jak pokazano w [pierwszym samouczku](template-tutorial-create-first-template.md#deploy-template).

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name addstorage `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $templateFile
```

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Aby uruchomić to polecenie wdrażania, musisz mieć [najnowszą wersję](/cli/azure/install-azure-cli) interfejsu wiersza polecenia platformy Azure.

```azurecli
az deployment group create \
  --name addstorage \
  --resource-group myResourceGroup \
  --template-file $templateFile
```

---

> [!NOTE]
> Jeśli wdrożenie nie powiodło się, użyj przełącznika **debugowania** z poleceniem wdrażania, aby wyświetlić dzienniki debugowania.  Można również użyć pełnego **przełącznika,** aby wyświetlić pełne dzienniki debugowania.

Dwa możliwe błędy wdrażania, które mogą wystąpić:

- Błąd: Code=AccountNameInvalid; Message={provide-unique-name} nie jest prawidłową nazwą konta magazynu. Nazwa konta magazynu musi mieć długość od 3 do 24 znaków i używać tylko cyfr i liter.

    W szablonie zastąp **{provide-unique-name}** unikatową nazwą konta magazynu.  Zobacz [Dodawanie zasobu](#add-resource).

- Błąd: Code=StorageAccountAlreadyTaken; Message=Konto magazynu o nazwie store1abc09092019 jest już zrobione.

    W szablonie spróbuj użyć innej nazwy konta magazynu.

To wdrożenie trwa dłużej niż puste wdrożenie szablonu, ponieważ konto magazynu jest tworzony. Może to potrwać około minuty, ale zwykle jest szybsze.

## <a name="verify-deployment"></a>Weryfikowanie wdrożenia

Wdrożenie można zweryfikować, eksplorując grupę zasobów z witryny Azure portal.

1. Zaloguj się do [Portalu Azure](https://portal.azure.com).
1. W menu po lewej stronie wybierz pozycję **Grupy zasobów**.
1. Wybierz grupę zasobów, do której została wdrożona.
1. Widać, że konto magazynu zostało wdrożone.
1. Należy zauważyć, że etykieta wdrożenia teraz mówi: **Wdrożenia: 2 Powiodło się**.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli przechodzisz do następnego samouczka, nie musisz usuwać grupy zasobów.

Jeśli zatrzymujesz się teraz, możesz wyczyścić zasoby wdrożone przez usunięcie grupy zasobów.

1. W witrynie Azure portal wybierz **grupę zasobów** z lewego menu.
2. Wprowadź nazwę grupy zasobów w polu **Filtruj według nazwy**.
3. Wybierz nazwę grupy zasobów.
4. Wybierz **pozycję Usuń grupę zasobów** z górnego menu.

## <a name="next-steps"></a>Następne kroki

Utworzono prosty szablon do wdrożenia konta magazynu platformy Azure.  W późniejszych samouczkach dowiesz się, jak dodawać parametry, zmienne, zasoby i dane wyjściowe do szablonu. Te funkcje są blokami konstrukcyjnymi dla znacznie bardziej złożonych szablonów.

> [!div class="nextstepaction"]
> [Dodawanie parametrów](template-tutorial-add-parameters.md)