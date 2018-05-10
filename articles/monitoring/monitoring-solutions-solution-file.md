---
title: Tworzenie pliku rozwiązania zarządzania na platformie Azure | Dokumentacja firmy Microsoft
description: Rozwiązania do zarządzania zapewniają scenariuszy pakietów zarządzania, dodawanych przez klientów do ich środowiska platformy Azure.  Ten artykuł zawiera szczegółowe informacje dotyczące tworzenia rozwiązań do zarządzania do użycia w środowisku lub udostępniane klientom.
services: monitoring
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.assetid: 1915e204-ba7e-431b-9718-9eb6b4213ad8
ms.service: monitoring
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/09/2018
ms.author: bwren
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 46e6ea791752045b0f1afbf1e83e43f498415e54
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/08/2018
---
# <a name="creating-a-management-solution-file-in-azure-preview"></a>Tworzenie pliku rozwiązania zarządzania na platformie Azure (wersja zapoznawcza)
> [!NOTE]
> To jest wstępna dokumentacji do tworzenia rozwiązań do zarządzania na platformie Azure, które są obecnie w wersji zapoznawczej. Żadnego schematu opisanych poniżej może ulec zmianie.  

Rozwiązania do zarządzania na platformie Azure są zaimplementowane jako [szablonów Resource Manager](../azure-resource-manager/resource-manager-template-walkthrough.md).  Learning jest głównym zadaniem się dowiedzieć, jak tworzyć rozwiązania do zarządzania jak [Tworzenie szablonu](../azure-resource-manager/resource-group-authoring-templates.md).  Ten artykuł zawiera unikatowe szczegółowe informacje używane w celu rozwiązania oraz skonfigurować zasoby typowe rozwiązania szablonów.


## <a name="tools"></a>Narzędzia

Można użyć dowolnego edytora tekstu do pracy z plikami rozwiązania, ale zaleca się korzystanie z funkcji dostępnych w programie Visual Studio lub Visual Studio Code zgodnie z opisem w następujących artykułach.

- [Tworzenie i wdrażanie grup zasobów platformy Azure za pomocą programu Visual Studio](../azure-resource-manager/vs-azure-tools-resource-groups-deployment-projects-create-deploy.md)
- [Praca z szablony usługi Azure Resource Manager w kodzie programu Visual Studio](../azure-resource-manager/resource-manager-vs-code.md)




## <a name="structure"></a>Struktura
Podstawowa struktura pliku rozwiązania zarządzania jest taka sama jak [szablonu usługi Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md#template-format), który ma następującą składnię.  Każdy z poniższych rozdziałach opisano elementów najwyższego poziomu i ich zawartość w rozwiązaniu.  

    {
       "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
       "contentVersion": "1.0",
       "parameters": {  },
       "variables": {  },
       "resources": [  ],
       "outputs": {  }
    }

## <a name="parameters"></a>Parametry
[Parametry](../azure-resource-manager/resource-group-authoring-templates.md#parameters) wartości, które wymagają od użytkownika, przy instalacji rozwiązania do zarządzania.  Istnieją standardowe parametry, których wszystkie rozwiązania, a następnie można dodać dodatkowe parametry zgodnie z potrzebami dla określonego rozwiązania.  Jak użytkownicy będą podać wartości parametrów instalacji rozwiązanie będzie zależeć od określonego parametru i sposób instalacji rozwiązania.

Gdy użytkownik [instaluje rozwiązania do zarządzania](monitoring-solutions.md#install-a-management-solution) za pośrednictwem portalu Azure Marketplace lub szybki start Azure szablonów wyświetlony monit o wybranie [obszaru roboczego analizy dzienników i konto automatyzacji](monitoring-solutions.md#log-analytics-workspace-and-automation-account).  Są one używane do wypełnienia wartości parametrów standardowa.  Użytkownik nie jest monitowany o bezpośrednio Podaj wartości parametrów standardowych, ale są monitowani o podanie wartości żadnych parametrów.


Poniżej przedstawiono przykładowe parametru.  

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
| type |Typ danych parametru. Kontrolki wprowadzania wyświetlane dla użytkownika zależy od typu danych.<br><br>bool — menu rozwijane<br>ciąg — pole tekstowe<br>int — pole tekstowe<br>SecureString — pole hasła<br> |
| category |Kategoria opcjonalny parametr.  Parametry w tej samej kategorii są grupowane razem. |
| Formant |Dodatkowe funkcje dotyczące parametrów ciągu.<br><br>DATETIME — formant daty i godziny są wyświetlane.<br>Identyfikator GUID - wartości identyfikatora Guid jest generowany automatycznie, a parametr nie jest wyświetlany. |
| description |Opcjonalny opis dla parametru.  Wyświetlane w dymku informacji obok parametru. |

### <a name="standard-parameters"></a>Standardowe parametry
W poniższej tabeli wymieniono standardowe parametry dla wszystkich rozwiązań zarządzania.  Te wartości zostaną wypełnione dla użytkownika nie jest już dla nich rozwiązania jest zainstalowany za pomocą portalu Azure Marketplace lub szablonów Szybki Start.  Użytkownik musi podać wartości dla nich, jeśli rozwiązanie jest zainstalowana z innej metody.

> [!NOTE]
> Interfejs użytkownika w portalu Azure Marketplace i szablony szybkiego startu oczekuje nazwy parametrów w tabeli.  Jeśli używasz różne nazwy parametrów następnie użytkownik będzie monitowany dla nich, a nie zostaną one automatycznie wypełnione.
>
>

| Parametr | Typ | Opis |
|:--- |:--- |:--- |
| accountName |ciąg |Nazwa konta automatyzacji Azure. |
| pricingTier |ciąg |Warstwa cenowa obszaru roboczego analizy dzienników i konto usługi Automatyzacja Azure. |
| regionId |ciąg |Region konto usługi Automatyzacja Azure. |
| solutionName |ciąg |Nazwa rozwiązania.  Jeśli wdrażasz rozwiązania za pomocą szablonów Szybki Start, następnie należy zdefiniować Nazwa rozwiązania jako parametru, można zdefiniować zamiast tego, gdyż użytkownik musi określić jeden ciąg. |
| workspaceName |ciąg |Nazwa obszaru roboczego analizy dzienników. |
| workspaceRegionId |ciąg |Obszar roboczy analizy dzienników. |


Poniżej znajduje się struktury standardowe parametry, które można skopiować i wkleić do pliku rozwiązania.  

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


Możesz odwoływać się do wartości parametrów w innych elementach rozwiązania przy użyciu składni **(parametr name) parametrów**.  Na przykład, aby uzyskać dostęp do nazwy obszaru roboczego, należy użyć **parameters('workspaceName')**

## <a name="variables"></a>Zmienne
[Zmienne](../azure-resource-manager/resource-group-authoring-templates.md#variables) wartości, które będą używane w pozostałej części rozwiązania do zarządzania.  Wartości te nie są widoczne dla użytkownika instalację.  Są one przeznaczone do zapewnienia autora z jednej lokalizacji, w którym można zarządzać wartości, które mogą być używane wielokrotnie w całym rozwiązaniu. Zachowaj wartości określonych do rozwiązania w zmiennych przeciwieństwie twardych kodowanie ich w **zasobów** elementu.  To sprawia, że kod jest bardziej przejrzysta i pozwala łatwo zmieniać tych wartości w nowszych wersjach.

Poniżej przedstawiono przykład **zmienne** element z typowych parametrów używanych w rozwiązaniach.

    "variables": {
        "SolutionVersion": "1.1",
        "SolutionPublisher": "Contoso",
        "SolutionName": "My Solution",
        "LogAnalyticsApiVersion": "2015-11-01-preview",
        "AutomationApiVersion": "2015-10-31"
    },

Odwołanie do zmiennej wartości za pośrednictwem rozwiązania przy użyciu składni **zmienne ("Nazwa zmiennej")**.  Na przykład, aby uzyskać dostęp do zmiennej Nazwa rozwiązania, możesz użyć **variables('SolutionName')**.

Można również zdefiniować złożone zmienne ustawia wiele wartości.  Są to szczególnie przydatne w rozwiązaniach do zarządzania gdzie definiujesz wiele właściwości dla różnych typów zasobów.  Można na przykład restrukturyzacji pokazanym powyżej do następujących zmiennych rozwiązania.

    "variables": {
        "Solution": {
          "Version": "1.1",
          "Publisher": "Contoso",
          "Name": "My Solution"
        },
        "LogAnalyticsApiVersion": "2015-11-01-preview",
        "AutomationApiVersion": "2015-10-31"
    },

W takim przypadku można odwoływać się do wartości zmiennych za pośrednictwem rozwiązania przy użyciu składni **variables('variable name').property**.  Na przykład, aby uzyskać dostęp do zmiennej nazwy rozwiązania, możesz użyć **variables('Solution'). Nazwa**.

## <a name="resources"></a>Zasoby
[Zasoby](../azure-resource-manager/resource-group-authoring-templates.md#resources) określają różne zasoby zainstaluje i skonfiguruje rozwiązania do zarządzania.  Są to największa i najbardziej złożonych części szablonu.  Można pobrać struktury i pełny opis elementów zasobów w [szablonów Authoring Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md#resources).  Różne zasoby zdefiniowane przez użytkownika będzie zazwyczaj są wyszczególnione w inne artykuły w tej dokumentacji. 


### <a name="dependencies"></a>Zależności
**DependsOn** określa element [zależności](../azure-resource-manager/resource-group-define-dependencies.md) na inny zasób.  Po zainstalowaniu rozwiązania zasobu nie jest tworzony, dopóki wszystkie jego zależności zostały utworzone.  Na przykład może rozwiązania [uruchomienia elementu runbook](monitoring-solutions-resources-automation.md#runbooks) po zainstalowaniu przy użyciu [zadania zasobu](monitoring-solutions-resources-automation.md#automation-jobs).  Zasobów zadania będzie zależał od zasobu elementu runbook, aby upewnić się, że element runbook został utworzony przed utworzeniem zadania.

### <a name="log-analytics-workspace-and-automation-account"></a>Obszar roboczy analizy dzienników i konta automatyzacji
Rozwiązania do zarządzania wymagają [obszaru roboczego analizy dzienników](../log-analytics/log-analytics-manage-access.md) zawiera widoki i [konto automatyzacji](../automation/automation-security-overview.md#automation-account-overview) zawiera elementy runbook i powiązanych zasobów.  Te muszą być dostępne, przed zasobów w rozwiązaniu są tworzone i nie powinna być zdefiniowana w rozwiązaniu do samej siebie.  Użytkownik będzie [Określ obszar roboczy i konta](monitoring-solutions.md#log-analytics-workspace-and-automation-account) podczas ich wdrażania rozwiązania, ale Autor powinien wziąć pod uwagę następujące kwestie.


## <a name="solution-resource"></a>Rozwiązanie zasobów
Każde rozwiązanie wymaga zapisu zasobów w **zasobów** element, który definiuje się rozwiązania.  Będzie to mieć typu **Microsoft.OperationsManagement/solutions** i ma następującą strukturę. Obejmuje to [standardowe parametry](#parameters) i [zmienne](#variables) zwykle używane do definiowania właściwości rozwiązania.


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
Rozwiązanie muszą mieć [zależności](../azure-resource-manager/resource-group-define-dependencies.md) na wszystkich innych zasobów w rozwiązaniu, ponieważ muszą istnieć przed utworzeniem rozwiązania.  Można to zrobić, dodając wpis dla każdego zasobu w **dependsOn** elementu.

### <a name="properties"></a>Właściwości
Zasób rozwiązanie ma właściwości w poniższej tabeli.  Obejmują one zasoby, do których odwołuje się i zawartych w rozwiązaniu, który definiuje sposób zarządzania zasobu po zainstalowaniu rozwiązania.  Każdy zasób w rozwiązaniu powinien być wyświetlany w jednym **referencedResources** lub **containedResources** właściwości.

| Właściwość | Opis |
|:--- |:--- |
| workspaceResourceId |Identyfikator obszaru roboczego analizy dzienników w formie  *<Resource Group ID>/providers/Microsoft.OperationalInsights/workspaces/\<nazwa obszaru roboczego\>*. |
| referencedResources |Lista zasobów w rozwiązaniu, które nie powinny być usuwane, gdy rozwiązanie zostanie usunięta. |
| containedResources |Lista zasobów w rozwiązaniu, która powinna zostać usunięta po usunięciu rozwiązania. |

W powyższym przykładzie jest rozwiązania z elementu runbook, harmonogram i widoku.  Harmonogram i runbook są *przywoływanego* w **właściwości** elementu, więc nie zostaną usunięte po usunięciu rozwiązania.  Widok jest *zawarte* , zostanie ono usunięte po usunięciu rozwiązania.

### <a name="plan"></a>Planowanie
**Planu** jednostki zasobu rozwiązanie ma właściwości w poniższej tabeli.

| Właściwość | Opis |
|:--- |:--- |
| name |Nazwa rozwiązania. |
| wersja |Wersja rozwiązania zgodnie z ustaleniami autora. |
| produkt |Unikatowy ciąg do identyfikowania rozwiązania. |
| Wydawcy |Wydawca rozwiązania. |



## <a name="sample"></a>Sample
Można wyświetlić przykłady pliki rozwiązania z zasobem rozwiązania w następujących lokalizacjach.

- [Zasoby do automatyzacji](monitoring-solutions-resources-automation.md#sample)
- [Zasoby wyszukiwania i alertów](monitoring-solutions-resources-searches-alerts.md#sample)


## <a name="next-steps"></a>Kolejne kroki
* [Dodaj zapisanych wyszukiwań i alerty](monitoring-solutions-resources-searches-alerts.md) do rozwiązania do zarządzania.
* [Dodawanie widoków](monitoring-solutions-resources-views.md) do rozwiązania do zarządzania.
* [Dodawanie elementów runbook i innych zasobów automatyzacji](monitoring-solutions-resources-automation.md) do rozwiązania do zarządzania.
* Poznaj lepiej [szablonów Authoring Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md).
* Wyszukiwanie [szablonów Szybki Start Azure](https://azure.microsoft.com/documentation/templates) przykłady różnych szablonów usługi Resource Manager.
