---
title: Tworzenie aplikacji logiki z szablonów usługi Azure Resource Manager | Dokumentacja firmy Microsoft
description: Tworzenie i wdrażanie przepływów pracy aplikacji logiki z szablonów usługi Azure Resource Manager
services: logic-apps
documentationcenter: ''
author: ecfan
manager: anneta
editor: ''
ms.assetid: 7574cc7c-e5a1-4b7c-97f6-0cffb1a5d536
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/15/2017
ms.author: LADocs; estfan
ms.openlocfilehash: 69be968f34d756cc725f5353dcf074c01fad65ce
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/03/2018
---
# <a name="create-and-deploy-logic-apps-with-azure-resource-manager-templates"></a>Tworzenie i wdrażanie aplikacji logiki z szablonów usługi Azure Resource Manager

Aplikacje logiki platformy Azure udostępnia szablony usługi Azure Resource Manager, które są dostępne, nie tylko do tworzenia aplikacji logiki do automatyzacji przepływy pracy, ale także do definiowania zasobów i parametrów, które są używane do wdrożenia. Można użyć tego szablonu dla scenariuszy biznesowych lub dostosować szablon zgodnie z wymaganiami. Dowiedz się więcej o [szablonu usługi Resource Manager dla usługi logic apps](https://github.com/Azure/azure-quickstart-templates/blob/master/101-logic-app-create/azuredeploy.json) i [struktury szablonu usługi Azure Resource Manager i składni](../azure-resource-manager/resource-group-authoring-templates.md).

## <a name="define-the-logic-app"></a>Definiowanie aplikacji logiki

Definicję aplikacji logiki ten przykład jest uruchamiana raz na godzinę, a następnie wysyła pakiet usługi ping do lokalizacji określonej w `testUri` parametru.
Szablon używa wartości parametrów dla nazwy aplikacji logiki (```logicAppName```) oraz lokalizację na polecenie ping do testowania (```testUri```). Dowiedz się więcej o [definiowania tych parametrów w szablonie](#define-parameters). Szablon ustawia również lokalizacji dla aplikacji logiki w tej samej lokalizacji co grupa zasobów platformy Azure. 

``` json
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

| Parametr | Opis | Przykład definicji JSON | 
| --------- | ----------- | ----------------------- | 
| `logicAppName` | Określa nazwę aplikacji logiki tworzy tego szablonu. | "logicAppName": {"type": "string", "metadanych": {"opis": "myExampleLogicAppName"}} |
| `testUri` | Określa lokalizację na polecenie ping do testowania. | "testUri": {"type": "string", "defaultValue": "http://azure.microsoft.com/status/feed/"} | 
||||

Dowiedz się więcej o [interfejsu API REST dla definicji przepływu pracy aplikacji logiki i właściwości](https://docs.microsoft.com/rest/api/logic/workflows) i [korzystając z definicjami aplikacji logiki z JSON](logic-apps-author-definitions.md).

## <a name="deploy-logic-apps-automatically"></a>Automatyczne wdrażanie aplikacji logiki

Aby utworzyć i automatyczne wdrażanie aplikacji logiki do platformy Azure, wybierz **wdrażanie na platformie Azure** tutaj:

[![Wdrażanie na platformie Azure](./media/logic-apps-create-deploy-azure-resource-manager-templates/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-logic-app-create%2Fazuredeploy.json)

Ta akcja loguje się użytkownik do portalu Azure, którym można podać szczegóły aplikacji logiki i wprowadzić zmiany w szablonie lub parametrów. Na przykład portalu Azure wyświetla monit o podanie tych szczegółów:

* Nazwa subskrypcji platformy Azure
* Grupy zasobów, która ma być używany
* Lokalizacja aplikacji logiki
* Nazwa aplikacji logiki
* Identyfikator URI testu
* Akceptacji określonych warunków i postanowień

## <a name="deploy-logic-apps-with-commands"></a>Wdrażanie aplikacji logiki przy użyciu poleceń

[!INCLUDE [app-service-deploy-commands](../../includes/app-service-deploy-commands.md)]

### <a name="powershell"></a>PowerShell

```
New-AzureRmResourceGroupDeployment -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-logic-app-create/azuredeploy.json -ResourceGroupName ExampleDeployGroup
``` 

### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

```
azure group deployment create --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-logic-app-create/azuredeploy.json -g ExampleDeployGroup
```

## <a name="get-support"></a>Uzyskiwanie pomocy technicznej

* Jeśli masz pytania, odwiedź [forum usługi Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Aby przesłać pomysły dotyczące funkcji lub zagłosować na nie, odwiedź [witrynę opinii użytkowników usługi Logic Apps](http://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Monitorowanie aplikacji logiki](../logic-apps/logic-apps-monitor-your-logic-apps.md)