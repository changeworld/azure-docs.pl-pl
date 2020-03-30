---
title: Tworzenie pliku rozwiązania do zarządzania na platformie Azure | Dokumenty firmy Microsoft
description: Rozwiązania do zarządzania zapewniają scenariusze zarządzania pakietami, które klienci mogą dodać do środowiska platformy Azure.  Ten artykuł zawiera szczegółowe informacje na temat tworzenia rozwiązań do zarządzania, które mają być używane w środowisku lub udostępniane klientom.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 01/09/2018
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 999177f821b98adfa015520252bd3323d0892533
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79275180"
---
# <a name="creating-a-management-solution-file-in-azure-preview"></a>Tworzenie pliku rozwiązania do zarządzania na platformie Azure (wersja zapoznawcza)
> [!NOTE]
> Jest to wstępna dokumentacja do tworzenia rozwiązań do zarządzania na platformie Azure, które są obecnie w wersji zapoznawczej. Każdy schemat opisany poniżej może ulec zmianie.  

Rozwiązania do zarządzania na platformie Azure są implementowane jako [szablony Menedżera zasobów](../../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md).  Głównym zadaniem w nauce tworzenia rozwiązań do zarządzania jest uczenie [się,](../../azure-resource-manager/templates/template-syntax.md)jak tworzyć szablon .  Ten artykuł zawiera unikatowe szczegóły szablonów używanych dla rozwiązań i jak skonfigurować typowe zasoby rozwiązania.


## <a name="tools"></a>Narzędzia

Do pracy z plikami rozwiązania można używać dowolnego edytora tekstu, ale zaleca się korzystanie z funkcji udostępnianych w programie Visual Studio lub Visual Studio Code zgodnie z opisem w poniższych artykułach.

- [Tworzenie i wdrażanie grup zasobów platformy Azure za pomocą programu Visual Studio](../../azure-resource-manager/templates/create-visual-studio-deployment-project.md)
- [Praca z szablonami usługi Azure Resource Manager w programie Visual Studio Code](../../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md)




## <a name="structure"></a>Struktura
Podstawowa struktura pliku rozwiązania do zarządzania jest taka sama jak [szablon Menedżera zasobów](../../azure-resource-manager/templates/template-syntax.md#template-format), który jest następujący.  Każda z poniższych sekcji opisuje elementy najwyższego poziomu i ich zawartość w rozwiązaniu.  

    {
       "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
       "contentVersion": "1.0",
       "parameters": {  },
       "variables": {  },
       "resources": [  ],
       "outputs": {  }
    }

## <a name="parameters"></a>Parametry
[Parametry](../../azure-resource-manager/templates/template-syntax.md#parameters) są wartościami wymaganymi od użytkownika podczas instalowania rozwiązania do zarządzania.  Istnieją standardowe parametry, które będą miały wszystkie rozwiązania i można dodać dodatkowe parametry, zgodnie z wymaganiami dla danego rozwiązania.  Sposób, w jaki użytkownicy będą dostarczać wartości parametrów podczas instalowania rozwiązania, będzie zależeć od określonego parametru i sposobu instalowania rozwiązania.

Gdy użytkownik [instaluje rozwiązanie do zarządzania](solutions.md#install-a-monitoring-solution) za pośrednictwem witryny Azure Marketplace lub szablonów Szybki start platformy Azure, zostanie wyświetlony monit o [wybranie obszaru roboczego usługi Log Analytics i konta automatyzacji.](solutions.md#log-analytics-workspace-and-automation-account)  Są one używane do wypełniania wartości każdego z parametrów standardowych.  Użytkownik nie jest monitowany o bezpośrednie podanie wartości dla standardowych parametrów, ale jest monitowany o podanie wartości dla wszelkich dodatkowych parametrów.


Przykładowy parametr jest pokazany poniżej.  

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
| type |Typ danych dla parametru. Formant wejściowy wyświetlany dla użytkownika zależy od typu danych.<br><br>bool - Pole rozwijane<br>ciąg - pole tekstowe<br>int - Pole tekstowe<br>securestring - Pole hasła<br> |
| category |Opcjonalna kategoria dla parametru.  Parametry w tej samej kategorii są zgrupowane razem. |
|  — formant |Dodatkowe funkcje parametrów ciągu.<br><br>datetime — wyświetlana jest kontrola datetime.<br>guid - Wartość Guid jest generowany automatycznie, a parametr nie jest wyświetlany. |
| description |Opcjonalny opis parametru.  Wyświetlany w dymku informacyjnym obok parametru. |

### <a name="standard-parameters"></a>Parametry standardowe
W poniższej tabeli wymieniono standardowe parametry dla wszystkich rozwiązań do zarządzania.  Te wartości są wypełniane dla użytkownika, a nie monitowanie o nich, gdy rozwiązanie jest zainstalowane z witryny Azure Marketplace lub szablonów Szybki start.  Użytkownik musi podać wartości dla nich, jeśli rozwiązanie jest zainstalowane z inną metodą.

> [!NOTE]
> Interfejs użytkownika w witrynie Azure Marketplace i szablony Szybki start oczekuje nazwy parametrów w tabeli.  Jeśli używasz różnych nazw parametrów, użytkownik zostanie poproszony o ich podanie i nie zostanie on automatycznie wypełniony.
>
>

| Parametr | Typ | Opis |
|:--- |:--- |:--- |
| accountName |ciąg |Nazwa konta usługi Azure Automation. |
| cennikTier |ciąg |Warstwa cenowa zarówno obszaru roboczego usługi Log Analytics, jak i konta usługi Azure Automation. |
| regionId |ciąg |Region konta usługi Azure Automation. |
| nazwa rozwiązania |ciąg |Nazwa rozwiązania.  Jeśli wdrażasz rozwiązanie za pomocą szablonów Szybkiego startu, należy zdefiniować solutionName jako parametr, dzięki czemu można zdefiniować ciąg zamiast wymagać od użytkownika, aby określić jeden. |
| nazwa obszaru roboczego |ciąg |Nazwa obszaru roboczego usługi Log Analytics. |
| obszar roboczyRegionId |ciąg |Region obszaru roboczego usługi Log Analytics. |


Poniżej przedstawiono strukturę standardowych parametrów, które można skopiować i wkleić do pliku rozwiązania.  

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


Wartości parametrów w innych elementach rozwiązania można odwoływać się do parametrów z **parametrami składni('nazwa parametru')**.  Na przykład, aby uzyskać dostęp do nazwy obszaru roboczego, należy użyć **parametrów('workspaceName')**

## <a name="variables"></a>Zmienne
[Zmienne są wartościami,](../../azure-resource-manager/templates/template-syntax.md#variables) które będą używane w pozostałej części rozwiązania do zarządzania.  Te wartości nie są widoczne dla użytkownika instalującego rozwiązanie.  Mają one na celu zapewnienie autorowi jednej lokalizacji, w której mogą zarządzać wartościami, które mogą być używane wiele razy w całym rozwiązaniu. Należy umieścić wszystkie wartości specyficzne dla rozwiązania w zmiennych, w przeciwieństwie do twardego kodowania ich w elemencie **zasobów.**  Dzięki temu kod jest bardziej czytelny i umożliwia łatwą zmianę tych wartości w nowszych wersjach.

Poniżej przedstawiono przykład elementu zmiennych o **typowych** parametrach używanych w rozwiązaniach.

    "variables": {
        "SolutionVersion": "1.1",
        "SolutionPublisher": "Contoso",
        "SolutionName": "My Solution",
        "LogAnalyticsApiVersion": "2015-11-01-preview",
        "AutomationApiVersion": "2015-10-31"
    },

Wartości zmiennych można odwoływać za pośrednictwem rozwiązania ze **zmiennymi składniowymi("nazwa zmiennej")**.  Na przykład, aby uzyskać dostęp do zmiennej SolutionName, należy użyć **zmiennych('Nazwa rozwiązania).)**.

Można również zdefiniować złożone zmienne, które wiele zestawów wartości.  Są one szczególnie przydatne w rozwiązaniach zarządzania, w których definiujesz wiele właściwości dla różnych typów zasobów.  Na przykład można zrestrukturyzować zmienne rozwiązania pokazane powyżej do następujących.

    "variables": {
        "Solution": {
          "Version": "1.1",
          "Publisher": "Contoso",
          "Name": "My Solution"
        },
        "LogAnalyticsApiVersion": "2015-11-01-preview",
        "AutomationApiVersion": "2015-10-31"
    },

W takim przypadku należy odwołać się do wartości zmiennych za pośrednictwem rozwiązania ze **zmiennymi składniowymi('nazwa zmiennej').właściwość**.  Na przykład, aby uzyskać dostęp do zmiennej Nazwa rozwiązania, należy użyć **zmiennych('Rozwiązanie'). Nazwa**.

## <a name="resources"></a>Resources
[Zasoby](../../azure-resource-manager/templates/template-syntax.md#resources) definiują różne zasoby, które rozwiązanie do zarządzania zainstaluje i skonfiguruje.  Będzie to największa i najbardziej złożona część szablonu.  Strukturę i pełny opis elementów zasobów można uzyskać w [szablonach usługi Azure Resource Manager.](../../azure-resource-manager/templates/template-syntax.md#resources)  Różne zasoby, które zazwyczaj można zdefiniować są szczegółowo opisane w innych artykułach w tej dokumentacji. 


### <a name="dependencies"></a>Zależności
Element **dependsOn** określa [zależność od](../../azure-resource-manager/templates/define-resource-dependency.md) innego zasobu.  Po zainstalowaniu rozwiązania zasób nie jest tworzony, dopóki nie utworzono wszystkich jego zależności.  Na przykład rozwiązanie może [uruchomić system runbook](solutions-resources-automation.md#runbooks) po zainstalowaniu przy użyciu [zasobu zadania](solutions-resources-automation.md#automation-jobs).  Zasób zadania będzie zależny od zasobu egodziela, aby upewnić się, że projekt runbook jest tworzony przed utworzeniem zadania.

### <a name="log-analytics-workspace-and-automation-account"></a>Obszar roboczy usługi Log Analytics i konto automatyzacji
Rozwiązania do zarządzania wymagają [obszaru roboczego usługi Log Analytics](../../azure-monitor/platform/manage-access.md) zawierającego widoki i konto [automatyzacji,](../../automation/automation-security-overview.md#automation-account-overview) które zawiera elementy runbook i powiązane zasoby.  Muszą one być dostępne przed utworzeniem zasobów w rozwiązaniu i nie powinny być zdefiniowane w samym rozwiązaniu.  Użytkownik [określi obszar roboczy i konto](solutions.md#log-analytics-workspace-and-automation-account) podczas wdrażania rozwiązania, ale jako autor należy wziąć pod uwagę następujące punkty.


## <a name="solution-resource"></a>Zasób rozwiązania
Każde rozwiązanie wymaga wpisu zasobu w elemencie **zasobów,** który definiuje samo rozwiązanie.  Będzie to mieć typ **Microsoft.OperationsManagement/solutions** i mają następującą strukturę. Obejmuje to [standardowe parametry](#parameters) i [zmienne,](#variables) które są zwykle używane do definiowania właściwości rozwiązania.


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
Zasób rozwiązania musi mieć [zależność od](../../azure-resource-manager/templates/define-resource-dependency.md) każdego innego zasobu w rozwiązaniu, ponieważ muszą one istnieć przed utworzeniem rozwiązania.  Można to zrobić, dodając wpis dla każdego zasobu w **dependsOn** elementu.

### <a name="properties"></a>Właściwości
Zasób rozwiązania ma właściwości w poniższej tabeli.  Obejmuje to zasoby, do których odwołuje się i zawiera rozwiązanie, które definiuje sposób zarządzania zasobem po zainstalowaniu rozwiązania.  Każdy zasób w rozwiązaniu powinny być wymienione w **referencedResources** lub **containedResources** właściwości.

| Właściwość | Opis |
|:--- |:--- |
| obszar roboczyZdanie zasobów |Identyfikator obszaru roboczego usługi Log Analytics w * \<formularzu Identyfikator grupy zasobów>/providers/Microsoft.OperationalInsights/workspaces/\<Nazwa obszaru roboczego\>*. |
| referencedResources |Lista zasobów w rozwiązaniu, które nie powinny być usuwane po usunięciu rozwiązania. |
| zawarte Źródła zasobów |Lista zasobów w rozwiązaniu, które powinny zostać usunięte po usunięciu rozwiązania. |

Powyższy przykład dotyczy rozwiązania z uruchomieniu, harmonogramem i widokiem.  Harmonogram i element runbook są *odwoływane* w element **właściwości,** więc nie są usuwane po usunięciu rozwiązania.  Widok jest *zawarty,* więc jest usuwany po usunięciu roztworu.

### <a name="plan"></a>Planowanie
Jednostka **planu** zasobu rozwiązania ma właściwości w poniższej tabeli.

| Właściwość | Opis |
|:--- |:--- |
| name |Nazwa rozwiązania. |
| version |Wersja rozwiązania określona przez autora. |
| product |Unikatowy ciąg do identyfikowania rozwiązania. |
| wydawca |Wydawca rozwiązania. |



## <a name="next-steps"></a>Następne kroki
* [Dodaj zapisane wyszukiwania i alerty](solutions-resources-searches-alerts.md) do rozwiązania do zarządzania.
* [Dodaj widoki](solutions-resources-views.md) do rozwiązania do zarządzania.
* [Dodaj elementy runbook i inne zasoby automatyzacji](solutions-resources-automation.md) do rozwiązania do zarządzania.
* Poznaj szczegóły tworzenia [szablonów usługi Azure Resource Manager](../../azure-resource-manager/templates/template-syntax.md).
* Przeszukaj [szablony szybki start platformy Azure](https://azure.microsoft.com/documentation/templates) w poszukiwaniu przykładów różnych szablonów Menedżera zasobów.
