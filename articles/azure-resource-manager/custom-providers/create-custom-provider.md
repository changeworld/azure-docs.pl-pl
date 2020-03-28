---
title: Tworzenie dostawcy zasobów
description: W tym artykule opisano sposób tworzenia dostawcy zasobów i wdrażania jego niestandardowych typów zasobów.
author: MSEvanhi
ms.topic: tutorial
ms.date: 05/01/2019
ms.author: evanhi
ms.openlocfilehash: 393993a44c860525b9bd9a540ed7afff78e5b93c
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "75649869"
---
# <a name="quickstart-create-custom-provider-and-deploy-custom-resources"></a>Szybki start: tworzenie dostawcy niestandardowego i wdrażanie zasobów niestandardowych

W tym przewodniku Szybki start utworzysz własnego dostawcę zasobów i wdrożysz niestandardowe typy zasobów dla tego dostawcy zasobów. Aby uzyskać więcej informacji na temat dostawców niestandardowych, zobacz [Omówienie dostawców niestandardowych dostawców platformy Azure w wersji zapoznawczej](overview.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby wykonać kroki w tym przewodniku Szybki start, należy wywołać operacje REST. Istnieją [różne sposoby wysyłania żądań REST](/rest/api/azure/). Jeśli nie masz jeszcze narzędzia do operacji REST, zainstaluj [program ARMClient](https://github.com/projectkudu/ARMClient). Jest to narzędzie wiersza polecenia typu open source, które upraszcza wywoływanie interfejsu API usługi Azure Resource Manager.

## <a name="deploy-custom-provider"></a>Wdrażanie dostawcy niestandardowego

Aby skonfigurować dostawcę niestandardowego, należy wdrożyć [przykładowy szablon](https://github.com/Azure/azure-docs-json-samples/blob/master/custom-providers/customprovider.json) w ramach subskrypcji platformy Azure.

Po wdrożeniu szablonu subskrypcja ma następujące zasoby:

* Aplikacja funkcji z operacjami dla zasobów i akcji.
* Konto magazynu do przechowywania użytkowników, które są tworzone za pośrednictwem dostawcy niestandardowego.
* Dostawca niestandardowy, który definiuje niestandardowe typy zasobów i akcje. Używa punktu końcowego aplikacji funkcji do wysyłania żądań.
* Niestandardowy zasób od dostawcy niestandardowego.

Aby wdrożyć niestandardowego dostawcę za pomocą programu PowerShell, należy użyć:

```azurepowershell-interactive
$rgName = "<resource-group-name>"
$funcName = "<function-app-name>"

New-AzResourceGroup -Name $rgName -Location eastus
New-AzResourceGroupDeployment -ResourceGroupName $rgName `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/custom-providers/customprovider.json `
  -funcname $funcName
```

Można też wdrożyć rozwiązanie za pomocą następującego przycisku:

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-docs-json-samples%2Fmaster%2Fcustom-providers%2Fcustomprovider.json" target="_blank">
    <img src="https://azuredeploy.net/deploybutton.png"/>
</a>

## <a name="view-custom-provider-and-resource"></a>Wyświetlanie niestandardowego dostawcy i zasobu

W portalu dostawca niestandardowy jest ukrytym typem zasobu. Aby potwierdzić, że dostawca zasobów został wdrożony, przejdź do grupy zasobów. Wybierz opcję **Pokaż ukryte typy**.

![Pokaż ukryte typy zasobów](./media/create-custom-provider/show-hidden.png)

Aby wyświetlić wdrożony niestandardowy typ zasobu, użyj operacji GET dla swojego typu zasobu.

```
GET https://management.azure.com/subscriptions/<sub-id>/resourceGroups/<rg-name>/providers/Microsoft.CustomProviders/resourceProviders/<provider-name>/users?api-version=2018-09-01-preview
```

W przypadku ARMClient należy użyć:

```powershell
$subID = (Get-AzContext).Subscription.Id
$requestURI = "https://management.azure.com/subscriptions/$subID/resourceGroups/$rgName/providers/Microsoft.CustomProviders/resourceProviders/$funcName/users?api-version=2018-09-01-preview"

armclient GET $requestURI
```

Otrzymasz odpowiedź:

```json
{
  "value": [
    {
      "properties": {
        "provisioningState": "Succeeded",
        "FullName": "Santa Claus",
        "Location": "NorthPole"
      },
      "id": "/subscriptions/<sub-id>/resourceGroups/<rg-name>/providers/Microsoft.CustomProviders/resourceProviders/<provider-name>/users/santa",
      "name": "santa",
      "type": "Microsoft.CustomProviders/resourceProviders/users"
    }
  ]
}
```

## <a name="call-action"></a>Akcja wywołania

Dostawca niestandardowy ma również akcję o nazwie **ping**. Kod, który przetwarza żądanie jest implementowany w aplikacji funkcji. Akcja ping odpowiada z pozdrowieniami.

Aby wysłać żądanie ping, użyj operacji POST na dostawcy niestandardowego.

```
POST https://management.azure.com/subscriptions/<sub-id>/resourceGroups/<rg-name>/providers/Microsoft.CustomProviders/resourceProviders/<provider-name>/ping?api-version=2018-09-01-preview
```

W przypadku ARMClient należy użyć:

```powershell
$pingURI = "https://management.azure.com/subscriptions/$subID/resourceGroups/$rgName/providers/Microsoft.CustomProviders/resourceProviders/$funcName/ping?api-version=2018-09-01-preview"

armclient POST $pingURI
```

Otrzymasz odpowiedź:

```json
{
  "pingcontent": {
    "source": "<function-name>.azurewebsites.net"
  },
  "message": "hello <function-name>.azurewebsites.net"
}
```

## <a name="create-resource-type"></a>Tworzenie typu zasobu

Aby utworzyć niestandardowy typ zasobu, można wdrożyć zasób w szablonie. Takie podejście jest pokazane w szablonie wdrożonym w tym przewodniku Szybki start. Można również wysłać żądanie PUT dla typu zasobu.

```
PUT https://management.azure.com/subscriptions/<sub-id>/resourceGroups/<rg-name>/providers/Microsoft.CustomProviders/resourceProviders/<provider-name>/users/<resource-name>?api-version=2018-09-01-preview

{"properties":{"FullName": "Test User", "Location": "Earth"}}
```

W przypadku ARMClient należy użyć:

```powershell
$addURI = "https://management.azure.com/subscriptions/$subID/resourceGroups/$rgName/providers/Microsoft.CustomProviders/resourceProviders/$funcName/users/testuser?api-version=2018-09-01-preview"
$requestBody = "{'properties':{'FullName': 'Test User', 'Location': 'Earth'}}"

armclient PUT $addURI $requestBody
```

Otrzymasz odpowiedź:

```json
{
  "properties": {
    "provisioningState": "Succeeded",
    "FullName": "Test User",
    "Location": "Earth"
  },
  "id": "/subscriptions/<sub-ID>/resourceGroups/<rg-name>/providers/Microsoft.CustomProviders/resourceProviders/<provider-name>/users/testuser",
  "name": "testuser",
  "type": "Microsoft.CustomProviders/resourceProviders/users"
}
```

## <a name="next-steps"></a>Następne kroki

Aby zapoznać się z wprowadzeniem do dostawców niestandardowych, zobacz [omówienie dostawców niestandardowych dostawców platformy Azure](overview.md).
