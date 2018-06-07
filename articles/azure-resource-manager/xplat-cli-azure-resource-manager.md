---
title: Zarządzanie zasobami za pomocą wiersza polecenia platformy Azure | Dokumentacja firmy Microsoft
description: Umożliwia zarządzanie zasobami platformy Azure i grup Azure interfejsu wiersza polecenia (CLI)
editor: ''
manager: timlt
documentationcenter: ''
author: tfitzmac
services: azure-resource-manager
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: vm-multiple
ms.devlang: na
ms.topic: conceptual
ms.date: 10/06/2017
ms.author: tomfitz
ms.openlocfilehash: 4111d1d990f3e9efb78fae0476b027d5bae013af
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34603250"
---
# <a name="use-the-azure-cli-to-manage-azure-resources-and-resource-groups"></a>Użyj wiersza polecenia platformy Azure do zarządzania zasobami Azure i grup zasobów

W tym artykule Dowiedz się jak zarządzać rozwiązań z wiersza polecenia platformy Azure i usługi Azure Resource Manager. Jeśli nie masz doświadczenia z usługą Resource Manager, zobacz [omówienie Resource Manager](resource-group-overview.md). Ten artykuł dotyczy zadań zarządzania. Wykonasz następujące zadania:

1. Tworzenie grupy zasobów
2. Dodaj zasób do grupy zasobów
3. Dodaj tag do zasobu
4. Wykonywanie zapytań względem zasobów na podstawie nazw lub wartości tagów
5. Zastosuj i Usuń blokada na zasób
6. Usuń grupę zasobów

W tym artykule nie pokazuje sposób wdrażania szablonu usługi Resource Manager do subskrypcji. Informacje, zobacz [wdrożenie zasobów z szablonami usługi Resource Manager i interfejsu wiersza polecenia Azure](resource-group-template-deploy-cli.md).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Aby zainstalować i korzystać z interfejsu wiersza polecenia lokalnie, zobacz [zainstalować Azure CLI 2.0](/cli/azure/install-azure-cli).

## <a name="set-subscription"></a>Zestaw subskrypcji

Jeśli masz więcej niż jedną subskrypcję, możesz przełączyć się do innej subskrypcji. Po pierwsze Zobacz wszystkie subskrypcje dla swojego konta.

```azurecli-interactive
az account list
```

Zwraca listę włączonych i wyłączonych subskrypcji.

```json
[
  {
    "cloudName": "AzureCloud",
    "id": "<guid>",
    "isDefault": true,
    "name": "Example Subscription One",
    "registeredProviders": [],
    "state": "Enabled",
    "tenantId": "<guid>",
    "user": {
      "name": "example@contoso.org",
      "type": "user"
    }
  },
  ...
]
```

Zwróć uwagę, że jedna subskrypcja jest oznaczony jako domyślny. Ta subskrypcja jest bieżący kontekst dla operacji. Aby przełączyć się do innej subskrypcji, należy podać nazwę subskrypcji z **skonfigurowane konto az** polecenia.

```azurecli-interactive
az account set -s "Example Subscription Two"
```

Aby wyświetlić bieżący kontekst subskrypcji, należy użyć **Pokaż konto az** bez parametru:

```azurecli-interactive
az account show
```

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Przed wdrożeniem żadnych zasobów w Twojej subskrypcji, należy utworzyć grupę zasobów, która będzie zawierać zasoby.

Aby utworzyć grupę zasobów, użyj polecenia **az group create**. Używa polecenia **nazwa** parametr, aby określić nazwę grupy zasobów i **lokalizacji** parametru w celu określenia lokalizacji.

```azurecli-interactive
az group create --name TestRG1 --location "South Central US"
```

Dane wyjściowe znajduje się w następującym formacie:

```json
{
  "id": "/subscriptions/<subscription-id>/resourceGroups/TestRG1",
  "location": "southcentralus",
  "managedBy": null,
  "name": "TestRG1",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "tags": null
}
```

Jeśli chcesz później pobrać grupy zasobów, użyj następującego polecenia:

```azurecli-interactive
az group show --name TestRG1
```

Aby uzyskać wszystkie grupy zasobów w ramach subskrypcji, należy użyć:

```azurecli-interactive
az group list
```

## <a name="add-resources-to-a-resource-group"></a>Dodaj zasoby do grupy zasobów

Aby dodać zasobu do grupy zasobów, można użyć **tworzenie zasobu az** polecenia lub polecenia specyficzne dla typu zasobu tworzysz (takich jak **Tworzenie konta magazynu az**). Możesz może ułatwić polecenia, które są specyficzne dla typu zasobu, ponieważ uwzględnia on również parametry dla właściwości, które są potrzebne dla nowego zasobu. Do używania **tworzenie zasobu az**, musisz znać wszystkie właściwości można ustawić bez konieczności podawania dla nich.

Jednak dodawanie zasobów za pomocą skryptu może spowodować przyszłych pomyłek, ponieważ nowy zasób nie istnieje w szablonie usługi Resource Manager. Szablony umożliwiają niezawodne i wielokrotnego wdrażania rozwiązania.

Poniższe polecenie tworzy konto magazynu. Zamiast nazwy pokazano w przykładzie, Podaj unikatową nazwę konta magazynu. Nazwa musi mieć od 3 do 24 znaków i zawierać tylko cyfry i małe litery. Jeśli używasz nazwę pokazano w przykładzie, wystąpi błąd, ponieważ ta nazwa jest już używana.

```azurecli-interactive
az storage account create -n myuniquestorage -g TestRG1 -l westus --sku Standard_LRS
```

Jeśli potrzebujesz do odtworzenia tego zasobu, użyj następującego polecenia:

```azurecli-interactive
az storage account show --name myuniquestorage --resource-group TestRG1
```

## <a name="add-a-tag"></a>Dodaj tag

Znaczniki umożliwiają organizowania zasobów zgodnie z innej właściwości. Na przykład może mieć kilka zasobów w różnych grup zasobów należących do tego samego działu. Tag działu i wartości można zastosować do tych zasobów, aby oznaczyć je jako należące do tej samej kategorii. Alternatywnie można oznaczyć, czy zasób jest używany w środowisku produkcyjnym lub testowym. W tym artykule tagi są stosowane tylko do jednego zasobu, ale w danym środowisku prawdopodobnie warto dodawania tagów do wszystkich zasobów.

Polecenie stosuje się dwa tagi do konta magazynu:

```azurecli-interactive
az resource tag --tags Dept=IT Environment=Test -g TestRG1 -n myuniquestorage --resource-type "Microsoft.Storage/storageAccounts"
```

Tagi są aktualizowane jako pojedynczego obiektu. Aby dodać tag do zasobu, która zawiera już znaczników, należy najpierw pobrać istniejące znaczniki. Dodaj nowy znacznik do obiektu, który zawiera istniejące znaczniki i ponownie zastosuj wszystkie tagi do zasobu.

```azurecli-interactive
jsonrtag=$(az resource show -g TestRG1 -n myuniquestorage --resource-type "Microsoft.Storage/storageAccounts" --query tags)
rt=$(echo $jsonrtag | tr -d '"{},' | sed 's/: /=/g')
az resource tag --tags $rt Project=Redesign -g TestRG1 -n myuniquestorage --resource-type "Microsoft.Storage/storageAccounts"
```

## <a name="search-for-resources"></a>Wyszukaj zasoby

Użyj **listę zasobów az** polecenie, aby pobrać zasobów do wyszukiwania różnych warunków.

* Aby uzyskać za pomocą nazwy zasobu, należy podać **nazwa** parametru:

  ```azurecli-interactive
  az resource list -n myuniquestorage
  ```

* Aby uzyskać wszystkie zasoby w grupie zasobów, należy podać **grupy zasobów** parametru:

  ```azurecli-interactive
  az resource list --resource-group TestRG1
  ```

* Aby pobrać wszystkich zasobów o nazwie tagu i wartości, należy podać **tag** parametru:

  ```azurecli-interactive
  az resource list --tag Dept=IT
  ```

* Do wszystkich zasobów z określonego typu zasobu, należy podać **typu zasobu** parametru:

  ```azurecli-interactive
  az resource list --resource-type "Microsoft.Storage/storageAccounts"
  ```

## <a name="get-resource-id"></a>Pobierz identyfikator zasobu

Wiele poleceń zająć identyfikator zasobu jako parametr. Aby uzyskać identyfikator zasobu i przechowywać w zmiennej, należy użyć:

```azurecli-interactive
webappID=$(az resource show -g exampleGroup -n exampleSite --resource-type "Microsoft.Web/sites" --query id --output tsv)
```

## <a name="lock-a-resource"></a>Zablokowanie zasobu

Aby upewnić się, że krytyczne zasobów nie zostanie przypadkowo usunięty lub zmodyfikowany, zastosuj blokady do zasobu. Można określić **CanNotDelete** lub **tylko do odczytu**.

Aby utworzyć lub usunąć blokady zarządzania, musi mieć dostęp do `Microsoft.Authorization/*` lub `Microsoft.Authorization/locks/*` akcje. Wbudowanych ról tylko właściciel i Administrator dostępu użytkowników są przyznawane te akcje.

Aby zastosować blokady, użyj następującego polecenia:

```azurecli-interactive
az lock create --lock-type CanNotDelete --resource-name myuniquestorage --resource-group TestRG1 --resource-type Microsoft.Storage/storageAccounts --name storagelock
```

Nie można usunąć zablokowanych zasobu w poprzednim przykładzie, aż do usunięcia blokady. Aby usunąć blokadę, należy użyć:

```azurecli-interactive
az lock delete --name storagelock --resource-group TestRG1 --resource-type Microsoft.Storage/storageAccounts --resource-name myuniquestorage
```

Aby uzyskać więcej informacji na temat ustawienie blokady, zobacz [blokowania zasobów z usługi Azure Resource Manager](resource-group-lock-resources.md).

## <a name="remove-resources-or-resource-group"></a>Usuwanie zasobów lub grupy zasobów
Można usunąć zasób lub grupa zasobów. Usuń grupę zasobów, należy również usunąć wszystkie zasoby w grupie zasobów.

* Aby usunąć zasób z grupy zasobów, należy użyć polecenia delete dla typu zasobu, który usuwasz. Polecenie usuwa zasób, ale nie powoduje usunięcia grupy zasobów.

  ```azurecli-interactive
  az storage account delete -n myuniquestorage -g TestRG1
  ```

* Aby usunąć grupę zasobów i wszystkie jej zasoby, użyj polecenia **az group delete**.

  ```azurecli-interactive
  az group delete -n TestRG1
  ```

Dla obu polecenia zostanie wyświetlona prośba o potwierdzenie, że chcesz usunąć zasób lub grupa zasobów.

## <a name="next-steps"></a>Kolejne kroki
* Aby uzyskać informacje dotyczące tworzenia szablonów usługi Resource Manager, zobacz [Authoring Azure Resource Manager szablony](resource-group-authoring-templates.md).
* Informacje na temat wdrażania szablonów, zobacz [wdrażanie aplikacji przy użyciu szablonu usługi Resource Manager Azure](resource-group-template-deploy-cli.md).
* Można przenieść istniejące zasoby do nowej grupy zasobów. Aby uzyskać przykłady, zobacz [przeniesienia zasobów do nowej grupy zasobów lub subskrypcji](resource-group-move-resources.md).
* Aby uzyskać instrukcje dla przedsiębiorstw dotyczące użycia usługi Resource Manager w celu efektywnego zarządzania subskrypcjami, zobacz [Azure enterprise scaffold - prescriptive subscription governance](/azure/architecture/cloud-adoption-guide/subscription-governance) (Szkielet platformy Azure dla przedsiębiorstwa — narzucony nadzór subskrypcji).