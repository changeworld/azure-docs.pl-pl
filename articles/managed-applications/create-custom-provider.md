---
title: Tworzenie dostawcy zasobów za pomocą dostawców niestandardowych Azure w wersji zapoznawczej
description: W tym artykule opisano sposób tworzenia dostawcy zasobów i wdrażanie ich niestandardowych typów zasobów.
services: managed-applications
author: MSEvanhi
ms.service: managed-applications
ms.topic: tutorial
ms.date: 05/01/2019
ms.author: evanhi
ms.openlocfilehash: e4196cf59537be5194ceb510a1b7b066c97de19a
ms.sourcegitcommit: 6f043a4da4454d5cb673377bb6c4ddd0ed30672d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/08/2019
ms.locfileid: "65410217"
---
# <a name="tutorial-create-custom-provider-and-deploy-custom-resources"></a>Samouczek: Tworzenie niestandardowego dostawcy i wdrażanie zasobów niestandardowych

W ramach tego samouczka możesz utworzyć własnego dostawcę zasobów i wdrażać niestandardowe typy zasobów dla tego dostawcy zasobów. Aby uzyskać więcej informacji na temat niestandardowych dostawców, zobacz [Azure niestandardowych dostawców (wersja zapoznawcza) — omówienie](custom-providers-overview.md).

## <a name="prerequisites"></a>Wymagania wstępne

Wykonanie czynności opisanych w tym samouczku, musisz wywołać operacje REST. Istnieją [różne sposoby wysyłania żądania REST](/rest/api/azure/). Jeśli nie masz jeszcze narzędziem dla operacji REST, należy zainstalować [ARMClient](https://github.com/projectkudu/ARMClient). To narzędzie wiersza polecenia typu open source, które upraszcza wywoływanie interfejsu API usługi Azure Resource Manager.

## <a name="deploy-custom-provider"></a>Wdrażanie niestandardowego dostawcy

Aby skonfigurować niestandardowego dostawcę, należy wdrożyć [przykładowy szablon](https://github.com/Azure/azure-docs-json-samples/blob/master/custom-providers/customprovider.json) do subskrypcji platformy Azure.

Po wdrożeniu szablonu, Twoja subskrypcja obejmuje następujące zasoby:

* Funkcja aplikacji za pomocą operacji dotyczących zasobów i akcji.
* Konto magazynu do przechowywania użytkowników, które są tworzone za pomocą niestandardowego dostawcy.
* Dostawcy niestandardowego, który definiuje niestandardowe typy zasobów i akcji. Używa punktu końcowego aplikacji funkcji wysyłania żądań.
* Zasób niestandardowy z niestandardowego dostawcy.

Aby wdrożyć dostawcy niestandardowego przy użyciu programu PowerShell, należy użyć:

```azurepowershell-interactive
$rgName = "<resource-group-name>"
$funcName = "<function-app-name>"

New-AzResourceGroup -Name $rgName -Location eastus
New-AzResourceGroupDeployment -ResourceGroupName $rgName `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/custom-providers/customprovider.json `
  -funcname $funcName
```

Alternatywnie można wdrożyć rozwiązanie za pomocą poniższy przycisk:

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-docs-json-samples%2Fmaster%2Fcustom-providers%2Fcustomprovider.json" target="_blank">
    <img src="https://azuredeploy.net/deploybutton.png"/>
</a>

## <a name="view-custom-provider-and-resource"></a>Widoku niestandardowego dostawcy i zasobów

W portalu dostawcy niestandardowego jest typem zasobu ukryte. Aby upewnić się, że dostawca zasobów został wdrożony, przejdź do grupy zasobów. Wybierz opcję, aby **Pokaż ukryte typy**.

![Pokaż ukryte typy zasobów](./media/create-custom-providers/show-hidden.png)

Aby wyświetlić typ zasobu niestandardowego, który został wdrożony, użyj operacji GET od typu zasobu.

```
GET https://management.azure.com/subscriptions/<sub-id>/resourceGroups/<rg-name>/providers/Microsoft.CustomProviders/resourceProviders/<provider-name>/users?api-version=2018-09-01-preview
```

Za pomocą ARMClient należy użyć:

```powershell
$subID = (Get-AzContext).Subscription.Id
$requestURI = "https://management.azure.com/subscriptions/$subID/resourceGroups/$rgName/providers/Microsoft.CustomProviders/resourceProviders/$funcName/users?api-version=2018-09-01-preview"

armclient GET $requestURI
```

Otrzymasz odpowiedzi:

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

## <a name="call-action"></a>Wywołaj akcję

Niestandardowego dostawcy ma również akcję o nazwie **ping**. Kod, który przetwarza żądanie jest wdrażany w aplikacji funkcji. Akcja ping w odpowiedzi pozdrowienia.

Aby wysłać żądanie ping, należy użyć operacji POST na niestandardowego dostawcy.

```
POST https://management.azure.com/subscriptions/<sub-id>/resourceGroups/<rg-name>/providers/Microsoft.CustomProviders/resourceProviders/<provider-name>/ping?api-version=2018-09-01-preview
```

Za pomocą ARMClient należy użyć:

```powershell
$pingURI = "https://management.azure.com/subscriptions/$subID/resourceGroups/$rgName/providers/Microsoft.CustomProviders/resourceProviders/$funcName/ping?api-version=2018-09-01-preview"

armclient POST $pingURI
```

Otrzymasz odpowiedzi:

```json
{
  "pingcontent": {
    "source": "<function-name>.azurewebsites.net"
  },
  "message": "hello <function-name>.azurewebsites.net"
}
```

## <a name="create-resource-type"></a>Utwórz typ zasobu

Aby utworzyć niestandardowy typ zasobu, można wdrożyć zasobów w szablonie. To podejście jest wyświetlany w szablonie, wdrożonego w ramach tego samouczka. Można również wysłać żądanie PUT dla typu zasobu.

```
PUT https://management.azure.com/subscriptions/<sub-id>/resourceGroups/<rg-name>/providers/Microsoft.CustomProviders/resourceProviders/<provider-name>/users/<resource-name>?api-version=2018-09-01-preview

{"properties":{"FullName": "Test User", "Location": "Earth"}}
```

Za pomocą ARMClient należy użyć:

```powershell
$addURI = "https://management.azure.com/subscriptions/$subID/resourceGroups/$rgName/providers/Microsoft.CustomProviders/resourceProviders/$funcName/users/testuser?api-version=2018-09-01-preview"
$requestBody = "{'properties':{'FullName': 'Test User', 'Location': 'Earth'}}"

armclient PUT $addURI $requestBody
```

Otrzymasz odpowiedzi:

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

## <a name="next-steps"></a>Kolejne kroki

Wprowadzenie do dostawców niestandardowych, zobacz [Azure niestandardowych dostawców (wersja zapoznawcza) — omówienie](custom-providers-overview.md).
