---
title: Zarządzanie zasobami za pomocą wiersza polecenia platformy Azure | Dokumentacja firmy Microsoft
description: Użyj interfejsu wiersza polecenia platformy Azure (CLI) do zarządzania zasobami platformy Azure i grupami
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
ms.openlocfilehash: dd111c33cbd348a05ed0f0c04f7325347612e54d
ms.sourcegitcommit: c2c64fc9c24a1f7bd7c6c91be4ba9d64b1543231
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/26/2018
ms.locfileid: "39259890"
---
# <a name="use-the-azure-cli-to-manage-azure-resources-and-resource-groups"></a>Używanie wiersza polecenia platformy Azure do zarządzania zasobami i grupami zasobów platformy Azure

W tym artykule dowiesz się, jak zarządzać rozwiązania za pomocą wiersza polecenia platformy Azure i usługi Azure Resource Manager. Jeśli użytkownik nie jest zaznajomiony z usługą Resource Manager, zobacz [Omówienie usługi Resource Manager](resource-group-overview.md). Ten artykuł koncentruje się na zadania związane z zarządzaniem. Wykonasz następujące zadania:

1. Tworzenie grupy zasobów
2. Dodaj zasób do grupy zasobów
3. Dodaj tag do zasobu
4. Wykonywanie zapytań względem zasobów na podstawie nazwy lub wartości tagów
5. Zastosuj i Usuń blokady dla zasobu
6. Usuń grupę zasobów

W tym artykule nie pokazuje, jak wdrożyć szablon usługi Resource Manager do Twojej subskrypcji. Aby uzyskać te informacje, zobacz [wdrażanie zasobów za pomocą szablonów usługi Resource Manager i interfejsu wiersza polecenia Azure](resource-group-template-deploy-cli.md).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Aby zainstalować i korzystać z interfejsu wiersza polecenia lokalnie, zobacz [interfejsu wiersza polecenia platformy Azure Zainstaluj](/cli/azure/install-azure-cli).

## <a name="set-subscription"></a>Zestaw subskrypcji

Jeśli masz więcej niż jedną subskrypcję, możesz przełączyć się do innej subskrypcji. Po pierwsze Zobaczmy, wszystkie subskrypcje dla swojego konta.

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

Należy zauważyć, że jedna subskrypcja jest oznaczony jako domyślny. Ta subskrypcja jest bieżący kontekst dla operacji. Aby przełączyć się do innej subskrypcji, podaj nazwę subskrypcji, za pomocą **zestaw konta az** polecenia.

```azurecli-interactive
az account set -s "Example Subscription Two"
```

Aby wyświetlić bieżącego kontekstu subskrypcji, użyj **Pokaż konta az** bez parametru:

```azurecli-interactive
az account show
```

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Przed wdrożeniem wszystkie zasoby w subskrypcji, należy utworzyć grupę zasobów, która będzie zawierać zasoby.

Aby utworzyć grupę zasobów, użyj polecenia **az group create**. Używa polecenia **nazwa** parametru do określenia nazwy grupy zasobów i **lokalizacji** parametru, aby określić jego lokalizację.

```azurecli-interactive
az group create --name TestRG1 --location "South Central US"
```

Dane wyjściowe są w następującym formacie:

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

Jeśli zachodzi potrzeba później pobrać grupy zasobów, użyj następującego polecenia:

```azurecli-interactive
az group show --name TestRG1
```

Aby uzyskać wszystkie grupy zasobów w ramach subskrypcji, użyj:

```azurecli-interactive
az group list
```

## <a name="add-resources-to-a-resource-group"></a>Dodawanie zasobów do grupy zasobów

Można dodać zasobu do grupy zasobów, można użyć **tworzenia zasobów az** lub polecenia, które są specyficzne dla typu zasobu tworzysz (takich jak **Tworzenie konta magazynu az**). Może znaleźć łatwiejszy w obsłudze polecenia, które są specyficzne dla typu zasobu, ponieważ zawiera on parametry dla właściwości, które są wymagane dla nowego zasobu. Aby użyć **tworzenia zasobów az**, musisz wiedzieć, wszystkie właściwości można ustawić bez monitowania o podanie ich.

Jednak dodanie zasobów za pośrednictwem skryptu może spowodować przyszłych pomyłek, ponieważ nowy zasób nie istnieje w szablonie usługi Resource Manager. Szablony umożliwiają niezawodne i wielokrotnie wdrażać rozwiązania.

Następujące polecenie tworzy konto magazynu. Zamiast przy użyciu nazwy, jak pokazano w przykładzie, należy podać unikatową nazwę konta magazynu. Nazwa musi mieć długość od 3 do 24 znaków długości i użyć tylko cyfry i małe litery. Jeśli używasz nazwy, jak pokazano w przykładzie, otrzymasz komunikat o błędzie, ponieważ ta nazwa jest już używana.

```azurecli-interactive
az storage account create -n myuniquestorage -g TestRG1 -l westus --sku Standard_LRS
```

Jeśli musisz pobrać ten zasób później, użyj następującego polecenia:

```azurecli-interactive
az storage account show --name myuniquestorage --resource-group TestRG1
```

## <a name="add-a-tag"></a>Dodaj tag

Tagi umożliwiają organizowanie zasobów zgodnie z różnymi właściwościami. Na przykład może mieć kilka zasobów, w różnych grupach zasobów, które należą do tego samego działu. Department tag i wartość można zastosować do tych zasobów, aby oznaczyć je jako należące do tej samej kategorii. Lub można określić, czy zasób jest używany w środowisku przedprodukcyjnym lub testowym. W tym artykule stosujesz tagi do tylko jednego zasobu, ale w danym środowisku prawdopodobnie warto zastosować znaczniki do wszystkich posiadanych zasobów.

Następujące polecenie stosuje się dwa znaczniki do swojego konta magazynu:

```azurecli-interactive
az resource tag --tags Dept=IT Environment=Test -g TestRG1 -n myuniquestorage --resource-type "Microsoft.Storage/storageAccounts"
```

Tagi są aktualizowane jako pojedynczy obiekt. Aby dodać tag do zasobu, która zawiera już znaczników, należy najpierw pobrać istniejących tagów. Dodaj nowy tag do obiektu, który zawiera istniejące tagi i ponownie zastosować wszystkie tagi do zasobu.

```azurecli-interactive
jsonrtag=$(az resource show -g TestRG1 -n myuniquestorage --resource-type "Microsoft.Storage/storageAccounts" --query tags)
rt=$(echo $jsonrtag | tr -d '"{},' | sed 's/: /=/g')
az resource tag --tags $rt Project=Redesign -g TestRG1 -n myuniquestorage --resource-type "Microsoft.Storage/storageAccounts"
```

## <a name="search-for-resources"></a>Wyszukaj zasoby

Użyj **az resource list** polecenie, aby pobrać zasoby dla warunków wyszukiwania innym.

* Aby uzyskać zasobu według nazwy, podaj **nazwa** parametru:

  ```azurecli-interactive
  az resource list -n myuniquestorage
  ```

* Aby uzyskać wszystkie zasoby w grupie zasobów, należy podać **grupy zasobów** parametru:

  ```azurecli-interactive
  az resource list --resource-group TestRG1
  ```

* Aby uzyskać wszystkie zasoby z nazwę i wartość tagu, należy podać **tag** parametru:

  ```azurecli-interactive
  az resource list --tag Dept=IT
  ```

* Do wszystkich zasobów przy użyciu określonego typu zasobów, należy podać **typ zasobu** parametru:

  ```azurecli-interactive
  az resource list --resource-type "Microsoft.Storage/storageAccounts"
  ```

## <a name="get-resource-id"></a>Pobierz identyfikator zasobu

Wiele poleceń zająć identyfikator zasobu jako parametr. Aby uzyskać identyfikator zasobu i magazynu w zmiennej, należy użyć:

```azurecli-interactive
webappID=$(az resource show -g exampleGroup -n exampleSite --resource-type "Microsoft.Web/sites" --query id --output tsv)
```

## <a name="lock-a-resource"></a>Blokowanie zasobów

Kiedy należy upewnić się, że krytycznych zasobów nie zostanie przypadkowo usunięty lub zmodyfikowany, zastosować blokadę do zasobu. Można określić **CanNotDelete** lub **tylko do odczytu**.

Aby utworzyć lub usunąć blokady zarządzania, musi mieć dostęp do `Microsoft.Authorization/*` lub `Microsoft.Authorization/locks/*` akcji. Wbudowanych ról tylko właściciel i Administrator dostępu użytkowników są przyznawane tych akcji.

Aby zastosować blokadę, użyj następującego polecenia:

```azurecli-interactive
az lock create --lock-type CanNotDelete --resource-name myuniquestorage --resource-group TestRG1 --resource-type Microsoft.Storage/storageAccounts --name storagelock
```

Nie można usunąć zasobu zablokowane w poprzednim przykładzie, dopóki blokada została usunięta. Aby usunąć blokadę, należy użyć:

```azurecli-interactive
az lock delete --name storagelock --resource-group TestRG1 --resource-type Microsoft.Storage/storageAccounts --resource-name myuniquestorage
```

Aby uzyskać więcej informacji na temat ustawień blokady zobacz [blokowanie zasobów w usłudze Azure Resource Manager](resource-group-lock-resources.md).

## <a name="remove-resources-or-resource-group"></a>Usuń zasoby lub grupy zasobów
Możesz usunąć zasób lub grupa zasobów. Możesz usunąć grupę zasobów, możesz również usunąć wszystkie zasoby w tej grupie zasobów.

* Można usunąć zasobu z grupy zasobów, użyj polecenia delete dla typu zasobu, który usuwasz. Polecenie spowoduje usunięcie zasobu, ale nie powoduje usunięcia grupy zasobów.

  ```azurecli-interactive
  az storage account delete -n myuniquestorage -g TestRG1
  ```

* Aby usunąć grupę zasobów i wszystkie jej zasoby, użyj polecenia **az group delete**.

  ```azurecli-interactive
  az group delete -n TestRG1
  ```

Oba polecenia zostanie wyświetlony monit upewnij się, że chcesz usunąć zasób lub grupa zasobów.

## <a name="next-steps"></a>Kolejne kroki
* Aby dowiedzieć się więcej na temat tworzenia szablonów usługi Resource Manager, zobacz [Tworzenie szablonów usługi Resource Manager platformy Azure](resource-group-authoring-templates.md).
* Aby dowiedzieć się więcej na temat wdrażania szablonów, zobacz [wdrażanie aplikacji przy użyciu szablonu Azure Resource Manager](resource-group-template-deploy-cli.md).
* Można przenieść istniejące zasoby, do nowej grupy zasobów. Aby uzyskać przykłady, zobacz [przeniesienia zasobów do nowej grupy zasobów lub subskrypcji](resource-group-move-resources.md).
* Aby uzyskać instrukcje dla przedsiębiorstw dotyczące użycia usługi Resource Manager w celu efektywnego zarządzania subskrypcjami, zobacz [Azure enterprise scaffold - prescriptive subscription governance](/azure/architecture/cloud-adoption-guide/subscription-governance) (Szkielet platformy Azure dla przedsiębiorstwa — narzucony nadzór subskrypcji).