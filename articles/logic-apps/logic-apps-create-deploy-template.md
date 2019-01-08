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
ms.openlocfilehash: 8714c91079d087b16a62af4af8b381edf6871f8b
ms.sourcegitcommit: 3ab534773c4decd755c1e433b89a15f7634e088a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/07/2019
ms.locfileid: "54065736"
---
# <a name="create-azure-resource-manager-templates-for-deploying-logic-apps"></a>Tworzenie szablonów usługi Azure Resource Manager do wdrażania aplikacji logiki

Po utworzeniu aplikacji logiki można utworzyć jako szablonu usługi Azure Resource Manager.
W ten sposób aplikacja logiki można łatwo wdrożyć na dowolnym środowiskiem lub grupy zasobów, w którym możesz go potrzebować.
Aby uzyskać więcej informacji o szablonach usługi Resource Manager, zobacz [Tworzenie szablonów usługi Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md) i [wdrażanie zasobów za pomocą szablonów usługi Azure Resource Manager](../azure-resource-manager/resource-group-template-deploy.md).

## <a name="logic-app-deployment-template"></a>Szablon wdrożenia aplikacji logiki

Aplikacja logiki ma trzy podstawowe składniki:

* **Zasobu aplikacji logiki**: Zawiera informacje dotyczące np. ceny planu, lokalizację i definicji przepływu pracy.
* **Definicja przepływu pracy**: W tym artykule opisano kroki dotyczące przepływu pracy aplikacji logiki i jak aparat usługi Logic Apps powinien zostać wykonany przepływ pracy.
Ta definicja można wyświetlić w aplikacji logiki **widok kodu** okna.
W zasobie aplikacji logiki możesz znaleźć tę definicję w `definition` właściwości.
* **Połączenia**: Odnosi się do rozdzielenia zasobów, które są bezpiecznie przechowywane metadane dotyczące wszystkie połączenia łącznika, takie jak parametry połączenia i token dostępu.
W zasobie aplikacji logiki, aplikację logiki odwołuje się do tych zasobów `parameters` sekcji.

Wszystkie te fragmenty istniejących aplikacji logiki można wyświetlić za pomocą narzędzia, takiego jak [Eksploratora zasobów Azure](http://resources.azure.com). Składnię JSON i właściwości, zobacz [typów zasobów Microsoft.Logic](/azure/templates/microsoft.logic/allversions).

Aby szablon aplikacji logiki do używania z wdrożeniami grup zasobów, należy zdefiniować zasoby i sparametryzuj zgodnie z potrzebami.
Na przykład Jeżeli wdrażasz rozwoju, testowania i środowiska produkcyjnego, prawdopodobnie chcesz użyć ciągów innego połączenia z bazą danych SQL w każdym środowisku.
Możesz też wdrożyć w ramach różnych subskrypcji lub grupy zasobów.  

## <a name="create-a-logic-app-deployment-template"></a>Tworzenie szablonu wdrożenia aplikacji logiki

Najprostszym sposobem mają logiki prawidłowy szablon wdrożenia aplikacji jest użycie [Visual Studio Tools for Logic Apps](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md#prerequisites).
Narzędzia programu Visual Studio wygenerowanie szablonu prawidłowe wdrożenie, które można stosować w dowolnej subskrypcji lub lokalizacji.

Niektóre inne narzędzia mogą pomóc podczas tworzenia szablonu wdrożenia aplikacji logiki.
Można tworzyć ręcznie, oznacza to, korzystając z zasobów już omówionego tutaj, aby utworzyć parametry zgodnie z potrzebami.
Innym rozwiązaniem jest użycie [twórcę szablonu aplikacji logiki](https://github.com/jeffhollan/LogicAppTemplateCreator) modułu programu PowerShell. Ten moduł typu open-source ocenia najpierw aplikacji logiki i połączeń, że jej używa, a następnie generuje zasobów szablonu z wymaganymi parametrami dla wdrożenia.
Na przykład jeśli masz aplikację logiki, która otrzymuje komunikat z kolejki usługi Azure Service Bus i dodaje dane do usługi Azure SQL database, narzędzie zachowuje całą logikę aranżacji i parametryzuje dane połączenie SQL i usługi Service Bus, które ciągów, aby można je skonfigurować na wdrażanie Dzięki niemu.

> [!NOTE]
> Połączenia muszą być w tej samej grupie zasobów aplikacji logiki.
>
>

### <a name="install-the-logic-app-template-powershell-module"></a>Zainstaluj moduł PowerShell szablonu aplikacji logiki
Najprostszym sposobem zainstalowania modułu jest za pośrednictwem [galerii programu PowerShell](https://www.powershellgallery.com/packages/LogicAppTemplate/0.1), za pomocą polecenia `Install-Module -Name LogicAppTemplate`.  

Możesz także zainstalować moduł PowerShell ręcznie:

1. Pobierz najnowszą wersję [twórcę szablonu aplikacji logiki](https://github.com/jeffhollan/LogicAppTemplateCreator/releases).  
2. Wyodrębnij folder w folderze modułu programu PowerShell (zazwyczaj `%UserProfile%\Documents\WindowsPowerShell\Modules`).

Dla modułu do pracy z dowolnym dzierżawę i subskrypcję access token, firma Microsoft zaleca korzystanie z [ARMClient](https://github.com/projectkudu/ARMClient) narzędzie wiersza polecenia.  To [wpis w blogu](http://blog.davidebbo.com/2015/01/azure-resource-manager-client.html) w tym artykule omówiono ARMClient bardziej szczegółowo.

### <a name="generate-a-logic-app-template-by-using-powershell"></a>Generuj szablon aplikacji logiki przy użyciu programu PowerShell
Po zainstalowaniu programu PowerShell, można wygenerować szablonu przy użyciu następującego polecenia:

`armclient token $SubscriptionId | Get-LogicAppTemplate -LogicApp MyApp -ResourceGroup MyRG -SubscriptionId $SubscriptionId -Verbose | Out-File C:\template.json`

`$SubscriptionId` jest to identyfikator subskrypcji platformy Azure. Ten wiersz najpierw pobiera token za pomocą ARMClient, dostępu, a następnie przekazuje ją przy użyciu skryptu programu PowerShell, a następnie tworzy szablon w pliku JSON.

## <a name="add-parameters-to-a-logic-app-template"></a>Dodawanie parametrów do szablonu aplikacji logiki
Po utworzeniu szablonu aplikacji logiki, można dodać ani zmodyfikować parametrów, które mogą wymagać. Na przykład jeśli Twoja definicja zawiera Identyfikatora zasobu do funkcji platformy Azure lub zagnieżdżony przepływ pracy, który ma zostać umieszczony w jednym wdrożeniu, możesz dodać więcej zasobów do szablonu i sparametryzuj identyfikatory zgodnie z potrzebami. To samo dotyczy wszelkie odwołania do niestandardowych interfejsów API struktury Swagger punktów końcowych, które chcesz wdrożyć z każdą grupą zasobów.

### <a name="add-references-for-dependent-resources-to-visual-studio-deployment-templates"></a>Dodaj odwołania do zasobów zależnych w szablonach wdrożenia programu Visual Studio

Jeśli chcesz, aby Twoja aplikacja logiki, aby odwołać się zasoby zależne, możesz użyć [funkcje szablonu usługi Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-functions) w szablonie wdrożenia aplikacji logiki. Na przykład możesz zechcieć aplikację logiki, aby odwoływać się do konta funkcji platformy Azure lub integracji, które mają zostać wdrożone wraz z aplikacją logiki. Należy przestrzegać następujących wytycznych, o tym, jak używać parametrów w szablonie wdrożenia, aby poprawnie renderowany w Projektancie aplikacji logiki. 

Można używać parametrów aplikacji logiki w tego rodzaju wyzwalacze i akcje:

*   Podrzędny przepływ pracy
*   Aplikacja funkcji
*   Wywołanie usługi APIM
*   Adres URL środowiska uruchomieniowego połączenia interfejsu API
*   Ścieżka połączenia interfejsu API

I korzystać z funkcji szablonu, takich jak parametry, zmienne, resourceId, concat, itp. Na przykład Oto jak można zastąpić identyfikator zasobu funkcji platformy Azure:

```
"parameters":{
    "functionName": {
        "type":"string",
        "minLength":1,
        "defaultValue":"<FunctionName>"
    }
},
```

I użycia parametrów:

```
"MyFunction": {
    "type": "Function",
    "inputs": {
        "body":{},
        "function":{
            "id":"[resourceid('Microsoft.Web/sites/functions','functionApp',parameters('functionName'))]"
        }
    },
    "runAfter":{}
}
```
Inny przykład można zdefiniować parametry operację wysyłania komunikatu usługi Service Bus:

```
"Send_message": {
    "type": "ApiConnection",
        "inputs": {
            "host": {
                "connection": {
                    "name": "@parameters('$connections')['servicebus']['connectionId']"
                }
            },
            "method": "post",
            "path": "[concat('/@{encodeURIComponent(''', parameters('queueuname'), ''')}/messages')]",
            "body": {
                "ContentData": "@{base64(triggerBody())}"
            },
            "queries": {
                "systemProperties": "None"
            }
        },
        "runAfter": {}
    }
```
> [!NOTE] 
> host.runtimeUrl jest opcjonalna i może zostać usunięty z szablonu, jeśli jest obecny.
> 


> [!NOTE] 
> Projektant aplikacji logiki w przypadku użycia parametrów trzeba podać wartości domyślnych, na przykład:
> 
> ```
> "parameters": {
>     "IntegrationAccount": {
>     "type":"string",
>     "minLength":1,
>     "defaultValue":"/subscriptions/<subscriptionID>/resourceGroups/<resourceGroupName>/providers/Microsoft.Logic/integrationAccounts/<integrationAccountName>"
>     }
> },
> ```

## <a name="add-your-logic-app-to-an-existing-resource-group-project"></a>Dodawanie aplikacji logiki do istniejącego projektu grupy zasobów

Jeśli masz istniejący projekt grupy zasobów, można dodać aplikacji logiki do tego projektu w oknie Konspekt pliku JSON. Można również dodać inną aplikację logiki, wraz z wcześniej utworzoną aplikację.

1. Otwórz plik `<template>.json`.

2. Aby otworzyć okno konspekt pliku JSON, przejdź do **widoku** > **Windows inne** > **konspekt pliku JSON**.

3. Można dodać zasobu do pliku szablonu, kliknij przycisk **Dodaj zasób** u góry okna konspekt pliku JSON. W oknie Konspekt pliku JSON, kliknij prawym przyciskiem myszy **zasobów**i wybierz **Dodaj nowy zasób**.

    ![Okna konspekt pliku JSON](./media/logic-apps-create-deploy-template/jsonoutline.png)
    
4. W **Dodaj zasób** okno dialogowe, Znajdź i wybierz **aplikacji logiki**. Nadaj nazwę aplikacji logiki, a następnie wybierz **Dodaj**.

    ![Dodaj zasób](./media/logic-apps-create-deploy-template/addresource.png)


## <a name="deploy-a-logic-app-template"></a>Wdrażanie szablonu aplikacji logiki

Szablonu można wdrożyć przy użyciu dowolnych narzędzi, takich jak PowerShell, interfejsu API REST, [potoki usługi Azure DevOps platformy Azure](#team-services)i wdrażanie szablonu przy użyciu witryny Azure portal.
Ponadto do przechowywania wartości parametrów, firma Microsoft zaleca utworzenie [plik parametrów](../azure-resource-manager/resource-group-template-deploy.md#parameter-files).
Dowiedz się, jak [wdrażanie zasobów za pomocą szablonów usługi Azure Resource Manager i programu PowerShell](../azure-resource-manager/resource-group-template-deploy.md) lub [wdrażanie zasobów za pomocą szablonów usługi Azure Resource Manager i witryny Azure portal](../azure-resource-manager/resource-group-template-deploy-portal.md).

### <a name="authorize-oauth-connections"></a>Autoryzowania połączeń protokołu OAuth

Po wdrożeniu aplikacja logiki działa end-to-end przy użyciu prawidłowych parametrów.
Należy jednak nadal autoryzować połączenia protokołu OAuth do wygenerowania tokenu dostępu nie jest ważna.
Do autoryzowania połączeń protokołu OAuth, Otwórz aplikację logiki w Projektancie aplikacji logiki i autoryzować tych połączeń. Lub dla automatycznego wdrażania, możesz użyć skryptu do wyrażenia zgody na każde połączenie protokołu OAuth.
Brak przykładowy skrypt w usłudze GitHub w ramach [LogicAppConnectionAuth](https://github.com/logicappsio/LogicAppConnectionAuth) projektu.

<a name="team-services"></a>
## <a name="azure-devops-azure-pipelines"></a>Potoki usługi Azure DevOps platformy Azure

Typowy scenariusz wdrażania i zarządzania nimi w środowisku jest za pomocą narzędzia, takiego jak Azure potoków w DevOps platformy Azure, Szablon wdrożenia aplikacji logiki. Obejmuje usługi Azure DevOps [wdrażanie grupy zasobów platformy Azure](https://github.com/Microsoft/azure-pipelines-tasks/tree/master/Tasks/AzureResourceGroupDeploymentV2) zadań, można dodać do dowolnej kompilacji lub potoku wydania. Musisz mieć [nazwy głównej usługi](https://blogs.msdn.microsoft.com/visualstudioalm/2015/10/04/automating-azure-resource-group-deployment-using-a-service-principal-in-visual-studio-online-buildrelease-management/) dla autoryzacji do wdrożenia, a następnie można wygenerować potoku tworzenia wersji.

1. W potokach Azure wybierz **pusty** tak, aby utworzyć pustym potoku.

    ![Tworzenie pustego potoku][1]

2. Wybierz wszystkie zasoby potrzebne w tym celu najprawdopodobniej łącznie z szablonu aplikacji logiki, który jest generowany, ręcznie lub jako część procesu kompilacji.
3. Dodaj **wdrożenie grupy zasobów Azure** zadania.
4. Konfigurowanie przy użyciu [nazwy głównej usługi](https://blogs.msdn.microsoft.com/visualstudioalm/2015/10/04/automating-azure-resource-group-deployment-using-a-service-principal-in-visual-studio-online-buildrelease-management/)i odwoływać się do plików szablonu oraz parametrów szablonu.
5. Nadal nad kroki procesu tworzenia wersji dla dowolnego środowiska, testów automatycznych lub osób zatwierdzających, zgodnie z potrzebami.

<!-- Image References -->
[1]: ./media/logic-apps-create-deploy-template/emptyreleasedefinition.png
