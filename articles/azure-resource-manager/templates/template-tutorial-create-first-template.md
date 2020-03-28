---
title: Samouczek — tworzenie & wdrażania szablonu
description: Utwórz swój pierwszy szablon usługi Azure Resource Manager. W samouczku dowiesz się o składni pliku szablonu i jak wdrożyć konto magazynu.
author: mumian
ms.date: 03/27/2020
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: ace76b9a13f44c14e348a0338ca01dd6b3948ce3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80369938"
---
# <a name="tutorial-create-and-deploy-your-first-arm-template"></a>Samouczek: Tworzenie i wdrażanie pierwszego szablonu ARM

W tym samouczku przedstawiono szablony usługi Azure Resource Manager (ARM). Pokazuje, jak utworzyć szablon startowy i wdrożyć go na platformie Azure. Dowiesz się o strukturze szablonu i narzędziach potrzebnych do pracy z szablonami. Ukończenie tego samouczka zajmuje około **12 minut,** ale rzeczywisty czas będzie się różnić w zależności od liczby narzędzi potrzebnych do zainstalowania.

Ten samouczek jest pierwszym z serii. W miarę postępów w serii, można modyfikować szablon początkowy krok po kroku, dopóki nie zbadasz wszystkich podstawowych części szablonu ARM. Te elementy są blokami konstrukcyjnymi dla szablonów znacznie bardziej złożonych. Mamy nadzieję, że pod koniec serii masz pewność, że tworzysz własne szablony i przygotujesz do automatyzacji wdrożeń za pomocą szablonów.

Jeśli chcesz dowiedzieć się więcej o korzyściach płynących z używania szablonów i o tym, dlaczego należy zautomatyzować wdrażanie za pomocą szablonów, zobacz [szablony usługi Azure Resource Manager](overview.md).

Jeśli nie masz subskrypcji platformy Azure, [utwórz bezpłatne konto](https://azure.microsoft.com/free/) przed rozpoczęciem.

## <a name="get-tools"></a>Uzyskaj narzędzia

Zacznijmy od upewnienia się, że masz narzędzia potrzebne do tworzenia i wdrażania szablonów.

### <a name="editor"></a>Edytor

Szablony są plikami JSON. Aby utworzyć szablony, potrzebujesz dobrego edytora JSON. Zalecamy program Visual Studio Code z rozszerzeniem Narzędzia Menedżera zasobów. Jeśli chcesz zainstalować te narzędzia, zobacz [Tworzenie szablonów ARM za pomocą programu Visual Studio Code](use-vs-code-to-create-template.md).

### <a name="command-line-deployment"></a>Wdrażanie wiersza polecenia

Aby wdrożyć szablon, należy również wdrożyć szablon w programie Azure PowerShell lub platformie Azure CLI. Jeśli używasz interfejsu wiersza polecenia platformy Azure, musisz mieć najnowszą wersję. Instrukcje instalacji można znaleźć na:

- [Instalowanie programu Azure PowerShell](/powershell/azure/install-az-ps)
- [Instalowanie interfejsu wiersza polecenia platformy Azure w systemie Windows](/cli/azure/install-azure-cli-windows)
- [Instalowanie interfejsu wiersza polecenia platformy Azure w systemie Linux](/cli/azure/install-azure-cli-linux)

Po zainstalowaniu interfejsu wiersza polecenia programu Azure PowerShell lub interfejsu wiersza polecenia platformy Azure upewnij się, że logujesz się po raz pierwszy. Aby uzyskać pomoc, zobacz [Logowanie — program PowerShell](/powershell/azure/install-az-ps#sign-in) lub [Logowanie — Azure CLI](/cli/azure/get-started-with-azure-cli#sign-in).

W porządku, jesteś gotowy, aby rozpocząć naukę o szablonach.

## <a name="create-your-first-template"></a>Tworzenie pierwszego szablonu

1. Otwórz kod programu Visual Studio z zainstalowanym rozszerzeniem Narzędzia Menedżera zasobów.
1. Z menu **Plik** wybierz polecenie **Nowy plik,** aby utworzyć nowy plik.
1. Z menu **Plik** wybierz polecenie **Zapisz jako**.
1. Nazwij plik **azuredeploy** i wybierz rozszerzenie pliku **JSON.** Pełna nazwa pliku **azuredeploy.json**.
1. Zapisz plik na stacji roboczej. Wybierz ścieżkę, która jest łatwa do zapamiętania, ponieważ po wdrożeniu szablonu udostępnisz tę ścieżkę.
1. Skopiuj i wklej do pliku następujący JSON:

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "resources": []
    }
    ```

    Oto jak wygląda twoje środowisko vs code:

    ![Szablon szablonu programu Visual Studio szablonu Menedżera zasobów pierwszy szablon](./media/template-tutorial-create-first-template/resource-manager-visual-studio-code-first-template.png)

    Ten szablon nie wdraża żadnych zasobów. Zaczynamy od pustego szablonu, dzięki czemu możesz zapoznać się z krokami wdrażania szablonu, minimalizując jednocześnie ryzyko, że coś pójdzie nie tak.

    Plik JSON zawiera następujące elementy:

    - **$schema**: Określa lokalizację pliku schematu JSON. Plik schematu opisuje właściwości, które są dostępne w szablonie. Na przykład schemat definiuje **zasoby** jako jedną z prawidłowych właściwości szablonu. Nie martw się, że datą dla schematu jest 2019-04-01. Ta wersja schematu jest aktualna i zawiera wszystkie najnowsze funkcje. Data schematu nie została zmieniona, ponieważ od czasu jego wprowadzenia nie wprowadzono żadnych przełomowych zmian.
    - **contentVersion**: Określa wersję szablonu (na przykład 1.0.0.0). Można podać dowolną wartość dla tego elementu. Ta wartość służy do dokumentowania istotnych zmian w szablonie. Podczas wdrażania zasobów przy użyciu szablonu, ta wartość może służyć do upewnij się, że używany jest odpowiedni szablon.
    - **zasoby**: Zawiera zasoby, które chcesz wdrożyć lub zaktualizować. Obecnie jest pusta, ale później dodasz zasoby.

1. Zapisz plik.

Gratulacje, utworzono pierwszy szablon.

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Aby rozpocząć pracę z platformą Azure PowerShell/Azure CLI, zaloguj się przy użyciu poświadczeń platformy Azure.

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

```azurepowershell
Connect-AzAccount
```

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

```azurecli
az login
```

---
## <a name="create-resource-group"></a>Tworzenie grupy zasobów

Podczas wdrażania szablonu należy określić grupę zasobów, która będzie zawierać zasoby. Przed uruchomieniem polecenia wdrażania utwórz grupę zasobów za pomocą interfejsu wiersza polecenia platformy Azure lub programu Azure PowerShell. Wybierz karty w poniższej sekcji kodu, aby wybrać między platformą Azure PowerShell i interfejsu wiersza polecenia platformy Azure. Przykłady interfejsu wiersza polecenia w tym artykule są zapisywane dla powłoki Bash.

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroup `
  -Name myResourceGroup `
  -Location "Central US"
```

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

```azurecli
az group create \
  --name myResourceGroup \
  --location "Central US"
```

---

## <a name="deploy-template"></a>Wdrażanie szablonu

Aby wdrożyć szablon, użyj interfejsu wiersza polecenia platformy Azure lub programu Azure PowerShell. Użyj utworzonej grupy zasobów. Nadaj nazwę wdrożeniu, aby można było łatwo zidentyfikować ją w historii wdrażania. Dla wygody należy również utworzyć zmienną, która przechowuje ścieżkę do pliku szablonu. Ta zmienna ułatwia uruchamianie poleceń wdrażania, ponieważ nie trzeba ponownie wpisywać ścieżkę przy każdym wdrożeniu.

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

```azurepowershell
$templateFile = "{provide-the-path-to-the-template-file}"
New-AzResourceGroupDeployment `
  -Name blanktemplate `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $templateFile
```

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Aby uruchomić to polecenie wdrażania, musisz mieć [najnowszą wersję](/cli/azure/install-azure-cli) interfejsu wiersza polecenia platformy Azure.

```azurecli
templateFile="{provide-the-path-to-the-template-file}"
az deployment group create \
  --name blanktemplate \
  --resource-group myResourceGroup \
  --template-file $templateFile
```

---

Polecenie wdrażania zwraca wyniki. Poszukaj, `ProvisioningState` czy wdrożenie zakończyło się pomyślnie.

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

![Stan inicjowania obsługi administracyjnej wdrożenia programu PowerShell](./media/template-tutorial-create-first-template/resource-manager-deployment-provisioningstate.png)

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

![Stan inicjowania obsługi administracyjnej wdrożenia interfejsu wiersza polecenia platformy Azure](./media/template-tutorial-create-first-template/azure-cli-provisioning-state.png)

---

## <a name="verify-deployment"></a>Weryfikowanie wdrożenia

Wdrożenie można zweryfikować, eksplorując grupę zasobów z witryny Azure portal.

1. Zaloguj się do [Portalu Azure](https://portal.azure.com).

1. W menu po lewej stronie wybierz pozycję **Grupy zasobów**.

1. Wybierz grupę zasobów wdrożoną w ostatniej procedurze. Domyślną nazwą jest **myResourceGroup**. Nie będzie widoczny żaden zasób wdrożony w grupie zasobów.

1. Uwaga w prawym górnym rogu przeglądu wyświetlany jest stan wdrożenia. Wybierz **1 Powiodło się**.

   ![Wyświetlanie stanu wdrożenia](./media/template-tutorial-create-first-template/deployment-status.png)

1. Zobaczysz historię wdrożenia dla grupy zasobów. Wybierz **blanktemplate**.

   ![Wybierz wdrożenie](./media/template-tutorial-create-first-template/select-from-deployment-history.png)

1. Pojawi się podsumowanie wdrożenia. W takim przypadku nie ma wiele do zobaczenia, ponieważ nie zostały wdrożone żadne zasoby. W dalszej części tej serii może okazać się przydatne do przejrzenia podsumowania w historii wdrażania. Uwaga po lewej stronie można wyświetlić dane wejściowe, dane wyjściowe i szablon używany podczas wdrażania.

   ![Wyświetl podsumowanie wdrożenia](./media/template-tutorial-create-first-template/view-deployment-summary.png)

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli przechodzisz do następnego samouczka, nie musisz usuwać grupy zasobów.

Jeśli zatrzymujesz się teraz, możesz usunąć grupę zasobów.

1. W witrynie Azure portal wybierz **grupę zasobów** z lewego menu.
2. Wprowadź nazwę grupy zasobów w polu **Filtruj według nazwy**.
3. Wybierz nazwę grupy zasobów.
4. Wybierz **pozycję Usuń grupę zasobów** z górnego menu.

## <a name="next-steps"></a>Następne kroki

Utworzono prosty szablon do wdrożenia na platformie Azure. W następnym samouczku dodasz konto magazynu do szablonu i wdrożysz je w grupie zasobów.

> [!div class="nextstepaction"]
> [Dodawanie zasobu](template-tutorial-add-resource.md)
