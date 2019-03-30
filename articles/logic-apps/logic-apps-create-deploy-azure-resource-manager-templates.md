---
title: Tworzenie aplikacji logiki przy użyciu szablonów usługi Azure Resource Manager — Azure Logic Apps | Dokumentacja firmy Microsoft
description: Tworzenie i wdrażanie przepływów pracy aplikacji logiki przy użyciu szablonów usługi Azure Resource Manager w usłudze Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: article
ms.assetid: 7574cc7c-e5a1-4b7c-97f6-0cffb1a5d536
ms.date: 10/15/2017
ms.openlocfilehash: 8ad70c5d22ca73258fa9e6501d03d5409a4e45d8
ms.sourcegitcommit: 22ad896b84d2eef878f95963f6dc0910ee098913
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2019
ms.locfileid: "58652488"
---
# <a name="create-and-deploy-logic-apps-with-azure-resource-manager-templates"></a>Tworzenie i wdrażanie aplikacji logiki przy użyciu szablonów usługi Azure Resource Manager

Usługa Azure Logic Apps udostępnia szablony usługi Azure Resource Manager, które są dostępne, nie tylko do tworzenia aplikacji logiki umożliwiająca automatyzację przepływów pracy, ale także do definiowania zasoby i parametry, które są używane do wdrażania.
Można użyć tego szablonu w scenariuszach biznesowych lub dostosować szablon do własnych wymagań. Dowiedz się więcej o [szablonu usługi Resource Manager dla usługi logic apps](https://github.com/Azure/azure-quickstart-templates/blob/master/101-logic-app-create/azuredeploy.json) i [strukturę szablonu usługi Azure Resource Manager i składnię](../azure-resource-manager/resource-group-authoring-templates.md). Składnię JSON i właściwości, zobacz [typów zasobów Microsoft.Logic](/azure/templates/microsoft.logic/allversions).

## <a name="define-the-logic-app"></a>Definiowanie aplikacji logiki
Definicji aplikacji logiki ten przykład jest uruchamiany co godzinę i wysyła pakiet usługi ping w lokalizacji określonej w `testUri` parametru.
Szablon używa wartości parametru Nazwa aplikacji logiki (```logicAppName```) oraz lokalizacji, na polecenie ping do testowania (```testUri```). Dowiedz się więcej o [definiowania tych parametrów w szablonie](#define-parameters).
Szablon spowoduje także ustawienie lokalizacji dla aplikacji logiki w tej samej lokalizacji co grupa zasobów platformy Azure.

```json
{
   "type": "Microsoft.Logic/workflows",
   "apiVersion": "2016-06-01",
   "name": "[parameters('logicAppName')]",
   "location": "[resourceGroup().location]",
   "tags": {
      "displayName": "LogicApp"
   },
   "properties": {
      "definition": {
         "$schema": "https://schema.management.azure.com/schemas/2016-06-01/Microsoft.Logic.json",
         "contentVersion": "1.0.0.0",
         "parameters": {
            "testURI": {
               "type": "string",
               "defaultValue": "[parameters('testUri')]"
            }
         },
         "triggers": {
            "Recurrence": {
               "type": "Recurrence",
               "recurrence": {
                  "frequency": "Hour",
                  "interval": 1
               }
            }
         },
         "actions": {
            "Http": {
              "type": "Http",
              "inputs": {
                  "method": "GET",
                  "uri": "@parameters('testUri')"
              },
              "runAfter": {}
           }
         },
         "outputs": {}
      },
      "parameters": {}
   }
}
```

<a name="define-parameters"></a>

### <a name="define-parameters"></a>Zdefiniuj parametry

[!INCLUDE [app-service-logic-deploy-parameters](../../includes/app-service-logic-deploy-parameters.md)]

Poniżej przedstawiono opisy parametrów w szablonie:

| Parametr | Opis | Przykład kodu JSON z definicji |
| --------- | ----------- | ----------------------- |
| `logicAppName` | Definiuje nazwę aplikacji logiki ten szablon umożliwia utworzenie. | "logicAppName": {"type": "string", "metadane": {"description": "myExampleLogicAppName"}} |
| `testUri` | Określa lokalizację, aby wykonać polecenie ping do testowania. | "testUri": {"type": "string", "defaultValue": "https://azure.microsoft.com/status/feed/"} |
||||

Dowiedz się więcej o [interfejsu API REST dla definicji przepływu pracy aplikacji logiki i właściwości](https://docs.microsoft.com/rest/api/logic/workflows) i [Tworzenie definicji aplikacji logiki za pomocą kodu JSON](logic-apps-author-definitions.md).

## <a name="deploy-logic-apps-automatically"></a>Automatyczne wdrażanie aplikacji logiki

Aby utworzyć i automatyczne wdrażanie aplikacji logiki na platformie Azure, wybierz **Wdróż na platformie Azure** tutaj:

[![Wdrażanie na platformie Azure](./media/logic-apps-create-deploy-azure-resource-manager-templates/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-logic-app-create%2Fazuredeploy.json)

Ta akcja loguje Cię witrynie Azure Portal, w którym można podać szczegóły aplikacji logiki i wprowadź zmiany do szablonu lub parametrów.
Na przykład witryny Azure portal wyświetli monit o podanie tych szczegółów:

* Nazwa subskrypcji platformy Azure
* Grupy zasobów, której chcesz użyć
* Lokalizacja aplikacji logiki
* Nazwa aplikacji logiki
* Test identyfikatora URI
* Akceptacja określonego warunków i postanowień

## <a name="deploy-logic-apps-with-commands"></a>Wdrażanie aplikacji logiki za pomocą poleceń

[!INCLUDE [app-service-deploy-commands](../../includes/app-service-deploy-commands.md)]

### <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

```powershell
New-AzResourceGroupDeployment -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-logic-app-create/azuredeploy.json -ResourceGroupName ExampleDeployGroup
```

### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

```azurecli
azure group deployment create --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-logic-app-create/azuredeploy.json -g ExampleDeployGroup
```

## <a name="get-support"></a>Uzyskiwanie pomocy technicznej

* Jeśli masz pytania, odwiedź [forum usługi Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Aby przesłać pomysły dotyczące funkcji lub zagłosować na nie, odwiedź [witrynę opinii użytkowników usługi Logic Apps](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Monitorowanie aplikacji logiki](../logic-apps/logic-apps-monitor-your-logic-apps.md)
