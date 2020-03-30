---
title: Tworzenie szablonów aplikacji logiki do wdrożenia
description: Dowiedz się, jak tworzyć szablony usługi Azure Resource Manager do automatyzacji wdrażania w usłudze Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 07/26/2019
ms.openlocfilehash: 1fdee9a5d90fc065e198d880f9d0dea10804b881
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75972647"
---
# <a name="create-azure-resource-manager-templates-to-automate-deployment-for-azure-logic-apps"></a>Tworzenie szablonów usługi Azure Resource Manager w celu automatyzacji wdrażania aplikacji logiki azure

Aby ułatwić automatyzację tworzenia i wdrażania aplikacji logiki, w tym artykule opisano sposoby tworzenia [szablonu usługi Azure Resource Manager](../azure-resource-manager/management/overview.md) dla aplikacji logiki. Aby uzyskać omówienie struktury i składni szablonu zawierającego definicję przepływu pracy i inne zasoby niezbędne do wdrożenia, zobacz [Omówienie: Automatyzacja wdrażania aplikacji logiki za pomocą szablonów usługi Azure Resource Manager](logic-apps-azure-resource-manager-templates-overview.md).

Usługa Azure Logic Apps udostępnia [wstępnie utworzony szablon usługi Azure Resource Manager aplikacji logiki,](https://github.com/Azure/azure-quickstart-templates/blob/master/101-logic-app-create/azuredeploy.json) który można ponownie użyć, nie tylko do tworzenia aplikacji logiki, ale także do definiowania zasobów i parametrów do użycia do wdrożenia. Możesz użyć tego szablonu dla własnych scenariuszy biznesowych lub dostosować szablon do własnych wymagań.

> [!IMPORTANT]
> Upewnij się, że połączenia w szablonie używają tej samej grupy zasobów platformy Azure i lokalizacji, co aplikacja logiki.

Aby uzyskać więcej informacji o szablonach usługi Azure Resource Manager, zobacz następujące tematy:

* [Struktura i składnia szablonów usługi Azure Resource Manager](../azure-resource-manager/templates/template-syntax.md)
* [Szablony usługi Azure Resource Manager autora](../azure-resource-manager/templates/template-syntax.md)
* [Opracowywanie szablonów usługi Azure Resource Manager pozwalających zachować spójność w chmurze](../azure-resource-manager/templates/templates-cloud-consistency.md)

<a name="visual-studio"></a>

## <a name="create-templates-with-visual-studio"></a>Tworzenie szablonów za pomocą programu Visual Studio

Aby uzyskać najprostszy sposób tworzenia prawidłowych szablonów aplikacji logiki sparametryzowanej, które są w większości gotowe do wdrożenia, użyj programu Visual Studio (bezpłatna wersja społeczności lub większa) i narzędzia Azure Logic Apps Tools for Visual Studio. Następnie można [utworzyć aplikację logiki w programie Visual Studio](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md) lub znaleźć i pobrać [istniejącą aplikację logiki z witryny Azure portal do programu Visual Studio.](../logic-apps/manage-logic-apps-with-visual-studio.md)

Pobierając aplikację logiki, można uzyskać szablon, który zawiera definicje aplikacji logiki i innych zasobów, takich jak połączenia. Szablon również *parametryzuje*lub definiuje parametry dla, wartości używane do wdrażania aplikacji logiki i innych zasobów. Wartości tych parametrów można podać w oddzielnym pliku parametrów. W ten sposób można łatwiej zmienić te wartości na podstawie potrzeb wdrożenia. Więcej informacji można znaleźć w następujących tematach:

* [Tworzenie aplikacji logiki za pomocą programu Visual Studio](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md)
* [Zarządzanie aplikacjami logiki w programie Visual Studio](../logic-apps/manage-logic-apps-with-visual-studio.md)

<a name="azure-powershell"></a>

## <a name="create-templates-with-azure-powershell"></a>Tworzenie szablonów za pomocą programu Azure PowerShell

Szablony Usługi Resource Manager można tworzyć przy użyciu programu Azure PowerShell z [modułem LogicAppTemplate](https://github.com/jeffhollan/LogicAppTemplateCreator). Ten moduł open source najpierw ocenia aplikację logiki i wszystkie połączenia, które używa aplikacji logiki. Następnie moduł generuje zasoby szablonu z parametrami niezbędnymi do wdrożenia.

Załóżmy na przykład, że masz aplikację logiki, która odbiera komunikat z kolejki usługi Azure Service Bus i przekazuje dane do bazy danych SQL platformy Azure. Moduł zachowuje wszystkie logiki aranżacji i parametryzuje ciągi połączeń SQL i service bus, dzięki czemu można podać i zmienić te wartości na podstawie potrzeb wdrożenia.

W tych przykładach pokazano, jak tworzyć i wdrażać aplikacje logiki przy użyciu szablonów usługi Azure Resource Manager, potoków platformy Azure w usłudze Azure DevOps i programu Azure PowerShell:

* [Przykład: Łączenie się z kolejkami usługi Azure Service Bus z aplikacji logiki azure](https://docs.microsoft.com/samples/azure-samples/azure-logic-apps-deployment-samples/connect-to-azure-service-bus-queues-from-azure-logic-apps-and-deploy-with-azure-devops-pipelines/)
* [Przykład: Łączenie się z kontami usługi Azure Storage z usługi Azure Logic Apps](https://docs.microsoft.com/samples/azure-samples/azure-logic-apps-deployment-samples/connect-to-azure-storage-accounts-from-azure-logic-apps-and-deploy-with-azure-devops-pipelines/)
* [Przykład: Konfigurowanie akcji aplikacji funkcji dla usługi Azure Logic Apps](https://docs.microsoft.com/samples/azure-samples/azure-logic-apps-deployment-samples/set-up-an-azure-function-app-action-for-azure-logic-apps-and-deploy-with-azure-devops-pipelines/)
* [Przykład: Łączenie się z kontem integracji z usługi Azure Logic Apps](https://docs.microsoft.com/samples/azure-samples/azure-logic-apps-deployment-samples/connect-to-an-integration-account-from-azure-logic-apps-and-deploy-by-using-azure-devops-pipelines/)

### <a name="install-powershell-modules"></a>Instalowanie modułów programu PowerShell

1. Jeśli jeszcze tego nie zrobiłeś, zainstaluj program [Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps).

1. Aby uzyskać najprostszy sposób zainstalowania modułu LogicAppTemplate z [galerii programu PowerShell,](https://www.powershellgallery.com/packages/LogicAppTemplate)uruchom to polecenie:

   ```text
   PS> Install-Module -Name LogicAppTemplate
   ```

   Aby zaktualizować do najnowszej wersji, uruchom to polecenie:

   ```text
   PS> Update-Module -Name LogicAppTemplate
   ```

Lub, aby zainstalować ręcznie, wykonaj kroki opisane w GitHub for [Logic App Template Creator](https://github.com/jeffhollan/LogicAppTemplateCreator).

### <a name="install-azure-resource-manager-client"></a>Instalowanie klienta usługi Azure Resource Manager

Aby moduł LogicAppTemplate działał z dowolnym tokenem dostępu dzierżawy i subskrypcji platformy Azure, zainstaluj [narzędzie klienckie usługi Azure Resource Manager,](https://github.com/projectkudu/ARMClient)które jest prostym narzędziem wiersza polecenia, które wywołuje interfejs API usługi Azure Resource Manager.

Po uruchomieniu `Get-LogicAppTemplate` polecenia za pomocą tego narzędzia polecenie najpierw pobiera token dostępu za pośrednictwem narzędzia ARMClient, przesyła token do skryptu programu PowerShell i tworzy szablon jako plik JSON. Aby uzyskać więcej informacji na temat tego narzędzia, zobacz ten [artykuł o narzędziu klienta usługi Azure Resource Manager](https://blog.davidebbo.com/2015/01/azure-resource-manager-client.html).

### <a name="generate-template-with-powershell"></a>Generowanie szablonu za pomocą programu PowerShell

Aby wygenerować szablon po zainstalowaniu modułu LogicAppTemplate i [interfejsu wiersza polecenia platformy Azure,](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest)uruchom to polecenie programu PowerShell:

```text
PS> Get-LogicAppTemplate -Token (az account get-access-token | ConvertFrom-Json).accessToken -LogicApp <logic-app-name> -ResourceGroup <Azure-resource-group-name> -SubscriptionId $SubscriptionId -Verbose | Out-File C:\template.json
```

Aby postępować zgodnie z zaleceniem dotyczącymi rurociągów w tokenie z [narzędzia klienta usługi Azure Resource Manager,](https://github.com/projectkudu/ARMClient)uruchom to polecenie zamiast tego, gdzie `$SubscriptionId` jest identyfikator subskrypcji platformy Azure:

```text
PS> armclient token $SubscriptionId | Get-LogicAppTemplate -LogicApp <logic-app-name> -ResourceGroup <Azure-resource-group-name> -SubscriptionId $SubscriptionId -Verbose | Out-File C:\template.json
```

Po wyodrębnieniu można następnie utworzyć plik parametrów z szablonu, uruchamiając to polecenie:

```text
PS> Get-ParameterTemplate -TemplateFile $filename | Out-File '<parameters-file-name>.json'
```

W przypadku wyodrębniania za pomocą odwołań do usługi Azure Key Vault (tylko statyczne) uruchom to polecenie:

```text
PS> Get-ParameterTemplate -TemplateFile $filename -KeyVault Static | Out-File $fileNameParameter
```

| Parametry | Wymagany | Opis |
|------------|----------|-------------|
| Plik szablonu | Tak | Ścieżka pliku do pliku szablonu |
| KeyVault | Nie | Wyliczenia, który opisuje sposób obsługi możliwych wartości magazynu kluczy. Wartość domyślna to `None`. |
||||

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Wdrażanie szablonów aplikacji logiki](../logic-apps/logic-apps-deploy-azure-resource-manager-templates.md)
