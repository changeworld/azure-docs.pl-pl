---
title: Wdrażanie zasobów przy użyciu interfejsu wiersza polecenia platformy Azure i szablonu
description: Użyj Azure Resource Manager i interfejsu wiersza polecenia platformy Azure, aby wdrożyć zasoby na platformie Azure. Zasoby są zdefiniowane w szablonie usługi Resource Manager.
ms.topic: conceptual
ms.date: 10/09/2019
ms.openlocfilehash: 76b85f04a18da00d3d416476061beac0987e9f8b
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75477930"
---
# <a name="deploy-resources-with-resource-manager-templates-and-azure-cli"></a>Deploy resources with Resource Manager templates and Azure CLI (Wdrażanie zasobów za pomocą szablonów usługi Resource Manager i interfejsu wiersza polecenia platformy Azure)

W tym artykule wyjaśniono, jak używać interfejsu wiersza polecenia platformy Azure z szablonami Menedżer zasobów do wdrażania zasobów na platformie Azure. Jeśli nie znasz pojęć związanych z wdrażaniem rozwiązań platformy Azure i zarządzaniem nimi, zobacz [Omówienie wdrażania szablonów](overview.md).

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

Jeśli nie masz zainstalowanego interfejsu wiersza polecenia platformy Azure, możesz użyć [Cloud Shell](#deploy-template-from-cloud-shell).

## <a name="deployment-scope"></a>Zakres wdrożenia

Wdrożenie można określić w ramach subskrypcji platformy Azure lub grupy zasobów w ramach subskrypcji. W większości przypadków wdrożenie zostanie ukierunkowane na grupę zasobów. Wdrożenia subskrypcji umożliwiają stosowanie zasad i przypisań ról w ramach subskrypcji. W celu utworzenia grupy zasobów i wdrożenia do niej zasobów należy również użyć wdrożeń subskrypcji. W zależności od zakresu wdrożenia używane są inne polecenia.

Aby wdrożyć w **grupie zasobów**, użyj polecenie [AZ Group Deployment Create](/cli/azure/group/deployment?view=azure-cli-latest#az-group-deployment-create):

```azurecli
az group deployment create --resource-group <resource-group-name> --template-file <path-to-template>
```

Aby wdrożyć w **ramach subskrypcji**, użyj polecenie [AZ Deployment Create](/cli/azure/deployment?view=azure-cli-latest#az-deployment-create):

```azurecli
az deployment create --location <location> --template-file <path-to-template>
```

Aby uzyskać więcej informacji o wdrożeniach na poziomie subskrypcji, zobacz [Tworzenie grup zasobów i zasobów na poziomie subskrypcji](deploy-to-subscription.md).

Obecnie wdrożenia grup zarządzania są obsługiwane tylko za pomocą interfejsu API REST. Aby uzyskać więcej informacji o wdrożeniach na poziomie grupy zarządzania, zobacz [Tworzenie zasobów na poziomie grupy zarządzania](deploy-to-management-group.md).

W przykładach w tym artykule są używane wdrożenia grup zasobów.

## <a name="deploy-local-template"></a>Wdróż szablon lokalny

Podczas wdrażania zasobów na platformie Azure:

1. Zaloguj się do swojego konta platformy Azure
2. Utwórz grupę zasobów, która służy jako kontener dla wdrożonych zasobów. Nazwa grupy zasobów może zawierać tylko znaki alfanumeryczne, kropki, podkreślenia, łączniki i nawiasy. Może składać się z maksymalnie 90 znaków. Nie może kończyć się kropką.
3. Wdróż w grupie zasobów szablon definiujący zasoby do utworzenia

Szablon może zawierać parametry, które umożliwiają dostosowanie wdrożenia. Na przykład można podać wartości, które są dostosowane do określonego środowiska (np. deweloperskiego, testowego i produkcyjnego). Przykładowy szablon definiuje parametr dla jednostki SKU konta magazynu.

Poniższy przykład tworzy grupę zasobów i wdraża szablon z komputera lokalnego:

```azurecli-interactive
az group create --name ExampleGroup --location "Central US"
az group deployment create \
  --name ExampleDeployment \
  --resource-group ExampleGroup \
  --template-file storage.json \
  --parameters storageAccountType=Standard_GRS
```

Wdrożenie może potrwać kilka minut. Po zakończeniu zobaczysz komunikat, który zawiera wynik:

```azurecli
"provisioningState": "Succeeded",
```

## <a name="deploy-remote-template"></a>Wdróż zdalny szablon

Zamiast przechowywania szablonów Menedżer zasobów na komputerze lokalnym, warto przechowywać je w lokalizacji zewnętrznej. Szablony można przechowywać w repozytorium kontroli źródła (na przykład GitHub). Możesz również przechowywać je na koncie usługi Azure Storage w celu uzyskania dostępu współdzielonego w organizacji.

Aby wdrożyć szablon zewnętrzny, użyj parametru **Template-URI** . Użyj identyfikatora URI w przykładzie, aby wdrożyć przykładowy szablon z usługi GitHub.

```azurecli-interactive
az group create --name ExampleGroup --location "Central US"
az group deployment create \
  --name ExampleDeployment \
  --resource-group ExampleGroup \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json" \
  --parameters storageAccountType=Standard_GRS
```

Poprzedni przykład wymaga publicznie dostępnego identyfikatora URI dla szablonu, który działa w większości scenariuszy, ponieważ szablon nie powinien zawierać poufnych danych. Jeśli musisz określić dane poufne (na przykład hasło administratora), przekaż tę wartość jako bezpieczny parametr. Jeśli jednak nie chcesz, aby szablon był dostępny publicznie, możesz go chronić, przechowując go w prywatnym kontenerze magazynu. Informacje o wdrażaniu szablonu wymagającego tokenu sygnatury dostępu współdzielonego (SAS) znajdują się w temacie [Deploy Private Template with SAS token](secure-template-with-sas-token.md).

[!INCLUDE [resource-manager-cloud-shell-deploy.md](../../../includes/resource-manager-cloud-shell-deploy.md)]

W Cloud Shell Użyj następujących poleceń:

```azurecli-interactive
az group create --name examplegroup --location "South Central US"
az group deployment create --resource-group examplegroup \
  --template-uri <copied URL> \
  --parameters storageAccountType=Standard_GRS
```

## <a name="parameters"></a>Parametry

Aby przekazać wartości parametrów, można użyć parametrów wbudowanych lub pliku parametrów.

### <a name="inline-parameters"></a>Parametry wbudowane

Aby przekazać parametry wbudowane, podaj wartości w `parameters`. Na przykład, aby przekazać ciąg i tablicę do szablonu, jest powłoką bash, użyj:

```azurecli
az group deployment create \
  --resource-group testgroup \
  --template-file demotemplate.json \
  --parameters exampleString='inline string' exampleArray='("value1", "value2")'
```

Jeśli używasz interfejsu wiersza polecenia platformy Azure z wierszem poleceń systemu Windows (CMD) lub PowerShell, Przekaż tablicę w formacie: `exampleArray="['value1','value2']"`.

Możesz również pobrać zawartość pliku i podać tę zawartość jako parametr wbudowany.

```azurecli
az group deployment create \
  --resource-group testgroup \
  --template-file demotemplate.json \
  --parameters exampleString=@stringContent.txt exampleArray=@arrayContent.json
```

Pobieranie wartości parametru z pliku jest przydatne, gdy trzeba podać wartości konfiguracyjne. Można na przykład udostępnić [wartości z usługi Cloud-init dla maszyny wirtualnej z systemem Linux](../../virtual-machines/linux/using-cloud-init.md).

Format arrayContent. JSON to:

```json
[
    "value1",
    "value2"
]
```

### <a name="parameter-files"></a>Pliki parametrów

Zamiast przekazywać parametry jako wartości wbudowane w skrypcie, można ułatwić korzystanie z pliku JSON, który zawiera wartości parametrów. Plik parametru musi być plikiem lokalnym. Zewnętrzne pliki parametrów nie są obsługiwane w interfejsie wiersza polecenia platformy Azure.

Aby uzyskać więcej informacji na temat pliku parametrów, zobacz [Tworzenie pliku parametrów Menedżer zasobów](parameter-files.md).

Aby przekazać lokalny plik parametrów, użyj `@`, aby określić plik lokalny o nazwie Storage. Parameters. JSON.

```azurecli-interactive
az group deployment create \
  --name ExampleDeployment \
  --resource-group ExampleGroup \
  --template-file storage.json \
  --parameters @storage.parameters.json
```

## <a name="handle-extended-json-format"></a>Obsługa rozszerzonego formatu JSON

Aby wdrożyć szablon z wielowierszowymi ciągami lub komentarzami, należy użyć przełącznika `--handle-extended-json-format`.  Przykład:

```json
{
  "type": "Microsoft.Compute/virtualMachines",
  "name": "[variables('vmName')]", // to customize name, change it in variables
  "location": "[
    parameters('location')
    ]", //defaults to resource group location
  "apiVersion": "2018-10-01",
  /*
    storage account and network interface
    must be deployed first
  */
  "dependsOn": [
    "[resourceId('Microsoft.Storage/storageAccounts/', variables('storageAccountName'))]",
    "[resourceId('Microsoft.Network/networkInterfaces/', variables('nicName'))]"
  ],
```

## <a name="test-a-template-deployment"></a>Testowanie wdrożenia szablonu

Aby przetestować wartości szablonu i parametrów bez faktycznego wdrażania zasobów, użyj [AZ Group Deployment Validate](/cli/azure/group/deployment#az-group-deployment-validate).

```azurecli-interactive
az group deployment validate \
  --resource-group ExampleGroup \
  --template-file storage.json \
  --parameters @storage.parameters.json
```

Jeśli nie wykryto żadnych błędów, polecenie zwraca informacje o wdrożeniu testowym. W szczególności Zwróć uwagę na to, że wartość **błędu** to null.

```azurecli
{
  "error": null,
  "properties": {
      ...
```

Jeśli zostanie wykryty błąd, polecenie zwróci komunikat o błędzie. Na przykład przekazanie nieprawidłowej wartości dla jednostki SKU konta magazynu zwróci następujący błąd:

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

Jeśli szablon zawiera błąd składniowy, polecenie zwróci błąd wskazujący, że nie można przeanalizować szablonu. Komunikat wskazuje numer wiersza i położenie błędu analizy.

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

## <a name="next-steps"></a>Następne kroki

- Aby wrócić do pomyślnego wdrożenia, gdy wystąpi błąd, zobacz [wycofywanie po pomyślnym wdrożeniu](rollback-on-error.md).
- Aby określić sposób obsługi zasobów, które istnieją w grupie zasobów, ale nie są zdefiniowane w szablonie, zobacz [Azure Resource Manager trybami wdrożenia](deployment-modes.md).
- Aby zrozumieć, jak definiować parametry w szablonie, zobacz [Opis struktury i składni szablonów Azure Resource Manager](template-syntax.md).
- Aby uzyskać wskazówki dotyczące rozwiązywania typowych błędów wdrażania, zobacz [Rozwiązywanie typowych błędów wdrażania platformy Azure przy użyciu Azure Resource Manager](common-deployment-errors.md).
- Informacje o wdrażaniu szablonu wymagającego tokenu SAS można znaleźć w temacie [Deploy Private Template with SAS token](secure-template-with-sas-token.md).
- Aby bezpiecznie wdrożyć usługę w więcej niż jednym regionie, zobacz [Azure Menedżer wdrażania](deployment-manager-overview.md).
