---
title: Wdrażanie zasobów przy użyciu wiersza polecenia platformy Azure i szablonu | Dokumentacja firmy Microsoft
description: Użyj usługi Azure Resource Manager i interfejsu wiersza polecenia platformy Azure do wdrażania zasobów platformy Azure. Zasoby są zdefiniowane w szablonie usługi Resource Manager.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
ms.assetid: 493b7932-8d1e-4499-912c-26098282ec95
ms.service: azure-resource-manager
ms.devlang: azurecli
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/28/2019
ms.author: tomfitz
ms.openlocfilehash: 6cccae343e0a06af88c2e996c37910de72138c60
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/04/2019
ms.locfileid: "66475040"
---
# <a name="deploy-resources-with-resource-manager-templates-and-azure-cli"></a>Deploy resources with Resource Manager templates and Azure CLI (Wdrażanie zasobów za pomocą szablonów usługi Resource Manager i interfejsu wiersza polecenia platformy Azure)

W tym artykule wyjaśniono, jak używać wiersza polecenia platformy Azure przy użyciu szablonów usługi Resource Manager do wdrażania zasobów na platformie Azure. Jeśli nie znasz pojęć dotyczących wdrażania i zarządzania Twoich rozwiązań platformy Azure, zobacz [Omówienie usługi Azure Resource Manager](resource-group-overview.md).  

[!INCLUDE [sample-cli-install](../../includes/sample-cli-install.md)]

Jeśli nie masz zainstalowany interfejs wiersza polecenia platformy Azure, możesz użyć [Cloud Shell](#deploy-template-from-cloud-shell).

## <a name="deployment-scope"></a>Zakres wdrożenia

Można wskazać wdrożenia do subskrypcji platformy Azure lub grupy zasobów w ramach subskrypcji. W większości przypadków będzie wskazywać wdrożenie w grupie zasobów. Subskrypcja wdrożeń umożliwiają stosowanie zasad i przypisań ról w ramach subskrypcji. Subskrypcja wdrożeń możesz także użyć do tworzenia grupy zasobów i wdrażania zasobów. W zależności od zakresu wdrożenia możesz używać różnych poleceń.

Aby wdrożyć **grupy zasobów**, użyj [Utwórz wdrożenie grupy az](/cli/azure/group/deployment?view=azure-cli-latest#az-group-deployment-create):

```azurecli
az group deployment create --resource-group <resource-group-name> --template-file <path-to-template>
```

Aby wdrożyć **subskrypcji**, użyj [tworzenia wdrożenia az](/cli/azure/deployment?view=azure-cli-latest#az-deployment-create):

```azurecli
az deployment create --location <location> --template-file <path-to-template>
```

Wdrożenia grup zarządzania są obecnie obsługiwane tylko za pośrednictwem interfejsu API REST. Zobacz [wdrażanie zasobów przy użyciu szablonów usługi Resource Manager i interfejsu REST API usługi Resource Manager](resource-group-template-deploy-rest.md).

W przykładach w tym artykule używany wdrożenia grupy zasobów. Aby uzyskać więcej informacji o wdrożeniach subskrypcji, zobacz [tworzenia grupy zasobów i zasobów na poziomie subskrypcji](deploy-to-subscription.md).

## <a name="deploy-local-template"></a>Wdrażanie lokalnego szablonu

Podczas wdrażania zasobów na platformie Azure, możesz:

1. Zaloguj się do swojego konta platformy Azure
2. Utwórz grupę zasobów, która służy jako kontener dla wdrożonych zasobów. Nazwa grupy zasobów może zawierać tylko znaki alfanumeryczne, kropki, podkreślenia, łączniki i nawiasy. Może być maksymalnie 90 znaków. Nie może kończyć się kropką.
3. Wdrożyć szablon który definiuje zasoby do utworzenia grupy zasobów

Szablon może zawierać parametrów, które umożliwiają dostosowanie wdrożenia. Na przykład możesz podać wartości, które są dostosowane dla określonego środowiska (na przykład deweloperskim, testowym i produkcyjnym). Przykładowy szablon definiuje parametr dla jednostki SKU konta magazynu. 

Poniższy przykład tworzy grupę zasobów i służy do wdrażania szablonu z komputera lokalnego:

```azurecli-interactive
az group create --name ExampleGroup --location "Central US"
az group deployment create \
  --name ExampleDeployment \
  --resource-group ExampleGroup \
  --template-file storage.json \
  --parameters storageAccountType=Standard_GRS
```

Wdrożenie może potrwać kilka minut. Po zakończeniu zostanie wyświetlony komunikat, który zawiera wynik:

```azurecli
"provisioningState": "Succeeded",
```

## <a name="deploy-remote-template"></a>Wdrażanie szablonu zdalnego

Zamiast przechowywać szablonów usługi Resource Manager na komputerze lokalnym, użytkownik może chcieć przechowywać je w lokalizacji zewnętrznej. Szablony można przechowywać w repozytorium kontroli źródła (na przykład GitHub). Lub można przechowywać na koncie magazynu platformy Azure w celu zapewnienia dostępu współdzielonego w Twojej organizacji.

Aby wdrożyć szablon zewnętrznego, użyj **identyfikator uri szablonu** parametru. Użyj identyfikatora URI w przykładzie, aby wdrożyć przykładowy szablon z serwisu GitHub.

```azurecli-interactive
az group create --name ExampleGroup --location "Central US"
az group deployment create \
  --name ExampleDeployment \
  --resource-group ExampleGroup \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json" \
  --parameters storageAccountType=Standard_GRS
```

Poprzedni przykład wymaga publicznie identyfikator URI dla szablonu, który działa w przypadku większości scenariuszy, ponieważ szablon nie powinna zawierać dane poufne. Jeśli musisz określić dane poufne (na przykład hasło administratora), należy przekazać tę wartość jako parametru secure. Jednak jeśli nie chcesz, aby szablon był dostępny publicznie, można go chronić dzięki przechowywaniu go w kontenerze magazynu prywatnego. Aby uzyskać informacji o wdrażaniu szablonu, który wymaga tokenu (SAS) sygnatury dostępu współdzielonego, zobacz [wdrażanie prywatnego szablonu przy użyciu tokenu sygnatury dostępu Współdzielonego](resource-manager-cli-sas-token.md).

[!INCLUDE [resource-manager-cloud-shell-deploy.md](../../includes/resource-manager-cloud-shell-deploy.md)]

W usłudze Cloud Shell Użyj następujących poleceń:

```azurecli-interactive
az group create --name examplegroup --location "South Central US"
az group deployment create --resource-group examplegroup \
  --template-uri <copied URL> \
  --parameters storageAccountType=Standard_GRS
```

## <a name="redeploy-when-deployment-fails"></a>Wdróż ponownie, gdy wdrożenie zakończy się niepowodzeniem

Ta funkcja jest nazywana *wycofywania w przypadku błędu*. Jeśli wdrożenie zakończy się niepowodzeniem, można automatycznie wdrożyć ponownie wcześniej pomyślnego wdrażania z historii wdrożenia. Aby określić ponowne wdrożenie, użyj `--rollback-on-error` parametr w poleceniu wdrożenia. Ta funkcja jest przydatna, jeśli coś znanego, dobrego stanu dla danego wdrożenia infrastruktury, a chcesz przywrócić ten stan. Kilka ograniczeń i ostrzeżenia:

- Ponownego wdrożenia komputera jest uruchamiany, dokładnie tak, jak została poprzednio uruchomiona z tymi samymi parametrami. Nie można zmienić parametry.
- Poprzedniego wdrożenia zostanie uruchomiony z użyciem [w trybie](./deployment-modes.md#complete-mode). Zostaną usunięte wszystkie zasoby, które nie są uwzględnione w poprzednim wdrożeniu, a wszystkie konfiguracje zasobów są ustawione do poprzedniego stanu. Upewnij się, w pełni rozumiesz [tryby wdrażania](./deployment-modes.md).
- Ponownego wdrożenia komputera ma wpływ tylko na zasoby, nie ma wpływu na wszelkie zmiany danych.
- Ta funkcja jest obsługiwana tylko we wdrożeniach grupy zasobów, nie wdrożeń poziomu subskrypcji. Aby uzyskać więcej informacji na temat wdrażania poziomu subskrypcji zobacz [tworzenia grupy zasobów i zasobów na poziomie subskrypcji](./deploy-to-subscription.md).

Aby użyć tej opcji, wdrożeń muszą mieć unikatowe nazwy, dzięki czemu można je zidentyfikować w historii. Jeśli nie masz unikatowe nazwy bieżącego wdrożenia nie powiodło się może spowodować zastąpienie wcześniej pomyślnego wdrożenia w historii. Tej opcji można używać tylko w przypadku wdrożeń poziomu głównego. Wdrożenia z szablonów zagnieżdżonych nie są dostępne dla ponownego wdrażania.

Aby przeprowadzić ponowne wdrożenie ostatniego pomyślnego wdrożenia, należy dodać `--rollback-on-error` parametr jako flagi.

```azurecli-interactive
az group deployment create \
  --name ExampleDeployment \
  --resource-group ExampleGroup \
  --template-file storage.json \
  --parameters storageAccountType=Standard_GRS \
  --rollback-on-error
```

Aby przeprowadzić ponowne wdrożenie określonego wdrożenia, należy użyć `--rollback-on-error` parametru i podaj nazwę wdrożenia.

```azurecli-interactive
az group deployment create \
  --name ExampleDeployment02 \
  --resource-group ExampleGroup \
  --template-file storage.json \
  --parameters storageAccountType=Standard_GRS \
  --rollback-on-error ExampleDeployment01
```

Zakończyły się powodzeniem określonego wdrożenia.

## <a name="parameters"></a>Parametry

Aby przekazać wartości parametrów, można użyć wbudowanego parametrów lub plik parametrów. Powyższych przykładach w tym artykule Pokaż parametry w tekście.

### <a name="inline-parameters"></a>Parametry wbudowane

Aby przekazać parametry wbudowane, podaj wartości w `parameters`. Na przykład, aby przekazać ciąg i Tablica do szablonu jest powłoką Bash, użyj:

```azurecli
az group deployment create \
  --resource-group testgroup \
  --template-file demotemplate.json \
  --parameters exampleString='inline string' exampleArray='("value1", "value2")'
```

Możesz również uzyskać zawartość pliku i podaj tę zawartość jako parametr w tekście.

```azurecli
az group deployment create \
  --resource-group testgroup \
  --template-file demotemplate.json \
  --parameters exampleString=@stringContent.txt exampleArray=@arrayContent.json
```

Wprowadzenie wartości parametru z pliku jest przydatne, gdy trzeba określić wartości konfiguracji. Na przykład można podać [wartości pakietu cloud-init dla maszyny wirtualnej Linux](../virtual-machines/linux/using-cloud-init.md).

ArrayContent.json format jest następujący:

```json
[
    "value1",
    "value2"
]
```

### <a name="parameter-files"></a>Pliki parametrów

Zamiast przekazywania parametrów jako wartości wbudowanych w skrypcie, użytkownik może okazać się łatwiejszy w obsłudze pliku JSON, który zawiera wartości parametrów. Plik parametrów musi być plikiem lokalnym. Pliki parametrów zewnętrznych nie są obsługiwane przy użyciu wiersza polecenia platformy Azure.

Plik parametrów musi być w następującym formacie:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
     "storageAccountType": {
         "value": "Standard_GRS"
     }
  }
}
```

Należy zauważyć, że w sekcji parametrów zawiera nazwę parametru, która pasuje do parametrów zdefiniowanych w szablonie (storageAccountType). Plik parametrów zawiera wartość dla parametru. Ta wartość jest automatycznie przekazanych do szablonu podczas wdrażania. Można utworzyć więcej niż jeden plik parametrów i następnie przekazać plik parametrów właściwe dla scenariusza. 

Poprzedni przykład skopiuj i zapisz go jako plik o nazwie `storage.parameters.json`.

Aby przekazać plik parametrów lokalnych, należy użyć `@` do określenia pliku lokalnego o nazwie storage.parameters.json.

```azurecli-interactive
az group deployment create \
  --name ExampleDeployment \
  --resource-group ExampleGroup \
  --template-file storage.json \
  --parameters @storage.parameters.json
```

### <a name="parameter-precedence"></a>Parametr pierwszeństwo

Można użyć wbudowanego parametrów i pliku parametrów lokalnych w tej samej operacji wdrożenia. Na przykład można określić niektóre wartości w pliku parametrów lokalnych i dodać inne wbudowane wartości podczas wdrażania. Jeśli możesz podać wartości parametrów w pliku parametrów lokalnych i wbudowane, pierwszeństwo ma wartość wbudowanej.

```azurecli
az group deployment create \
  --resource-group testgroup \
  --template-file demotemplate.json \
  --parameters @demotemplate.parameters.json \
  --parameters exampleArray=@arrtest.json
```

## <a name="test-a-template-deployment"></a>Testowanie wdrażania szablonu

Aby przetestować wartości szablonu oraz parametrów bez faktycznego wdrażania zasobów, użyj [zweryfikować wdrożenie grupy az](/cli/azure/group/deployment#az-group-deployment-validate). 

```azurecli-interactive
az group deployment validate \
  --resource-group ExampleGroup \
  --template-file storage.json \
  --parameters @storage.parameters.json
```

Jeśli zostaną wykryte żadne błędy, polecenie zwraca informacje o wdrożeniu testu. W szczególności zwróć uwagę, że **błąd** ma wartość null.

```azurecli
{
  "error": null,
  "properties": {
      ...
```

Jeśli zostanie wykryty błąd, to polecenie zwraca komunikat o błędzie. Na przykład przekazując niepoprawną wartość dla konta magazynu jednostki SKU i zwraca następujący błąd:

```azurecli
{
  "error": {
    "code": "InvalidTemplate",
    "details": null,
    "message": "Deployment template validation failed: 'The provided value 'badSKU' for the template parameter 
      'storageAccountType' at line '13' and column '20' is not valid. The parameter value is not part of the allowed 
      value(s): 'Standard_LRS,Standard_ZRS,Standard_GRS,Standard_RAGRS,Premium_LRS'.'.",
    "target": null
  },
  "properties": null
}
```

Jeśli szablon zawiera błąd składniowy, polecenie zwraca komunikat o błędzie informujący, że nie można go przeanalizować szablonu. Komunikat wskazuje, numer wiersza i położenie błąd analizy.

```azurecli
{
  "error": {
    "code": "InvalidTemplate",
    "details": null,
    "message": "Deployment template parse failed: 'After parsing a value an unexpected character was encountered:
      \". Path 'variables', line 31, position 3.'.",
    "target": null
  },
  "properties": null
}
```

## <a name="next-steps"></a>Kolejne kroki

- Przykłady w niniejszym artykule wdrażanie zasobów w grupie zasobów w subskrypcji domyślnej. Aby użyć innej subskrypcji, zobacz [Zarządzanie wieloma subskrypcjami platformy Azure](/cli/azure/manage-azure-subscriptions-azure-cli).
- Aby określić sposób obsługi zasobów, które istnieją w grupie zasobów, ale nie są zdefiniowane w szablonie, zobacz [tryby wdrażania usługi Azure Resource Manager](deployment-modes.md).
- Aby dowiedzieć się, jak zdefiniować parametry w szablonie, zobacz [Omówienie struktury i składni szablonów usługi Azure Resource Manager](resource-group-authoring-templates.md).
- Aby uzyskać porady dotyczące rozwiązywania typowych problemów wdrażania, zobacz [Rozwiązywanie typowych problemów wdrażania na platformie Azure przy użyciu usługi Azure Resource Manager](resource-manager-common-deployment-errors.md).
- Aby uzyskać informacje o wdrażaniu szablonu, który wymaga tokenu sygnatury dostępu Współdzielonego, zobacz [wdrażanie prywatnego szablonu przy użyciu tokenu sygnatury dostępu Współdzielonego](resource-manager-cli-sas-token.md).
- Aby bezpiecznie wdrożyć usługę do więcej niż jednym regionie, zobacz [Azure Deployment Manager](deployment-manager-overview.md).
