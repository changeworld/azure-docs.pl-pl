---
title: Tworzenie pliku rozwiązania zarządzania na platformie Azure | Dokumentacja firmy Microsoft
description: Rozwiązania do zarządzania zapewniają scenariuszy zarządzania spakowane, dodawanych przez klientów do ich środowiska platformy Azure.  Ten artykuł zawiera szczegółowe informacje dotyczące tworzenia rozwiązania do zarządzania do użycia we własnym środowisku lub udostępniane klientom.
services: monitoring
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.assetid: 1915e204-ba7e-431b-9718-9eb6b4213ad8
ms.service: azure-monitor
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/09/2018
ms.author: bwren
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 4e5c27911fe86a6916235014f8602327df929e20
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60595766"
---
# <a name="creating-a-management-solution-file-in-azure-preview"></a>Tworzenie pliku rozwiązania zarządzania na platformie Azure (wersja zapoznawcza)
> [!NOTE]
> Jest to wersję wstępną dokumentacji dotyczące tworzenia rozwiązania do zarządzania na platformie Azure, które są obecnie w wersji zapoznawczej. Żadnego schematu opisanych poniżej ulec zmianie.  

Rozwiązania do zarządzania na platformie Azure są implementowane jako [szablonów usługi Resource Manager](../../azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal.md).  Głównym zadaniem, jak tworzyć rozwiązania do zarządzania jest uczenie jak [Tworzenie szablonu](../../azure-resource-manager/resource-group-authoring-templates.md).  Ten artykuł zawiera unikatowe szczegółowe informacje o szablonów używanych dla rozwiązania i sposobie konfigurowania typowe rozwiązanie z zasobów.


## <a name="tools"></a>Narzędzia

Można użyć dowolnego edytora tekstów, aby pracować z plikami rozwiązania, ale firma Microsoft zaleca korzystanie z funkcji oferowanych w programie Visual Studio lub Visual Studio Code, zgodnie z opisem w następujących artykułach.

- [Tworzenie i wdrażanie grup zasobów platformy Azure za pomocą programu Visual Studio](../../azure-resource-manager/vs-azure-tools-resource-groups-deployment-projects-create-deploy.md)
- [Praca z szablonów usługi Resource Manager platformy Azure w programie Visual Studio Code](../../azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal.md)




## <a name="structure"></a>Struktura
Podstawowa struktura pliku rozwiązania zarządzania jest taka sama jak [szablonu usługi Resource Manager](../../azure-resource-manager/resource-group-authoring-templates.md#template-format), który jest w następujący sposób.  Każdego z poniższych sekcjach opisano elementy najwyższego poziomu i ich zawartość w rozwiązaniu.  

    {
       "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
       "contentVersion": "1.0",
       "parameters": {  },
       "variables": {  },
       "resources": [  ],
       "outputs": {  }
    }

## <a name="parameters"></a>Parametry
[Parametry](../../azure-resource-manager/resource-group-authoring-templates.md#parameters) są wartościami, które wymagają od użytkownika podczas instalacjo rozwiązania do zarządzania.  Istnieją standardowe parametry, które będą miały wszystkie rozwiązania, a następnie można dodać dodatkowe parametry zgodnie z wymaganiami dla określonego rozwiązania.  Jak użytkownicy będą podać wartości parametrów podczas instalacjo rozwiązania będzie zależeć od określonego parametru i sposób instalacji rozwiązania.

Gdy użytkownik [zainstalowanie rozwiązania do zarządzania](solutions.md#install-a-monitoring-solution) za pośrednictwem witryny Azure Marketplace lub szybkiego startu platformy Azure szablonów wyświetlony monit o wybranie [obszar roboczy usługi Log Analytics i konto usługi Automation](solutions.md#log-analytics-workspace-and-automation-account).  Są one używane do wypełniania wartości każdego standardowe parametry.  Użytkownik nie jest monitowany o bezpośrednio podać wartości parametrów standardowych, ale są monitowani o podanie wartości żadnych dodatkowych parametrów.


Poniżej przedstawiono przykładowy parametr.  

    "startTime": {
        "type": "string",
        "metadata": {
            "description": "Enter time for starting VMs by resource group.",
            "control": "datetime",
            "category": "Schedule"
        }

W poniższej tabeli opisano atrybuty parametru.

| Atrybut | Opis |
|:--- |:--- |
| type |Typ danych parametru. Kontrolka wejściowa wyświetlane użytkownikowi zależy od typu danych.<br><br>bool — menu rozwijane<br>ciąg — pole tekstowe<br>int — pole tekstowe<br>SecureString - pole hasła<br> |
| category |Kategoria opcjonalny parametr.  Parametry w tej samej kategorii są grupowane razem. |
| control |Dodatkowe funkcje dla parametrów.<br><br>DATETIME — formant typu DataGodzina jest wyświetlany.<br>Identyfikator GUID — wartość identyfikatora Guid jest generowany automatycznie, a parametr nie jest wyświetlana. |
| description |Opcjonalny opis dla parametru.  Wyświetlane w dymku informacji obok parametru. |

### <a name="standard-parameters"></a>Standardowe parametry
W poniższej tabeli wymieniono standardowe parametry dla wszystkich rozwiązań zarządzania.  Te wartości zostaną wypełnione dla użytkownika, który nie jest już dla nich, po zainstalowaniu rozwiązania z witryny Azure Marketplace lub szablonów szybkiego startu.  Użytkownik musi podać wartości dla nich, jeśli rozwiązanie jest zainstalowane za pomocą innej metody.

> [!NOTE]
> Interfejs użytkownika w portalu Azure Marketplace i szablony szybkiego startu oczekuje nazwy parametrów w tabeli.  Jeśli używasz nazwy różnych parametrów następnie użytkownik jest monitowany dla nich, a nie zostaną one automatycznie wypełnione.
>
>

| Parametr | Typ | Opis |
|:--- |:--- |:--- |
| accountName |string |Nazwa konta usługi Azure Automation. |
| pricingTier |string |Warstwa cenowa obszaru roboczego usługi Log Analytics i konto usługi Azure Automation. |
| regionId |string |Region konta usługi Azure Automation. |
| solutionName |string |Nazwa rozwiązania.  Jeśli wdrażasz swoje rozwiązanie przy użyciu szablonów szybkiego startu, następnie należy zdefiniować solutionName jako parametr, dzięki czemu można zdefiniować ciąg zamiast konieczności użytkownikowi na określenie jednego. |
| workspaceName |string |Nazwa obszaru roboczego analizy dzienników. |
| workspaceRegionId |string |Region obszaru roboczego usługi Log Analytics. |


Poniżej przedstawiono strukturę standardowe parametry, które można skopiować i wkleić do pliku rozwiązania.  

    "parameters": {
        "workspaceName": {
            "type": "string",
            "metadata": {
                "description": "A valid Log Analytics workspace name"
            }
        },
        "accountName": {
               "type": "string",
               "metadata": {
                   "description": "A valid Azure Automation account name"
               }
        },
        "workspaceRegionId": {
               "type": "string",
               "metadata": {
                   "description": "Region of the Log Analytics workspace"
            }
        },
        "regionId": {
            "type": "string",
            "metadata": {
                "description": "Region of the Azure Automation account"
            }
        },
        "pricingTier": {
            "type": "string",
            "metadata": {
                "description": "Pricing tier of both Log Analytics workspace and Azure Automation account"
            }
        }
    }


Możesz odwołać się do wartości parametrów w innych elementach rozwiązania przy użyciu składni **parametry ("Nazwa parametru")** .  Na przykład, aby uzyskać dostęp do nazwy obszaru roboczego, należy użyć **parameters('workspaceName')**

## <a name="variables"></a>Zmienne
[Zmienne](../../azure-resource-manager/resource-group-authoring-templates.md#variables) są wartościami, które będą używane w pozostałej części rozwiązania do zarządzania.  Te wartości nie są widoczne dla użytkownika, instalowanie rozwiązania.  Są one przeznaczone do zapewnienia Autor z jednej lokalizacji, w którym można zarządzać wartości, które mogą być wielokrotnie używane w całym rozwiązaniu. Należy umieścić wszelkie wartości określonych do rozwiązania w zmiennych twardych kodowania, w przeciwieństwie do **zasobów** elementu.  To czytelność kodu i pozwala łatwo zmieniać te wartości w nowszych wersjach.

Poniżej przedstawiono przykład **zmienne** element z typowych parametrów używane w rozwiązaniach.

    "variables": {
        "SolutionVersion": "1.1",
        "SolutionPublisher": "Contoso",
        "SolutionName": "My Solution",
        "LogAnalyticsApiVersion": "2015-11-01-preview",
        "AutomationApiVersion": "2015-10-31"
    },

Możesz odwołać się do wartości zmiennych za pośrednictwem rozwiązania przy użyciu składni **zmiennych ("Nazwa zmiennej")** .  Na przykład, aby uzyskać dostęp do zmiennej SolutionName, należy użyć **variables('SolutionName')** .

Można również zdefiniować zmienne złożonych zestawów wiele wartości.  Są to szczególnie przydatne w rozwiązaniach do zarządzania której definiujesz wiele właściwości dla różnych typów zasobów.  Można na przykład restrukturyzacji roztwór zmiennych powyżej do następujących.

    "variables": {
        "Solution": {
          "Version": "1.1",
          "Publisher": "Contoso",
          "Name": "My Solution"
        },
        "LogAnalyticsApiVersion": "2015-11-01-preview",
        "AutomationApiVersion": "2015-10-31"
    },

W tym przypadku odwołasz się do wartości zmiennych za pośrednictwem rozwiązania przy użyciu składni **variables('variable name').property**.  Na przykład, aby uzyskać dostęp do zmiennej nazwę rozwiązania, należy użyć **variables('Solution'). Nazwa**.

## <a name="resources"></a>Zasoby
[Zasoby](../../azure-resource-manager/resource-group-authoring-templates.md#resources) określają różne zasoby instalowanych i konfigurowanych rozwiązania do zarządzania.  Są to największych i najbardziej złożonych części szablonu.  Możesz uzyskać struktury i pełny opis zasobu elementów w [tworzenia usługi Azure Resource Manager](../../azure-resource-manager/resource-group-authoring-templates.md#resources).  Różne zasoby, które będą zazwyczaj definiują są szczegółowo opisane w innych artykułach w tej dokumentacji. 


### <a name="dependencies"></a>Zależności
**DependsOn** element Określa [zależności](../../azure-resource-manager/resource-group-define-dependencies.md) na inny zasób.  Po zainstalowaniu rozwiązania zasób nie został utworzony, dopóki wszystkie jego zależności zostały utworzone.  Na przykład może być rozwiązania [uruchamiania elementu runbook](solutions-resources-automation.md#runbooks) po jej zainstalowaniu, za pomocą [zadania zasobów](solutions-resources-automation.md#automation-jobs).  Zasób zadanie będzie zależał od zasobu elementu runbook, aby upewnić się, czy element runbook został utworzony przed utworzeniem zadania.

### <a name="log-analytics-workspace-and-automation-account"></a>Obszar roboczy usługi log Analytics i konto usługi Automation
Rozwiązania do zarządzania wymagają [obszaru roboczego usługi Log Analytics](../../azure-monitor/platform/manage-access.md) zawierają widoki i [konta usługi Automation](../../automation/automation-security-overview.md#automation-account-overview) zawiera elementy runbook i powiązane zasoby.  Te muszą być dostępne, zanim zasobów w rozwiązaniu są tworzone i nie powinna być zdefiniowana w samym rozwiązaniem.  Użytkownik zostanie [Określ obszar roboczy i konto](solutions.md#log-analytics-workspace-and-automation-account) podczas ich wdrażania rozwiązania, ale Autor należy wziąć pod uwagę następujące kwestie.


## <a name="solution-resource"></a>Rozwiązanie zasobów
Każde rozwiązanie wymaga zapisu zasobów w **zasobów** element, który definiuje samego rozwiązania.  Będzie to miało typu **Microsoft.OperationsManagement/solutions** i mają następującą strukturę. Obejmuje to [standardowe parametry](#parameters) i [zmienne](#variables) które zwykle są używane do definiowania właściwości rozwiązania.


    {
      "name": "[concat(variables('Solution').Name, '[' ,parameters('workspaceName'), ']')]",
      "location": "[parameters('workspaceRegionId')]",
      "tags": { },
      "type": "Microsoft.OperationsManagement/solutions",
      "apiVersion": "[variables('LogAnalyticsApiVersion')]",
      "dependsOn": [
        <list-of-resources>
      ],
      "properties": {
        "workspaceResourceId": "[resourceId('Microsoft.OperationalInsights/workspaces', parameters('workspaceName'))]",
        "referencedResources": [
            <list-of-referenced-resources>
        ],
        "containedResources": [
            <list-of-contained-resources>
        ]
      },
      "plan": {
        "name": "[concat(variables('Solution').Name, '[' ,parameters('workspaceName'), ']')]",
        "Version": "[variables('Solution').Version]",
        "product": "[variables('ProductName')]",
        "publisher": "[variables('Solution').Publisher]",
        "promotionCode": ""
      }
    }




### <a name="dependencies"></a>Zależności
Zasób rozwiązanie musi mieć [zależności](../../azure-resource-manager/resource-group-define-dependencies.md) na wszystkich innych zasobów w rozwiązaniu, ponieważ one muszą istnieć przed utworzeniem rozwiązania.  Możesz to zrobić, dodając wpis dla każdego zasobu w **dependsOn** elementu.

### <a name="properties"></a>Właściwości
Zasób rozwiązanie ma właściwości w poniższej tabeli.  Obejmują one zasoby, do których odwołuje się i zawarty w rozwiązaniu, który definiuje, jak zasób jest zarządzany, po zainstalowaniu rozwiązania.  Każdy zasób w rozwiązaniu powinien zostać wyświetlony w jednym **referencedResources** lub **containedResources** właściwości.

| Właściwość | Opis |
|:--- |:--- |
| workspaceResourceId |Identyfikator obszaru roboczego usługi Log Analytics w formie  *\<identyfikator grupy zasobów > /providers/Microsoft.OperationalInsights/workspaces/\<nazwa obszaru roboczego\>* . |
| referencedResources |Lista zasobów w rozwiązaniu, które nie powinny być usuwane, gdy rozwiązanie zostanie usunięty. |
| containedResources |Lista zasobów w rozwiązaniu, które powinny zostać usunięte po usunięciu rozwiązania. |

W powyższym przykładzie jest rozwiązanie z elementu runbook, harmonogram i widoku.  Harmonogram i elementu runbook są *odwołania* w **właściwości** elementu, więc nie są usuwane po usunięciu rozwiązania.  Widok jest *zawarte* , zostanie ono usunięte po usunięciu rozwiązania.

### <a name="plan"></a>Planowanie
**Plan** jednostki zasobów rozwiązanie ma właściwości w poniższej tabeli.

| Właściwość | Opis |
|:--- |:--- |
| name |Nazwa rozwiązania. |
| version |Wersja rozwiązania, zgodnie z ustaleniami autora. |
| product |Unikatowy ciąg do identyfikowania rozwiązania. |
| publisher |Z wydawcą rozwiązania. |



## <a name="next-steps"></a>Kolejne kroki
* [Dodawanie zapisanego wyszukiwania i alerty](solutions-resources-searches-alerts.md) do rozwiązania do zarządzania.
* [Dodawanie widoków](solutions-resources-views.md) do rozwiązania do zarządzania.
* [Dodawanie elementów runbook i innych zasobów usługi Automation](solutions-resources-automation.md) do rozwiązania do zarządzania.
* Poznaj lepiej [tworzenia usługi Azure Resource Manager](../../azure-resource-manager/resource-group-authoring-templates.md).
* Wyszukiwanie [szablony szybkiego startu platformy Azure](https://azure.microsoft.com/documentation/templates) przykłady różnych szablonów usługi Resource Manager.
