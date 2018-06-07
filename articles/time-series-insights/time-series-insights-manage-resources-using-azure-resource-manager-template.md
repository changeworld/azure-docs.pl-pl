---
title: Jak zarządzać środowiska Azure czas serii Insights przy użyciu szablonów usługi Azure Resource Manager | Dokumentacja firmy Microsoft
description: W tym artykule opisano sposób zarządzania środowiska Azure czas serii Insights programowo przy użyciu usługi Azure Resource Manager.
ms.service: time-series-insights
services: time-series-insights
author: sandshadow
ms.author: edett
manager: jhubbard
ms.reviewer: anshan
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 12/08/2017
ms.openlocfilehash: 99aabc01132da60a1b09fcf65f439b8e084bbffa
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34651968"
---
# <a name="create-time-series-insights-resources-using-azure-resource-manager-templates"></a>Utwórz zasoby Insights serii czasu za pomocą szablonów usługi Azure Resource Manager

W tym artykule opisano sposób tworzenia i wdrażania zasobów czasu serii Insights przy użyciu szablonów usługi Azure Resource Manager, programu PowerShell i czas serii Insights dostawcy zasobów.

Czas serii Insights obsługuje następujące zasoby:
   | Zasób | Opis |
   | --- | --- |
   | Środowisko | Środowisko czasu serii Insights to logiczne grupowanie zdarzeń, które są odczytywane brokerzy zdarzeń, przechowywane i dostępne dla zapytania. Aby uzyskać więcej informacji, zobacz [Planowanie środowiska Azure czas serii Insights](time-series-insights-environment-planning.md) |
   | Źródło zdarzenia | Źródłem zdarzenia jest połączenie brokera zdarzeń, z którego Insights serii czasu odczytuje i wysyła strumień zdarzeń do środowiska. Źródła zdarzeń obecnie obsługiwane są Centrum IoT i Centrum zdarzeń. |
   | Odwołanie do zestawu danych | Zestawy danych odwołanie do udostępnienia metadanych o zdarzeniach w środowisku. Dołączy metadanych w zestawów danych referencyjnych ze zdarzeniami podczas transferu. Zestawów danych referencyjnych są definiowane jako zasoby według ich właściwości klucza zdarzeń. Rzeczywiste metadanych, które tworzą odwołanie do zestawu danych jest przekazany lub zmodyfikowane za pośrednictwem płaszczyzna danych interfejsów API. |
   | Zasady dostępu | Zasady dostępu Udziel uprawnień do wysyłania zapytań danych, manipulować danymi odwołania w środowisku i udostępniania zapisane kwerendy i perspektywy skojarzony ze środowiskiem. Aby uzyskać więcej informacji, zobacz [zezwolić na dostęp do środowiska Insights serii czasu za pomocą portalu Azure](time-series-insights-data-access.md) |

Szablonu usługi Resource Manager to plik JSON, który definiuje infrastrukturę i konfigurację zasobów w grupie zasobów. Aby uzyskać więcej informacji można znaleźć w następujących dokumentach:

- [Omówienie usługi Azure Resource Manager - wdrażanie szablonu](../azure-resource-manager/resource-group-overview.md#template-deployment)
- [Deploy resources with Resource Manager templates and Azure PowerShell (Wdrażanie zasobów za pomocą szablonów usługi Resource Manager i programu Azure PowerShell)](../azure-resource-manager/resource-group-template-deploy.md)

[201-timeseriesinsights środowiska z — eventhub](https://github.com/Azure/azure-quickstart-templates/tree/master/201-timeseriesinsights-environment-with-eventhub) szablon szybkiego startu jest opublikowana w witrynie GitHub. Ten szablon tworzy środowisku czasu serii Insights źródło zdarzeń podrzędnych, skonfigurowany pod kątem korzystanie ze zdarzeń z Centrum zdarzeń i zasady określającymi udzielenie dostępu do danych w środowisku dostępu. Jeśli istniejące Centrum zdarzeń nie zostanie określona, zostanie utworzony przy użyciu wdrażania.

## <a name="deploy-the-quickstart-template-locally-using-powershell"></a>Wdróż szablon szybkiego startu lokalnie przy użyciu programu PowerShell

W poniższej procedurze opisano sposób wdrażania szablonu usługi Azure Resource Manager, który tworzy środowisku czasu serii Insights podrzędne źródło zdarzeń, skonfigurowany pod kątem korzystanie ze zdarzeń z Centrum zdarzeń i zasady określającymi udzielenie dostępu do dostępu przy użyciu programu PowerShell dane w środowisku. Jeśli istniejące Centrum zdarzeń nie zostanie określona, zostanie utworzony przy użyciu wdrażania.

Przybliżony przepływu pracy jest następujący:

1. Instalowanie programu PowerShell.
1. Tworzenie szablonu i pliku parametrów.
1. W programie PowerShell należy zalogować się do konta platformy Azure.
1. Utwórz nową grupę zasobów, jeśli jeszcze nie istnieje.
1. Testowanie wdrożenia.
1. Wdrażanie szablonu.

### <a name="install-powershell"></a>Instalowanie programu PowerShell

Instalowanie programu Azure PowerShell zgodnie z instrukcjami w [wprowadzenie do programu Azure PowerShell](/powershell/azure/get-started-azureps).

### <a name="create-a-template"></a>Tworzenie szablonu

Klonuj lub kopiowanie [201-timeseriesinsights środowiska z — eventhub](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-timeseriesinsights-environment-with-eventhub/azuredeploy.json) szablonu z serwisu GitHub.

### <a name="create-a-parameters-file"></a>Utwórz plik parametrów

Aby utworzyć plik parametrów, skopiuj [201-timeseriesinsights środowiska z — eventhub](https://github.com/Azure/azure-quickstart-templates/blob/master/201-timeseriesinsights-environment-with-eventhub/azuredeploy.parameters.json) pliku.

```json
{
  "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
      "eventHubNamespaceName": {
          "value": "GEN-UNIQUE"
      },
      "eventHubName": {
          "value": "GEN-UNIQUE"
      },
      "consumerGroupName": {
          "value": "GEN-UNIQUE"
      },
      "environmentName": {
        "value": "GEN-UNIQUE"
      },
      "eventSourceName": {
        "value": "GEN-UNIQUE"
      }
  }
}
```

#### <a name="required-parameters"></a>Wymagane parametry

   | Parametr | Opis |
   | --- | --- |
   | eventHubNamespaceName | Przestrzeń nazw źródła Centrum zdarzeń. |
   | eventHubName | Nazwa Centrum zdarzeń źródła. |
   | consumerGroupName | Nazwa grupy konsumentów, której usługa Insights serii czas będzie można odczytać danych z Centrum zdarzeń. **Uwaga:** Aby uniknąć rywalizacji, tej grupy konsumentów musi być dedykowane do usługi Insights serie czasu i nie są udostępniane innym czytników. |
   | EnvironmentName | Nazwa środowiska. Nazwa nie może zawierać: "<", ">', '%', '&', ': ','\\','?', '/' i wszelkie znaki kontrolne. Wszystkie inne znaki są dozwolone.|
   | eventSourceName | Nazwa zasobu podrzędnego źródła zdarzenia. Nazwa nie może zawierać: "<", ">', '%', '&', ': ','\\','?', '/' i wszelkie znaki kontrolne. Wszystkie inne znaki są dozwolone. |

#### <a name="optional-parameters"></a>Parametry opcjonalne

   | Parametr | Opis |
   | --- | --- |
   | existingEventHubResourceId | Identyfikator zasobu opcjonalne Centrum zdarzeń, które będą podłączone do środowiska czasu serii Insights przez źródło zdarzenia. **Uwaga:** wdrażanie szablonu użytkownika musi mieć uprawnienia do wykonania tej operacji listkeys w Centrum zdarzeń. Jeśli nie przekazano żadnej wartości, z nowym Centrum zdarzeń zostanie utworzona przez szablon. |
   | environmentDisplayName | Opcjonalne przyjazną nazwę do wyświetlenia na interfejsy użytkownika lub narzędzi zamiast nazwy środowiska. |
   | environmentSkuName | Nazwa jednostki sku. Aby uzyskać więcej informacji, zobacz [stronie cennika usługi Czas serii Insights](https://azure.microsoft.com/pricing/details/time-series-insights/).  |
   | environmentSkuCapacity | Pojemność jednostki Sku. Aby uzyskać więcej informacji, zobacz [stronie cennika usługi Czas serii Insights](https://azure.microsoft.com/pricing/details/time-series-insights/).|
   | environmentDataRetentionTime | Minimalna wartość timespan zdarzeń w środowisku będą dostępne dla zapytania. Musi być określona wartość w formacie ISO 8601, na przykład "P30D" dla zasad przechowywania równy 30 dni. |
   | eventSourceDisplayName | Opcjonalne przyjazną nazwę do wyświetlenia na interfejsy użytkownika lub narzędzi zamiast nazwy źródła zdarzenia. |
   | eventSourceTimestampPropertyName | Właściwości zdarzenia, która będzie służyć jako źródło zdarzenia sygnatury czasowej. Jeśli wartość nie jest określona dla timestampPropertyName lub jeśli określono wartość null lub pustego ciągu, będą używane podczas tworzenia zdarzenia. |
   | eventSourceKeyName | Nazwa klucza dostępu współdzielonego, który będzie używać usługa programu Insights serii czasu nawiązywania połączenia z Centrum zdarzeń. |
   | accessPolicyReaderObjectIds | Lista identyfikatorów użytkowników lub aplikacji w usłudze Azure AD, które powinny mieć dostępu do czytnika w środowisku obiektów. ObjectId główna usługi można uzyskać przez wywołanie metody **Get-AzureRMADUser** lub **Get-AzureRMADServicePrincipal** polecenia cmdlet. Tworzenie zasad dostępu dla grup usługi Azure AD nie jest jeszcze obsługiwane. |
   | accessPolicyContributorObjectIds | Lista identyfikatorów użytkowników lub aplikacji w usłudze Azure AD, które powinny mieć współautora dostęp do środowiska obiektów. ObjectId główna usługi można uzyskać przez wywołanie metody **Get-AzureRMADUser** lub **Get-AzureRMADServicePrincipal** polecenia cmdlet. Tworzenie zasad dostępu dla grup usługi Azure AD nie jest jeszcze obsługiwane. |

Na przykład następujący plik parametrów może służyć do tworzenia środowiska i źródło zdarzenia, które odczytuje zdarzenia z istniejących Centrum zdarzeń. Tworzy dwie zasady dostępu, określającymi udzielenie dostępu współautora do środowiska.

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "eventHubNamespaceName": {
            "value": "tsiTemplateTestNamespace"
        },
        "eventHubName": {
            "value": "tsiTemplateTestEventHub"
        },
        "consumerGroupName": {
            "value": "tsiTemplateTestConsumerGroup"
        },
        "environmentName": {
          "value": "tsiTemplateTestEnvironment"
        },
        "eventSourceName": {
          "value": "tsiTemplateTestEventSource"
        },
        "existingEventHubResourceId": {
          "value": "/subscriptions/{yourSubscription}/resourceGroups/MyDemoRG/providers/Microsoft.EventHub/namespaces/tsiTemplateTestNamespace/eventhubs/tsiTemplateTestEventHub"
        },
        "accessPolicyContributorObjectIds": {
            "value": [
                "AGUID001-0000-0000-0000-000000000000",
                "AGUID002-0000-0000-0000-000000000000"
            ]
        }
    }
  }
```

Aby uzyskać więcej informacji, zobacz [parametry](../azure-resource-manager/resource-group-template-deploy.md#parameter-files) artykułu.

### <a name="log-in-to-azure-and-set-the-azure-subscription"></a>Logowanie do platformy Azure i ustaw subskrypcji platformy Azure

Z wiersza polecenia programu PowerShell, uruchom następujące polecenie:

```powershell
Connect-AzureRmAccount
```

Zostanie wyświetlony monit logowania do konta platformy Azure. Po zalogowaniu, uruchom następujące polecenie, aby wyświetlić dostępne subskrypcji:

```powershell
Get-AzureRMSubscription
```

To polecenie zwraca listę dostępnych subskrypcji platformy Azure. Wybierz subskrypcję dla bieżącej sesji, uruchamiając następujące polecenie. Zastąp `<YourSubscriptionId>` o identyfikatorze GUID dla subskrypcji platformy Azure, którego chcesz użyć:

```powershell
Set-AzureRmContext -SubscriptionID <YourSubscriptionId>
```

### <a name="set-the-resource-group"></a>Ustaw grupę zasobów

Jeśli nie masz istniejący zasób grupy, Utwórz nową grupę zasobów o **New-AzureRmResourceGroup** polecenia. Podaj nazwę grupy zasobów i lokalizacji, w której chcesz użyć. Na przykład:

```powershell
New-AzureRmResourceGroup -Name MyDemoRG -Location "West US"
```

Jeśli to się powiedzie, zostanie wyświetlone podsumowanie nowej grupy zasobów.

```powershell
ResourceGroupName : MyDemoRG
Location          : westus
ProvisioningState : Succeeded
Tags              :
ResourceId        : /subscriptions/<GUID>/resourceGroups/MyDemoRG
```

### <a name="test-the-deployment"></a>Testowanie wdrożenia

Sprawdzanie poprawności wdrożenia, uruchamiając `Test-AzureRmResourceGroupDeployment` polecenia cmdlet. Podczas testowania wdrożenia, należy podać parametry, dokładnie tak jak w przypadku wykonywania wdrożenia.

```powershell
Test-AzureRmResourceGroupDeployment -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json -TemplateParameterFile <path to parameters file>\azuredeploy.parameters.json
```

### <a name="create-the-deployment"></a>Tworzenie wdrożenia

Aby utworzyć nowe wdrożenie, uruchom `New-AzureRmResourceGroupDeployment` polecenia cmdlet i podaj niezbędne parametry, po wyświetleniu monitu. Parametry zawierają nazwę dla danego wdrożenia, nazwę grupy zasobów, a ścieżka lub adres URL do pliku szablonu. Jeśli **tryb** parametr nie zostanie określony, wartością domyślną **przyrostowe** jest używany. Aby uzyskać więcej informacji, zobacz [przyrostowe i pełne wdrożeń](../azure-resource-manager/resource-group-template-deploy.md#incremental-and-complete-deployments).

Polecenie monituje o pięć wymaganych parametrów w oknie programu PowerShell:

```powershell
New-AzureRmResourceGroupDeployment -Name MyDemoDeployment -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json 
```

Aby zamiast tego określ plik parametrów, należy użyć następującego polecenia:

```powershell
New-AzureRmResourceGroupDeployment -Name MyDemoDeployment -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json -TemplateParameterFile <path to parameters file>\azuredeploy.parameters.json
```

Umożliwia także parametry wbudowanego po uruchomieniu polecenia cmdlet wdrażania. Polecenie wygląda następująco:

```powershell
New-AzureRmResourceGroupDeployment -Name MyDemoDeployment -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json -parameterName "parameterValue"
```

Do uruchomienia [pełną](../azure-resource-manager/resource-group-template-deploy.md#incremental-and-complete-deployments) wdrożenia, ustaw **tryb** parametr **Complete**:

```powershell
New-AzureRmResourceGroupDeployment -Name MyDemoDeployment -Mode Complete -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json
```

## <a name="verify-the-deployment"></a>Weryfikacja wdrażania

Jeśli zasoby zostały pomyślnie wdrożone, zostanie wyświetlone podsumowanie wdrożenia w oknie programu PowerShell:

```powershell
DeploymentName          : azuredeploy
ResourceGroupName       : MyDemoRG
ProvisioningState       : Succeeded
Timestamp               : 12/9/2017 01:06:54
Mode                    : Incremental
TemplateLink            :
Parameters              :
                          Name             Type                       Value
                          ===============  =========================  ==========
                          eventHubNamespaceName  String               <eventHubNamespaceName>
                          eventHubName     String                     <eventHubName>
                          consumerGroupName  String                   <consumerGroupName>
                          existingEventHubResourceId  String
                          environmentName  String                     <environmentName>
                          environmentDisplayName  String
                          environmentSkuName  String                  S1
                          environmentSkuCapacity  Int                 1
                          environmentDataRetentionTime  String        P30D
                          eventSourceName  String                     <eventSourceName>
                          eventSourceDisplayName  String
                          eventSourceTimestampPropertyName  String
                          eventSourceKeyName  String                  manage
                          accessPolicyReaderObjectIds  Array          []
                          accessPolicyContributorObjectIds  Array     [
                            "AGUID001-0000-0000-0000-000000000000",
                            "AGUID002-0000-0000-0000-000000000000"
                          ]

Outputs                 :
                          Name             Type                       Value
                          ===============  =========================  ==========
                          dataAccessFQDN   String
                          <guid>.env.timeseries.azure.com
```

## <a name="deploy-the-quickstart-template-through-the-azure-portal"></a>Wdróż szablon szybkiego startu za pośrednictwem portalu Azure

Zawiera również strony głównej szablon szybkiego startu w serwisie GitHub **wdrażanie na platformie Azure** przycisku. Kliknięcie spowoduje otwarcie strony wdrożenie niestandardowe w portalu Azure. Na tej stronie, wprowadź lub wybierz wartości dla każdego z parametrów z [wymagane parametry](time-series-insights-manage-resources-using-azure-resource-manager-template.md#required-parameters) lub [następujące parametry opcjonalne](time-series-insights-manage-resources-using-azure-resource-manager-template.md#optional-parameters) tabel. Po wypełnieniu ustawienia, klikając polecenie **zakupu** przycisk zainicjuje wdrażania szablonu.

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-timeseriesinsights-environment-with-eventhub%2Fazuredeploy.json" target="_blank">
    <img src="http://azuredeploy.net/deploybutton.png"/>
</a>

## <a name="next-steps"></a>Kolejne kroki

- Aby uzyskać informacje na programowego zarządzania zasobami Insights serii czasu przy użyciu interfejsów API REST, zobacz [Zarządzanie Insights serii czasem](https://docs.microsoft.com/rest/api/time-series-insights-management/).
