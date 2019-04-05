---
title: Jak zarządzać środowiskiem usługi Azure Time Series Insights przy użyciu szablonów usługi Azure Resource Manager | Dokumentacja firmy Microsoft
description: W tym artykule opisano, jak zarządzać środowiskiem usługi Azure Time Series Insights, w sposób programowy za pomocą usługi Azure Resource Manager.
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: anshan
manager: cshankar
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 12/08/2017
ms.custom: seodec18
ms.openlocfilehash: 79751dc0de8817c940355e8b64652014b1c67c35
ms.sourcegitcommit: 8313d5bf28fb32e8531cdd4a3054065fa7315bfd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/05/2019
ms.locfileid: "59045904"
---
# <a name="create-time-series-insights-resources-using-azure-resource-manager-templates"></a>Tworzenie zasobów usługi Time Series Insights przy użyciu szablonów usługi Azure Resource Manager

W tym artykule opisano sposób tworzenia i wdrażania zasobów usługi Time Series Insights przy użyciu szablonów usługi Azure Resource Manager, programu PowerShell i dostawcy zasobów usługi Time Series Insights.

Usługa Time Series Insights obsługuje następujące zasoby:

   | Zasób | Opis |
   | --- | --- |
   | Środowisko | Środowisko usługi Time Series Insights to logiczna grupa zdarzeń, które są odczytu z brokerów zdarzeń, przechowywane i udostępniane dla zapytania. Aby uzyskać więcej informacji, zobacz [Planowanie środowiska usługi Azure Time Series Insights](time-series-insights-environment-planning.md) |
   | Źródło zdarzeń | Źródłem zdarzenia jest połączenie z brokera zdarzeń, z którego odczytuje, a pozyskuje zdarzenia do środowiska usługi Time Series Insights. Źródła zdarzeń obecnie obsługiwane są usługi IoT Hub i Centrum zdarzeń. |
   | Zestaw danych referencyjnych | Zestawy danych referencyjnych Podaj metadane dotyczące zdarzeń w środowisku. Metadane w zestawy danych referencyjnych zostaną dołączone do zdarzeń podczas transferu danych przychodzących. Zestawy danych referencyjnych są definiowane jako zasoby według ich właściwości klucza zdarzeń. Rzeczywiste metadanych, które stanowi odwołanie do zestawu danych zostanie przekazany lub zmodyfikowany za pośrednictwem płaszczyzny danych interfejsów API. |
   | Zasady dostępu | Zasady dostępu Udziel uprawnień do wysyłania zapytań dotyczących danych, manipulowania danymi referencyjnymi w środowisku oraz udostępnianie zapisanych zapytań i perspektyw powiązanych ze środowiskiem. Aby uzyskać więcej informacji, zobacz [zezwolić na dostęp do środowiska usługi Time Series Insights przy użyciu witryny Azure portal](time-series-insights-data-access.md) |

Szablon usługi Resource Manager to plik JSON, który definiuje infrastrukturę i konfigurację zasobów w grupie zasobów. Więcej informacji na ten temat można znaleźć w następujących dokumentach:

- [Omówienie usługi Azure Resource Manager — wdrożenie szablonu](../azure-resource-manager/resource-group-overview.md#template-deployment)
- [Deploy resources with Resource Manager templates and Azure PowerShell (Wdrażanie zasobów za pomocą szablonów usługi Resource Manager i programu Azure PowerShell)](../azure-resource-manager/resource-group-template-deploy.md)
- [Typy zasobów Microsoft.TimeSeriesInsights](/azure/templates/microsoft.timeseriesinsights/allversions)

[201-timeseriesinsights środowiska z eventhub](https://github.com/Azure/azure-quickstart-templates/tree/master/201-timeseriesinsights-environment-with-eventhub) szablon szybkiego startu jest opublikowana w witrynie GitHub. Ten szablon tworzy środowisko usługi Time Series Insights, źródłem zdarzeń podrzędnych pod kątem korzystanie ze zdarzeń z Centrum zdarzeń i dostęp do zasad, które udzielić dostępu do danych środowiska. Jeśli w istniejącym Centrum zdarzeń nie jest określona, zostanie utworzony przy użyciu wdrażania.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="deploy-the-quickstart-template-locally-using-powershell"></a>Wdrażanie szablonu szybkiego startu lokalnie przy użyciu programu PowerShell

Poniższa procedura opisuje sposób używania programu PowerShell do wdrożenia szablonu usługi Azure Resource Manager, który tworzy środowisko usługi Time Series Insights, pod kątem korzystanie ze zdarzeń z Centrum zdarzeń i zasadami określającymi udzielenie dostępu do dostępu do źródła zdarzeń podrzędnych dane środowiska. Jeśli w istniejącym Centrum zdarzeń nie jest określona, zostanie utworzony przy użyciu wdrażania.

Przybliżony przepływ pracy jest w następujący sposób:

1. Instalowanie programu PowerShell.
1. Tworzenie szablonu i pliku parametrów.
1. W programie PowerShell Zaloguj się do konta platformy Azure.
1. Utwórz nową grupę zasobów, jeśli nie istnieje.
1. Testowanie wdrażania.
1. Wdróż szablon.

### <a name="install-powershell"></a>Instalowanie programu PowerShell

Zainstaluj program Azure PowerShell, postępując zgodnie z instrukcjami wyświetlanymi w [wprowadzenie do programu Azure PowerShell](/powershell/azure/get-started-azureps).

### <a name="create-a-template"></a>Tworzenie szablonu

Klonuj lub Kopiuj [201-timeseriesinsights środowiska z eventhub](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-timeseriesinsights-environment-with-eventhub/azuredeploy.json) szablon z serwisu GitHub.

### <a name="create-a-parameters-file"></a>Utwórz plik parametrów

Aby utworzyć plik parametrów, skopiuj [201-timeseriesinsights środowiska z eventhub](https://github.com/Azure/azure-quickstart-templates/blob/master/201-timeseriesinsights-environment-with-eventhub/azuredeploy.parameters.json) pliku.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
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
   | eventHubNamespaceName | Przestrzeń nazw Centrum zdarzeń źródła. |
   | eventHubName | Nazwa Centrum zdarzeń źródła. |
   | consumerGroupName | Nazwa grupy konsumentów, które będzie używane przez usługę Time Series Insights, można odczytać danych z Centrum zdarzeń. **UWAGA:** Aby uniknąć rywalizacji o zasoby, należy do usługi Time Series Insights w wersji dedykowanej i nie są współdzielone z innymi czytelnikami ta grupa odbiorców. |
   | environmentName | Nazwa środowiska. Nazwa nie może zawierać: "<", ">", '%', '&', ': ','\\','?', '/' i znaków kontrolnych. Wszystkie inne znaki są dozwolone.|
   | eventSourceName | Nazwa zasobu podrzędnego źródła zdarzeń. Nazwa nie może zawierać: "<", ">", '%', '&', ': ','\\','?', '/' i znaków kontrolnych. Wszystkie inne znaki są dozwolone. |

#### <a name="optional-parameters"></a>Następujące parametry opcjonalne

   | Parametr | Opis |
   | --- | --- |
   | existingEventHubResourceId | Identyfikator zasobu opcjonalne istniejącym Centrum zdarzeń, które będą podłączone do środowiska usługi Time Series Insights za pośrednictwem źródła zdarzeń. **UWAGA:** Użytkownik wdrożenie szablonu musi mieć uprawnienia do wykonania tej operacji klucze listy, w tym Centrum zdarzeń. Jeśli nie przekazano żadnej wartości, nowym Centrum zdarzeń zostanie utworzona przez szablon. |
   | environmentDisplayName | Opcjonalna nazwa przyjazna do wyświetlenia w interfejsach użytkownika lub narzędzia zamiast nazwy środowiska. |
   | environmentSkuName | Nazwa jednostki SKU. Aby uzyskać więcej informacji, zobacz [stronie cennika usługi Czas serii Insights](https://azure.microsoft.com/pricing/details/time-series-insights/).  |
   | environmentSkuCapacity | Pojemność jednostki Sku. Aby uzyskać więcej informacji, zobacz [stronie cennika usługi Czas serii Insights](https://azure.microsoft.com/pricing/details/time-series-insights/).|
   | environmentDataRetentionTime | Minimalna timespan zdarzenia środowiska będą dostępne dla zapytania. Należy określić wartość w formacie ISO 8601, na przykład "P30D" dla zasad przechowywania równy 30 dni. |
   | eventSourceDisplayName | Opcjonalna nazwa przyjazna do wyświetlenia w interfejsach użytkownika lub narzędzia zamiast nazwy źródła zdarzeń. |
   | eventSourceTimestampPropertyName | Właściwości zdarzenia, która będzie służyć jako źródło zdarzenia sygnatura czasowa. Jeśli wartość nie jest określona dla timestampPropertyName lub wartość null lub pusty ciąg jest określony, będą używane podczas tworzenia zdarzenia. |
   | eventSourceKeyName | Nazwa klucza dostępu współdzielonego, którego będzie używać usługa Time Series Insights, aby nawiązać połączenie z Centrum zdarzeń. |
   | accessPolicyReaderObjectIds | Lista obiektów identyfikatory użytkowników lub aplikacji w usłudze Azure AD, którą powinien posiadać czytnika dostęp do środowiska. Identyfikator obiektu nazwy głównej usługi można uzyskać przez wywołanie metody **Get AzADUser** lub **Get AzADServicePrincipal** polecenia cmdlet. Tworzenie zasad dostępu dla grup usługi Azure AD nie jest jeszcze obsługiwane. |
   | accessPolicyContributorObjectIds | Lista obiektów identyfikatory użytkowników lub aplikacji w usłudze Azure AD, która powinna mieć dostęp współautora do środowiska. Identyfikator obiektu nazwy głównej usługi można uzyskać przez wywołanie metody **Get AzADUser** lub **Get AzADServicePrincipal** polecenia cmdlet. Tworzenie zasad dostępu dla grup usługi Azure AD nie jest jeszcze obsługiwane. |

Na przykład następujący plik parametrów będzie służyć do tworzenia środowiska i źródło zdarzenia, która odczytuje zdarzenia z istniejącym Centrum zdarzeń. Tworzy również dwie zasady dostępu do udzielania dostępu współautora do środowiska.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
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

### <a name="log-in-to-azure-and-set-the-azure-subscription"></a>Logowanie do platformy Azure i ustaw subskrypcję platformy Azure

W wierszu polecenia programu PowerShell uruchom następujące polecenie:

```powershell
Connect-AzAccount
```

Monit logowania do konta platformy Azure. Po zalogowaniu, uruchom następujące polecenie, aby wyświetlić dostępne subskrypcji:

```powershell
Get-AzSubscription
```

To polecenie zwraca listę dostępnych subskrypcji platformy Azure. Wybierz subskrypcję dla bieżącej sesji, uruchamiając następujące polecenie. Zastąp `<YourSubscriptionId>` o identyfikatorze GUID subskrypcji platformy Azure, której chcesz użyć:

```powershell
Set-AzContext -SubscriptionID <YourSubscriptionId>
```

### <a name="set-the-resource-group"></a>Ustaw grupę zasobów

Jeśli nie masz istniejącego zasobu, grupy, Utwórz nową grupę zasobów za pomocą **New AzResourceGroup** polecenia. Podaj nazwę grupy zasobów i lokalizacji, w której chcesz użyć. Na przykład:

```powershell
New-AzResourceGroup -Name MyDemoRG -Location "West US"
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

Sprawdź poprawność wdrożenia, uruchamiając `Test-AzResourceGroupDeployment` polecenia cmdlet. Podczas testowania wdrożenia, dokładnie tak jak podczas wykonywania wdrożenia należy podać parametry.

```powershell
Test-AzResourceGroupDeployment -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json -TemplateParameterFile <path to parameters file>\azuredeploy.parameters.json
```

### <a name="create-the-deployment"></a>Tworzenie wdrożenia

Aby utworzyć nowe wdrożenie, uruchom `New-AzResourceGroupDeployment` polecenia cmdlet i podaj wymagane parametry po wyświetleniu monitu. Parametry zawierają nazwę dla danego wdrożenia, nazwę grupy zasobów, a ścieżka lub adres URL do pliku szablonu. Jeśli **tryb** parametr nie jest określony, wartością domyślną **przyrostowe** jest używany. Aby uzyskać więcej informacji, zobacz [przyrostowe i pełne wdrożeń](../azure-resource-manager/deployment-modes.md).

Następujące polecenie wyświetli monit o podanie pięć wymaganych parametrów w oknie programu PowerShell:

```powershell
New-AzResourceGroupDeployment -Name MyDemoDeployment -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json 
```

Aby określić plik parametrów zamiast tego, użyj następującego polecenia:

```powershell
New-AzResourceGroupDeployment -Name MyDemoDeployment -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json -TemplateParameterFile <path to parameters file>\azuredeploy.parameters.json
```

Umożliwia także wbudowane parametry po uruchomieniu polecenia cmdlet wdrażania usług. Polecenie to w następujący sposób:

```powershell
New-AzResourceGroupDeployment -Name MyDemoDeployment -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json -parameterName "parameterValue"
```

Do uruchomienia [pełną](../azure-resource-manager/deployment-modes.md) wdrożenia, ustawić **tryb** parametr **Complete**:

```powershell
New-AzResourceGroupDeployment -Name MyDemoDeployment -Mode Complete -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json
```

## <a name="verify-the-deployment"></a>Weryfikowanie wdrożenia

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

## <a name="deploy-the-quickstart-template-through-the-azure-portal"></a>Wdrażanie szablonu szybkiego startu w witrynie Azure portal

Strona główna szablon szybkiego startu w usłudze GitHub obejmuje również **Wdróż na platformie Azure** przycisku. Kliknięcie go otwiera stronę wdrożenie niestandardowe w witrynie Azure portal. Na tej stronie możesz wprowadzić lub wybrać wartości dla każdego z parametrów z [wymagane parametry](time-series-insights-manage-resources-using-azure-resource-manager-template.md#required-parameters) lub [następujące parametry opcjonalne](time-series-insights-manage-resources-using-azure-resource-manager-template.md#optional-parameters) tabel. Po wypełnieniu pól ustawienia, klikając polecenie **zakupu** przycisk zainicjuje wdrożenia szablonu.

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-timeseriesinsights-environment-with-eventhub%2Fazuredeploy.json" target="_blank">
    <img src="https://azuredeploy.net/deploybutton.png"/>
</a>

## <a name="next-steps"></a>Kolejne kroki

- Aby uzyskać informacji na temat programowego zarządzania zasobami usługi Time Series Insights przy użyciu interfejsów API REST, zobacz [Management Insights serii czasu](https://docs.microsoft.com/rest/api/time-series-insights-management/).
