---
title: Tworzenie szablonów wdrażania w usłudze Azure Logic Apps | Dokumentacja firmy Microsoft
description: Tworzenie szablonów usługi Azure Resource Manager do wdrażania aplikacji logiki
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: article
ms.assetid: 85928ec6-d7cb-488e-926e-2e5db89508ee
ms.date: 10/18/2016
ms.openlocfilehash: 624539557b0bf57e9d919a3a46337f1cf93a4f07
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "62128604"
---
# <a name="create-azure-resource-manager-templates-for-deploying-logic-apps"></a>Tworzenie szablonów usługi Azure Resource Manager do wdrażania aplikacji logiki

Podczas tworzenia aplikacji logiki można rozwinąć definicji aplikacji logiki do [szablonu usługi Azure Resource Manager](../azure-resource-manager/resource-group-overview.md). Następnie możesz użyć tego szablonu do automatyzowania wdrożeń przez definiowanie zasobów i parametrów, który ma być użyty do wdrożenia i podając wartości parametru za pomocą [pliku parametrów](../azure-resource-manager/resource-group-template-deploy.md#parameter-files).
W ten sposób możesz wdrożyć aplikacje logiki łatwiej i na dowolnym środowiskiem lub grupy zasobów platformy Azure. 

Usługa Azure Logic Apps udostępnia [szablon usługi Azure Resource Manager aplikacji logiki ze wstępnie utworzonych](https://github.com/Azure/azure-quickstart-templates/blob/master/101-logic-app-create/azuredeploy.json) możesz użyć, nie tylko w przypadku tworzenia aplikacji logiki, ale także do definiowania zasoby i parametry do zastosowania podczas wdrażania. Można użyć tego szablonu w scenariuszach biznesowych lub dostosować szablon do własnych wymagań. Dowiedz się więcej o [strukturę szablonu usługi Resource Manager i składnię](../azure-resource-manager/resource-group-authoring-templates.md). Składnię JSON i właściwości, zobacz [typów zasobów Microsoft.Logic](/azure/templates/microsoft.logic/allversions).

Aby uzyskać więcej informacji o szablonach usługi Azure Resource Manager zobacz następujące artykuły:

* [Tworzenie szablonów usługi Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md)
* [Opracowywanie szablonów usługi Azure Resource Manager pozwalających zachować spójność w chmurze](../azure-resource-manager/templates-cloud-consistency.md)

## <a name="logic-app-structure"></a>Struktura aplikacji logiki

Definicji aplikacji logiki ma te podstawowe części, w których można wyświetlić, przełączając się od "Widok projektanta" do "widoku kodu" lub przy użyciu narzędzia, takie jak [Eksploratora zasobów Azure](http://resources.azure.com). Definicji aplikacji logiki Javascript Object Notation (JSON), tak aby uzyskać więcej informacji na temat składni JSON i właściwości, zobacz [typów zasobów Microsoft.Logic](/azure/templates/microsoft.logic/allversions).

* **Zasobu aplikacji logiki**: W tym artykule opisano informacje, takie jak Twoja aplikacja logiki lokalizacji lub regionu, ceny planu i definicji przepływu pracy.

* **Definicja przepływu pracy**: W tym artykule opisano, aplikacja logiki wyzwalaczy i akcji oraz jak usługa Azure Logic Apps uruchamia przepływ pracy. W widoku kodu, można znaleźć definicji przepływu pracy w `definition` sekcji.

* **Połączenia**: Jeśli używasz zarządzanych łączników w aplikacji logiki, `$connections` sekcji odwołuje się do innych zasobów, które są bezpiecznie przechowywane metadane dotyczące tych połączeń między swoją aplikację logiki i innych systemów lub usług, takich jak parametry połączenia i tokeny dostępu. Wewnątrz definicji aplikacji logiki, odwołania do tych połączeń pojawiają się wewnątrz definicji aplikacji logiki `parameters` sekcji.

Aby utworzyć szablon aplikacji logiki, który można używać z wdrożeniami grup zasobów platformy Azure, możesz zdefiniować zasoby i sparametryzuj zgodnie z potrzebami. Na przykład Jeżeli wdrażasz rozwoju, testowania i środowiska produkcyjnego, prawdopodobnie chcesz użyć ciągów innego połączenia z bazą danych SQL w każdym środowisku.
Możesz też wdrożyć w ramach różnych subskrypcji lub grupy zasobów.

## <a name="create-logic-app-deployment-templates"></a>Tworzenie szablonów wdrażania aplikacji logiki

Najprostszym sposobem utworzenia logiki prawidłowy szablon wdrożenia aplikacji użytku programu Visual Studio i narzędzia Azure Logic Apps Tools dla rozszerzenia programu Visual Studio. Pobierając aplikację logiki w witrynie Azure portal w programie Visual Studio, otrzymasz szablon prawidłowe wdrożenie, który można użyć z dowolną subskrypcją platformy Azure i lokalizacji. Ponadto automatyczne pobieranie aplikacji logiki parametryzuje dane definicji aplikacji logiki, który jest osadzony w szablonie.
Aby uzyskać więcej informacji na temat tworzenia i zarządzanie aplikacjami logiki w programie Visual Studio, zobacz [tworzenie aplikacji logiki za pomocą programu Visual Studio](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md) i [Zarządzanie aplikacjami logiki w programie Visual Studio](../logic-apps/manage-logic-apps-with-visual-studio.md).

Inne niż Visual Studio lub ręcznego tworzenia szablonu i niezbędne parametry, postępując zgodnie ze wskazówkami w tym temacie, można również użyć [modułu programu PowerShell do tworzenia szablonów aplikacji logiki](https://github.com/jeffhollan/LogicAppTemplateCreator). Ten moduł typu open-source najpierw ocenia swoją aplikację logiki i połączeń, których używa aplikacja logiki. Moduł następnie generuje zasobów szablonu z wymaganymi parametrami dla wdrożenia. Na przykład załóżmy, że masz aplikację logiki, która otrzymuje komunikat z kolejki usługi Azure Service Bus i dodaje dane do usługi Azure SQL database. Narzędzie modułu zachowuje całą logikę aranżacji i parametryzuje dane parametry połączenia SQL i usługi Service Bus, aby te wartości można ustawiać na poziomie wdrożenia.

> [!IMPORTANT]
> Połączenia muszą istnieć w tej samej grupie zasobów platformy Azure, co aplikacja logiki.
> Dla modułu programu PowerShell do pracy z dowolnym dzierżawę i subskrypcję access token, korzystanie z platformy Azure moduł za pomocą [narzędzie klienta usługi Azure Resource Manager](https://github.com/projectkudu/ARMClient). Aby uzyskać więcej informacji, zobacz ten [artykuł na temat narzędzia klienta usługi Azure Resource Manager](https://blog.davidebbo.com/2015/01/azure-resource-manager-client.html) w tym artykule omówiono ARMClient bardziej szczegółowo.

### <a name="install-powershell-module-for-logic-app-templates"></a>Zainstaluj moduł programu PowerShell dla szablonów aplikacji logiki

Aby uzyskać Najprostszym sposobem zainstalowania modułu na podstawie [galerii programu PowerShell](https://www.powershellgallery.com/packages/LogicAppTemplate/0.1), użyj tego polecenia:

`Install-Module -Name LogicAppTemplate`

Można też ręcznie zainstalować moduł programu PowerShell:

1. Pobierz najnowszy [twórcę szablonu aplikacji logiki](https://github.com/jeffhollan/LogicAppTemplateCreator/releases).

1. Wyodrębnij folder w folderze modułu programu PowerShell, który zazwyczaj znajduje się `%UserProfile%\Documents\WindowsPowerShell\Modules`.

### <a name="generate-logic-app-template---powershell"></a>Generuj szablon aplikacji logiki — PowerShell

Po zainstalowaniu programu PowerShell, można wygenerować szablonu przy użyciu następującego polecenia:

`armclient token $SubscriptionId | Get-LogicAppTemplate -LogicApp MyApp -ResourceGroup MyRG -SubscriptionId $SubscriptionId -Verbose | Out-File C:\template.json`

`$SubscriptionId` jest to identyfikator subskrypcji platformy Azure. Ten wiersz najpierw pobiera token za pomocą ARMClient, dostępu, a następnie przekazuje ją przy użyciu skryptu programu PowerShell, a następnie tworzy szablon w pliku JSON.

## <a name="parameters-in-logic-app-templates"></a>Parametry w szablony aplikacji logiki

Po utworzeniu szablonu aplikacji logiki, można dodawać i edytować wszystkie niezbędne parametry. Szablon ma więcej niż jedną `parameters` sekcji, na przykład: 

* Definicja przepływu pracy aplikacji logiki ma swój własny [ `parameters` sekcji](../logic-apps/logic-apps-workflow-definition-language.md#parameters) gdzie można zdefiniować parametry, których Twoja aplikacja logiki używa do akceptowania dane wejściowe podczas wdrażania.

* Szablon usługi Resource Manager ma swój własny [ `parameters` sekcji](../azure-resource-manager/resource-group-authoring-templates.md#parameters), niezależnie od aplikacji logiki `parameters` sekcji. Na przykład:

  [!INCLUDE [logic-deploy-parameters](../../includes/app-service-logic-deploy-parameters.md)]

Na przykład załóżmy, że identyfikator zasobu, który reprezentuje funkcję platformy Azure lub przepływu pracy aplikacji logiki zagnieżdżonych odwołuje się do definicji aplikacji logiki i chcesz przeprowadzić wdrożenie tego Identyfikatora zasobu, wraz z aplikacją logiki jako pojedyncze wdrożenie. Możesz dodać ten identyfikator jako zasób do szablonu i sparametryzuj tego identyfikatora. Można użyć tej samej metody do odwoływania się do punktów końcowych niestandardowych interfejsów API lub interfejsu OpenAPI (dawniej "Swagger") czy mają zostać wdrożone z każdą grupą zasobów platformy Azure.

Gdy używane są parametry w szablonie wdrożenia, dzięki czemu Projektant aplikacji logiki można poprawnego wyświetlania tych parametrów, należy kierować następującymi wskazówkami:

* Użyj parametrów tylko w tych wyzwalaczy i akcji:

  * Aplikację usługi Azure Functions
  * Zagnieżdżone lub przepływu pracy aplikacji logiki podrzędne
  * Wywołanie usługi API Management
  * Adres URL środowiska uruchomieniowego połączenia interfejsu API
  * Ścieżka połączenia interfejsu API

* Podczas definiowania parametrów, upewnij się, możesz podać wartości domyślnej przy użyciu `defaultValue` wartość właściwości, na przykład:

  ```json
  "parameters": {
     "IntegrationAccount": {
        "type":"string",
        "minLength": 1,
        "defaultValue": "/subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource=group-name>/providers/Microsoft.Logic/integrationAccounts/<integration-account-name>"
     }
  },
  ```

* Aby zabezpieczyć lub ukryć poufnych informacji w szablonach, Zabezpiecz parametry. Dowiedz się więcej o [sposób używania parametrów zabezpieczonej](../logic-apps/logic-apps-securing-a-logic-app.md#secure-parameters-workflow).

Oto przykład pokazujący, jak można zdefiniować parametry akcji "Wyślij wiadomość" usługi Azure Service Bus:

```json
"Send_message": {
   "type": "ApiConnection",
   "inputs": {
      "host": {
         "connection": {
            "name": "@parameters('$connections')['servicebus']['connectionId']"
         },
         // If the `host.runtimeUrl` property appears in your template, 
         // you can remove this property, which is optional, for example:
         "runtimeUrl": {}
      },
      "method": "POST",
      "path": "[concat('/@{encodeURIComponent(''', parameters('<Azure-Service-Bus-queue-name>'), ''')}/messages')]",
      "body": {
         "ContentData": "@{base64(triggerBody())}"
      },
      "queries": {
         "systemProperties": "None"
      }
   },
   "runAfter": {}
},
```

### <a name="reference-dependent-resources"></a>Zasoby zależne odwołania

Jeśli Twoja aplikacja logiki wymaga odwołania do zasobów zależnych, możesz użyć [funkcje szablonu usługi Azure Resource Manager](../azure-resource-manager/resource-group-template-functions.md) w szablonie wdrożenia aplikacji logiki. Na przykład załóżmy, że chcesz, aby Twoja aplikacja logiki, aby odwoływać się do funkcji platformy Azure lub konto integracji z definicji dla partnerów, umów i innych artefaktów, które mają wdrożone wraz z aplikacją logiki.
Można użyć funkcji szablonu usługi Resource Manager, takie jak `parameters`, `variables`, `resourceId`, `concat`i tak dalej.

Oto przykład pokazujący, jak można zastąpić identyfikator zasobu dla funkcji platformy Azure, definiując te parametry:

``` json
"parameters": {
   "<Azure-function-name>": {
      "type": "string",
      "minLength": 1,
      "defaultValue": "<Azure-function-name>"
   }
},
```

Poniżej przedstawiono, jak użyć tych parametrów podczas odwoływania się do funkcji platformy Azure:

```json
"MyFunction": {
   "type": "Function",
   "inputs": {
      "body": {},
      "function": {
         "id":"[resourceid('Microsoft.Web/sites/functions','<Azure-Functions-app-name>', parameters('<Azure-function-name>'))]"
      }
   },
   "runAfter": {}
},
```

## <a name="add-logic-app-to-resource-group-project"></a>Dodawanie aplikacji logiki do projektu grupy zasobów

Jeśli masz istniejący projekt grupy zasobów platformy Azure, można dodać aplikacji logiki do tego projektu, przy użyciu okna konspekt pliku JSON. Można również dodać inną aplikację logiki, wraz z wcześniej utworzoną aplikację.

1. W Eksploratorze rozwiązań Otwórz `<template>.json` pliku.

2. Z **widoku** menu, wybierz opcję **Windows inne** > **konspekt pliku JSON**.

3. Można dodać zasobu do pliku szablonu, wybierz **Dodaj zasób** u góry okna konspekt pliku JSON. W oknie Konspekt pliku JSON, kliknij prawym przyciskiem myszy **zasobów**i wybierz **Dodaj nowy zasób**.

   ![Okna konspekt pliku JSON](./media/logic-apps-create-deploy-template/jsonoutline.png)

4. W **Dodaj zasób** okno dialogowe, Znajdź i wybierz **aplikacji logiki**. Nadaj nazwę aplikacji logiki, a następnie wybierz **Dodaj**.

   ![Dodaj zasób](./media/logic-apps-create-deploy-template/addresource.png)

## <a name="get-support"></a>Uzyskiwanie pomocy technicznej

Jeśli masz pytania, odwiedź [forum usługi Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Wdrażanie szablonów aplikacji logiki](../logic-apps/logic-apps-create-deploy-azure-resource-manager-templates.md)
