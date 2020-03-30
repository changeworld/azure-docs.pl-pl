---
title: Rozwiązanie usługi Azure Networking Analytics w usłudze Azure Monitor | Dokumenty firmy Microsoft
description: Za pomocą rozwiązania Usługi Azure Networking Analytics w usłudze Azure Monitor można przejrzeć dzienniki sieciowej grupy zabezpieczeń platformy Azure i dzienniki bramy aplikacji platformy Azure.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 06/21/2018
ms.openlocfilehash: 1045f86db5e1a9ed1979a266937974045e401e27
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79275570"
---
# <a name="azure-networking-monitoring-solutions-in-azure-monitor"></a>Rozwiązania do monitorowania sieci platformy Azure w usłudze Azure Monitor

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Usługa Azure Monitor oferuje następujące rozwiązania do monitorowania sieci:
* Monitor wydajności sieci (NPM) do
    * Monitorowanie kondycji sieci
* Analiza usługi Azure Application Gateway do przeglądu
    * Dzienniki bramy aplikacji platformy Azure
    * Metryki usługi Azure Application Gateway
* Rozwiązania do monitorowania i audytu aktywności sieciowej w sieci w chmurze
    * [Analiza ruchu](https://docs.microsoft.com/azure/networking/network-monitoring-overview#traffic-analytics) 
    * Analiza grupy zabezpieczeń sieci platformy Azure

## <a name="network-performance-monitor-npm"></a>Monitor wydajności sieci (NPM)

Rozwiązanie do zarządzania [Monitorem wydajności sieci](https://docs.microsoft.com/azure/networking/network-monitoring-overview) to rozwiązanie do monitorowania sieci, które monitoruje kondycję, dostępność i osiągalność sieci.  Służy do monitorowania łączności między:

* Chmura publiczna i lokalnie
* Centra danych i lokalizacje użytkowników (oddziały)
* Podsieci obsługujące różne warstwy aplikacji wielowarstwowej.

Aby uzyskać więcej informacji, zobacz [Monitor wydajności sieci](https://docs.microsoft.com/azure/networking/network-monitoring-overview).

## <a name="azure-application-gateway-and-network-security-group-analytics"></a>Analiza bramy aplikacji platformy Azure i sieciowej grupy zabezpieczeń
Aby korzystać z rozwiązań:
1. Dodaj rozwiązanie do zarządzania do usługi Azure Monitor i
2. Włącz diagnostykę, aby skierować diagnostykę do obszaru roboczego usługi Log Analytics w usłudze Azure Monitor. Nie jest konieczne, aby zapisać dzienniki do magazynu obiektów Blob platformy Azure.

Można włączyć diagnostykę i odpowiednie rozwiązanie dla jednej lub obu grup zabezpieczeń bramy aplikacji i sieci.

Jeśli rejestrowanie zasobów diagnostycznych dla określonego typu zasobu nie zostanie włączona, ale zainstalujesz rozwiązanie, bloki pulpitu nawigacyjnego dla tego zasobu będą puste i zostanie wyświetlony komunikat o błędzie.

> [!NOTE]
> W styczniu 2017 r. zmieniono obsługiwany sposób wysyłania dzienników z bram aplikacji i grup zabezpieczeń sieciowych do obszaru roboczego usługi Log Analytics. Jeśli widzisz **rozwiązanie usługi Azure Networking Analytics (przestarzałe),** zapoznaj się [ze starą usługą Networking Analytics,](#migrating-from-the-old-networking-analytics-solution) aby uzyskać kroki, które należy wykonać.
>
>

## <a name="review-azure-networking-data-collection-details"></a>Przeglądanie szczegółów zbierania danych sieciowych platformy Azure
Analizy bramy aplikacji platformy Azure i rozwiązania do zarządzania analizą sieciowej grupy zabezpieczeń zbierają dzienniki diagnostyczne bezpośrednio z bram aplikacji platformy Azure i grup zabezpieczeń sieciowych. Nie jest konieczne, aby zapisać dzienniki do magazynu obiektów Blob platformy Azure i nie agent jest wymagany do zbierania danych.

W poniższej tabeli przedstawiono metody zbierania danych i inne szczegóły dotyczące sposobu zbierania danych dla analizy usługi Azure Application Gateway i analizy sieciowej grupy zabezpieczeń.

| Platforma | Agent bezpośredni | Agent programu Systems Center Operations Manager | Azure | Wymagany jest program Operations Manager? | Dane agenta programu Operations Manager wysyłane za pośrednictwem grupy zarządzania | Częstotliwość zbierania |
| --- | --- | --- | --- | --- | --- | --- |
| Azure |  |  |&#8226; |  |  |po zalogowaniu |


## <a name="azure-application-gateway-analytics-solution-in-azure-monitor"></a>Rozwiązanie do analizy usługi Azure Application Gateway w usłudze Azure Monitor

![Symbol usługi Azure Application Gateway Analytics](media/azure-networking-analytics/azure-analytics-symbol.png)

Następujące dzienniki są obsługiwane dla bram aplikacji:

* AplikacjaGatewayAccessLog
* ApplicationGatewayPerformanceLog
* AplikacjaGatewayFirewallLog

Następujące metryki są obsługiwane dla bram aplikacji:again


* 5-minutowa przepustowość

### <a name="install-and-configure-the-solution"></a>Instalowanie i konfigurowanie rozwiązania
Aby zainstalować i skonfigurować rozwiązanie do analizy usługi Azure Application Gateway, należy użyć następujących instrukcji:

1. Włącz rozwiązanie do analizy bramy aplikacji platformy Azure z [witryny Azure marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.AzureAppGatewayAnalyticsOMS?tab=Overview) lub przy użyciu procesu opisanego w [dodatku rozwiązań usługi Azure Monitor z Galerii rozwiązań.](../../azure-monitor/insights/solutions.md)
2. Włącz rejestrowanie diagnostyki bram [aplikacji,](../../application-gateway/application-gateway-diagnostics.md) które chcesz monitorować.

#### <a name="enable-azure-application-gateway-diagnostics-in-the-portal"></a>Włączanie diagnostyki bramy aplikacji platformy Azure w portalu

1. W witrynie Azure portal przejdź do zasobu bramy aplikacji, aby monitorować.
2. Wybierz *dzienniki diagnostyki,* aby otworzyć następną stronę.

   ![obraz zasobu usługi Azure Application Gateway](media/azure-networking-analytics/log-analytics-appgateway-enable-diagnostics01.png)
3. Kliknij *pozycję Włącz diagnostykę,* aby otworzyć następną stronę.

   ![obraz zasobu usługi Azure Application Gateway](media/azure-networking-analytics/log-analytics-appgateway-enable-diagnostics02.png)
4. Aby włączyć diagnostykę, kliknij pozycję *Włączone* w obszarze *Stan*.
5. Kliknij pole wyboru *Wyślij do usługi Log Analytics*.
6. Wybierz istniejący obszar roboczy usługi Log Analytics lub utwórz obszar roboczy.
7. Kliknij pole wyboru w obszarze **Dziennik** dla każdego z typów dziennika do zbierania.
8. Kliknij *przycisk Zapisz,* aby włączyć rejestrowanie diagnostyki w usłudze Azure Monitor.

#### <a name="enable-azure-network-diagnostics-using-powershell"></a>Włączanie diagnostyki sieci platformy Azure przy użyciu programu PowerShell

Poniższy skrypt programu PowerShell zawiera przykład sposobu włączania rejestrowania zasobów dla bram aplikacji.

```powershell
$workspaceId = "/subscriptions/d2e37fee-1234-40b2-5678-0b2199de3b50/resourcegroups/oi-default-east-us/providers/microsoft.operationalinsights/workspaces/rollingbaskets"

$gateway = Get-AzApplicationGateway -Name 'ContosoGateway'

Set-AzDiagnosticSetting -ResourceId $gateway.ResourceId  -WorkspaceId $workspaceId -Enabled $true
```

### <a name="use-azure-application-gateway-analytics"></a>Korzystanie z analizy usługi Azure Application Gateway
![obraz kafelka analizy usługi Azure Application Gateway](media/azure-networking-analytics/log-analytics-appgateway-tile.png)

Po kliknięciu kafelka **analizy bramy aplikacji platformy Azure** w przeglądzie można wyświetlić podsumowania dzienników, a następnie przejść do szczegółów dla następujących kategorii:

* Dzienniki dostępu do bramy aplikacji
  * Błędy klienta i serwera dla dzienników dostępu bramy aplikacji
  * Żądania na godzinę dla każdej bramy aplikacji
  * Żądania nie powiodły się na godzinę dla każdej bramy aplikacji
  * Błędy przez agenta użytkownika bram aplikacji
* Wydajność bramy aplikacji
  * Kondycja hosta bramy aplikacji
  * Maksymalny i 95 percentyl dla żądań bramy aplikacji nie powiodło się

![obraz pulpitu nawigacyjnego usługi Azure Application Gateway](media/azure-networking-analytics/log-analytics-appgateway01.png)

![obraz pulpitu nawigacyjnego usługi Azure Application Gateway](media/azure-networking-analytics/log-analytics-appgateway02.png)

Na pulpicie nawigacyjnym **analizy bramy aplikacji platformy Azure** przejrzyj informacje podsumowujące w jednym z bloków, a następnie kliknij jeden z nich, aby wyświetlić szczegółowe informacje na stronie wyszukiwania dziennika.

Na dowolnej stronie wyszukiwania dziennika można wyświetlać wyniki według czasu, szczegółowych wyników i historii wyszukiwania w dzienniku. Można również filtrować według aspektów, aby zawęzić wyniki.


## <a name="azure-network-security-group-analytics-solution-in-azure-monitor"></a>Rozwiązanie do analizy grupy zabezpieczeń sieciowej platformy Azure w usłudze Azure Monitor

![Symbol usługi Azure Network Security Group Analytics](media/azure-networking-analytics/azure-analytics-symbol.png)

> [!NOTE]
> Rozwiązanie analityczne Network Security Group przechodzi do obsługi społeczności, ponieważ jego funkcjonalność została zastąpiona przez [Traffic Analytics](../../network-watcher/traffic-analytics.md).
> - Rozwiązanie jest teraz dostępne w [szablonach szybki start platformy Azure](https://azure.microsoft.com/resources/templates/oms-azurensg-solution/) i wkrótce nie będzie już dostępne w portalu Azure Marketplace.
> - Dla istniejących klientów, którzy już dodali rozwiązanie do swojego obszaru roboczego, będzie ono nadal działać bez żadnych zmian.
> - Firma Microsoft będzie nadal obsługiwać wysyłanie dzienników zasobów sieciowych do obszaru roboczego przy użyciu ustawień diagnostyki.

Następujące dzienniki są obsługiwane dla grup zabezpieczeń sieciowych:

* NetworkSecurityGroupEvent (NetworkSecurityGroupEvent)
* NetworkSecurityGroupRuleCounter

### <a name="install-and-configure-the-solution"></a>Instalowanie i konfigurowanie rozwiązania
Aby zainstalować i skonfigurować rozwiązanie usługi Azure Networking Analytics, skorzystaj z następujących instrukcji:

1. Włącz rozwiązanie analityczne azure network security group z [witryny Azure marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.AzureNSGAnalyticsOMS?tab=Overview) lub przy użyciu procesu opisanego w [add Azure Monitor solutions from the Solutions Gallery](../../azure-monitor/insights/solutions.md).
2. Włącz rejestrowanie diagnostyki zasobów [sieciowej grupy zabezpieczeń,](../../virtual-network/virtual-network-nsg-manage-log.md) które chcesz monitorować.

### <a name="enable-azure-network-security-group-diagnostics-in-the-portal"></a>Włączanie diagnostyki sieciowej grupy zabezpieczeń platformy Azure w portalu

1. W witrynie Azure portal przejdź do zasobu sieciowej grupy zabezpieczeń w celu monitorowania
2. Wybierz *dzienniki diagnostyki,* aby otworzyć następującą stronę

   ![obraz zasobu sieciowej grupy zabezpieczeń platformy Azure](media/azure-networking-analytics/log-analytics-nsg-enable-diagnostics01.png)
3. Kliknij *pozycję Włącz diagnostykę,* aby otworzyć następną stronę

   ![obraz zasobu sieciowej grupy zabezpieczeń platformy Azure](media/azure-networking-analytics/log-analytics-nsg-enable-diagnostics02.png)
4. Aby włączyć diagnostykę, kliknij pozycję *Włączone* w obszarze *Stan*
5. Kliknij pole wyboru *Wyślij do usługi Usługa Log Analytics*
6. Wybieranie istniejącego obszaru roboczego usługi Log Analytics lub tworzenie obszaru roboczego
7. Kliknij pole wyboru w obszarze **Dziennik** dla każdego z typów dzienników do zebrania
8. Kliknij *przycisk Zapisz,* aby włączyć rejestrowanie diagnostyki w usłudze Log Analytics

### <a name="enable-azure-network-diagnostics-using-powershell"></a>Włączanie diagnostyki sieci platformy Azure przy użyciu programu PowerShell

Poniższy skrypt programu PowerShell zawiera przykład włączania rejestrowania zasobów dla sieciowych grup zabezpieczeń
```powershell
$workspaceId = "/subscriptions/d2e37fee-1234-40b2-5678-0b2199de3b50/resourcegroups/oi-default-east-us/providers/microsoft.operationalinsights/workspaces/rollingbaskets"

$nsg = Get-AzNetworkSecurityGroup -Name 'ContosoNSG'

Set-AzDiagnosticSetting -ResourceId $nsg.ResourceId  -WorkspaceId $workspaceId -Enabled $true
```

### <a name="use-azure-network-security-group-analytics"></a>Korzystanie z analizy sieciowej grupy zabezpieczeń platformy Azure
Po kliknięciu kafelka **analizy grupy zabezpieczeń sieciowej platformy Azure** w przeglądzie można wyświetlić podsumowania dzienników, a następnie przejść do szczegółów dla następujących kategorii:

* Sieć grupy zabezpieczeń zablokowane przepływy
  * Reguły sieciowej grupy zabezpieczeń z zablokowanymi przepływami
  * Adresy MAC z zablokowanymi przepływami
* Dozwolona grupa zabezpieczeń sieciowych przepływów
  * Reguły sieciowej grupy zabezpieczeń z dozwolonymi przepływami
  * Adresy MAC z dozwolonymi przepływami

![obraz pulpitu nawigacyjnego analizy grupy zabezpieczeń sieciowej platformy Azure](media/azure-networking-analytics/log-analytics-nsg01.png)

![obraz pulpitu nawigacyjnego analizy grupy zabezpieczeń sieciowej platformy Azure](media/azure-networking-analytics/log-analytics-nsg02.png)

Na **pulpicie nawigacyjnym analizy grupy zabezpieczeń sieciowej platformy Azure** przejrzyj informacje podsumowujące w jednym z bloków, a następnie kliknij jeden z nich, aby wyświetlić szczegółowe informacje na stronie wyszukiwania dziennika.

Na dowolnej stronie wyszukiwania dziennika można wyświetlać wyniki według czasu, szczegółowych wyników i historii wyszukiwania w dzienniku. Można również filtrować według aspektów, aby zawęzić wyniki.

## <a name="migrating-from-the-old-networking-analytics-solution"></a>Migracja ze starego rozwiązania Networking Analytics
W styczniu 2017 r. zmieniono obsługiwany sposób wysyłania dzienników z bram aplikacji platformy Azure i grup zabezpieczeń sieci platformy Azure do obszaru roboczego analizy dzienników. Te zmiany zapewniają następujące korzyści:
+ Dzienniki są zapisywane bezpośrednio w usłudze Azure Monitor bez konieczności używania konta magazynu
+ Mniejsze opóźnienia w stosunku do czasu generowania dzienników w usłudze Azure Monitor
+ Mniej kroków konfiguracji
+ Typowy format dla wszystkich typów diagnostyki platformy Azure

Aby korzystać ze zaktualizowanych rozwiązań:

1. [Konfigurowanie diagnostyki do wysyłania bezpośrednio do usługi Azure Monitor z bram aplikacji platformy Azure](#enable-azure-application-gateway-diagnostics-in-the-portal)
2. [Konfigurowanie diagnostyki do wysyłania bezpośrednio do usługi Azure Monitor z grup zabezpieczeń w sieci platformy Azure](#enable-azure-network-security-group-diagnostics-in-the-portal)
2. Włącz *usługę Azure Application Gateway Analytics* i rozwiązanie usługi Azure Network Security Group *Analytics* przy użyciu procesu opisanego w [dodatku rozwiązań usługi Azure Monitor z galerii rozwiązań rozwiązań](solutions.md)
3. Aktualizowanie wszystkich zapisanych zapytań, pulpitów nawigacyjnych lub alertów w celu użycia nowego typu danych
   + Typ jest azurediagnostics. Za pomocą resourcetype do filtrowania do dzienników sieci platformy Azure.

     | Zamiast: | Używać: |
     | --- | --- |
     | NetworkApplicationgates &#124; gdzie OperationName=="ApplicationGatewayAccess" | AzureDiagnostics &#124; gdzie ResourceType=="APPLICATIONGATEWAYS" i OperationName=="ApplicationGatewayAccess" |
     | NetworkApplicationgates &#124; gdzie OperationName=="ApplicationGatewayPerformance" | AzureDiagnostics &#124; gdzie ResourceType=="APPLICATIONGATEWAYS" i OperationName=="ApplicationGatewayPerformance" |
     | Grupy zabezpieczeń sieciowych | AzureDiagnostics &#124; gdzie ResourceType=="NETWORKSECURITYGROUPS" |

   + Dla każdego pola, które ma \_sufiks s, \_d lub \_g w nazwie, zmień pierwszy znak na małe litery
   + W przypadku każdego pola, które \_ma sufiks o nazwie, dane są dzielone na poszczególne pola na podstawie nazw pól zagnieżdżonych.
4. Usuń rozwiązanie *usługi Azure Networking Analytics (przestarzałe).*
   + Jeśli korzystasz z programu PowerShell, użyj`Set-AzureOperationalInsightsIntelligencePack -ResourceGroupName <resource group that the workspace is in> -WorkspaceName <name of the log analytics workspace> -IntelligencePackName "AzureNetwork" -Enabled $false`

Dane zebrane przed zmianą nie są widoczne w nowym rozwiązaniu. Można kontynuować kwerendę dla tych danych przy użyciu starych nazw typów i pól.

## <a name="troubleshooting"></a>Rozwiązywanie problemów
[!INCLUDE [log-analytics-troubleshoot-azure-diagnostics](../../../includes/log-analytics-troubleshoot-azure-diagnostics.md)]

## <a name="next-steps"></a>Następne kroki
* Użyj [zapytań dziennika w usłudze Azure Monitor,](../log-query/log-query-overview.md) aby wyświetlić szczegółowe dane diagnostyczne platformy Azure.
