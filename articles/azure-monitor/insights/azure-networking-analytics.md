---
title: Rozwiązanie na platformie Azure Networking Analytics w usłudze Azure Monitor | Dokumentacja firmy Microsoft
description: Rozwiązanie Azure Networking Analytics w usłudze Azure Monitor umożliwia przeglądanie dzienników grupy zabezpieczeń sieci platformy Azure i dzienniki usługi Azure Application Gateway.
services: log-analytics
documentationcenter: ''
author: richrundmsft
manager: ewinner
editor: ''
ms.assetid: 66a3b8a1-6c55-4533-9538-cad60c18f28b
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 06/21/2018
ms.author: richrund
ms.openlocfilehash: 0a5d886558e72ef24b03a49750ed75cf7130bf08
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2019
ms.locfileid: "59006390"
---
# <a name="azure-networking-monitoring-solutions-in-azure-monitor"></a>Monitorowanie rozwiązań w usłudze Azure Monitor sieci platformy Azure

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Usługa Azure Monitor udostępnia następujące rozwiązania do monitorowania sieci:
* Rozwiązania Network Performance Monitor (NPM) do
    * Monitorowanie kondycji sieci
* Analizy usługi Azure Application Gateway, aby zapoznać się z
    * Dzienniki bramy aplikacji platformy Azure
    * Metryki usługi Azure Application Gateway
* Rozwiązania do monitorowania i Przeprowadź inspekcję działań na sieć w chmurze w sieci
    * [Analiza ruchu](https://docs.microsoft.com/azure/networking/network-monitoring-overview#traffic-analytics) 
    * Analiza sieciowej grupy zabezpieczeń platformy Azure

## <a name="network-performance-monitor-npm"></a>Rozwiązanie Network Performance Monitor (NPM)

[Rozwiązania Network Performance Monitor](https://docs.microsoft.com/azure/networking/network-monitoring-overview) rozwiązanie do zarządzania jest rozwiązanie, które monitoruje kondycję, dostępności i osiągalności sieci do monitorowania sieci.  Służy do monitorowania łączności między:

* Chmura publiczna i lokalnych
* Centra danych i lokalizacji użytkownika (biurach oddziałów)
* Podsieci, które hostują różne warstwy aplikacji wielowarstwowych.

Aby uzyskać więcej informacji, zobacz [rozwiązania Network Performance Monitor](https://docs.microsoft.com/azure/networking/network-monitoring-overview).

## <a name="azure-application-gateway-and-network-security-group-analytics"></a>Analizy usługi Azure Application Gateway i sieciowa grupa zabezpieczeń
Aby użyć rozwiązania:
1. Dodaj rozwiązanie do zarządzania do usługi Azure Monitor i
2. Włącz diagnostykę nakazać diagnostyki do obszaru roboczego usługi Log Analytics w usłudze Azure Monitor. Nie jest konieczne do zapisu w dziennikach do usługi Azure Blob storage.

Jedno lub obydwa Application Gateway i sieciowych grup zabezpieczeń, można włączyć diagnostyki i odpowiednie rozwiązania.

Jeśli nie włączaj rejestrowania diagnostycznego dla określonego typu zasobów, ale zainstalować rozwiązania, bloki pulpit nawigacyjny dla tego zasobu są puste i wyświetlić komunikat o błędzie.

> [!NOTE]
> W styczniu 2017 r. obsługiwanych sposób wysyłania dzienników z bram Application Gateway i sieciowych grup zabezpieczeń z obszarem roboczym usługi Log Analytics, zmienić. Jeśli widzisz **Azure Networking Analytics (przestarzałe)** rozwiązanie, zapoznaj się [migracji ze starego rozwiązania analizy sieci](#migrating-from-the-old-networking-analytics-solution) kroki należy wykonać.
>
>

## <a name="review-azure-networking-data-collection-details"></a>Przejrzyj szczegóły dotyczące zbierania danych sieci platformy Azure
Analiza usługi Azure Application Gateway i rozwiązań do zarządzania analiza sieciowej grupy zabezpieczeń należy zebrać dzienniki diagnostyczne bezpośrednio z bramy aplikacji platformy Azure i sieciowych grup zabezpieczeń. Nie jest konieczne do zapisu w dziennikach do usługi Azure Blob storage i agent nie jest wymagany do zbierania danych.

W poniższej tabeli przedstawiono metody zbierania danych i inne szczegóły dotyczące sposobu dane są zbierane dla usługi Azure Application Gateway analytics i analiza sieciowej grupy zabezpieczeń.

| Platforma | Agent bezpośredni | Systems Center Operations Manager agent | Azure | Wymagane programu Operations Manager? | Danych agenta programu Operations Manager wysyłane za pośrednictwem grupy zarządzania | Częstotliwość zbierania |
| --- | --- | --- | --- | --- | --- | --- |
| Azure |  |  |&#8226; |  |  |Po zalogowaniu |


## <a name="azure-application-gateway-analytics-solution-in-azure-monitor"></a>Rozwiązanie na platformie Azure Application Gateway analytics w usłudze Azure Monitor

![Usługa Azure Application Gateway Analytics symboli](media/azure-networking-analytics/azure-analytics-symbol.png)

Bramy Application Gateway obsługuje następujące dzienniki:

* ApplicationGatewayAccessLog
* ApplicationGatewayPerformanceLog
* ApplicationGatewayFirewallLog

Następujące metryki są obsługiwane w przypadku bram Application Gateway: ponownie


* Przepływność 5 minut

### <a name="install-and-configure-the-solution"></a>Instalowanie i konfigurowanie rozwiązania
Użyj poniższych instrukcji, aby zainstalować i skonfigurować rozwiązanie do analizy usługi Azure Application Gateway:

1. Włączanie rozwiązania analiza usługi Azure Application Gateway z [portalu Azure marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.AzureAppGatewayAnalyticsOMS?tab=Overview) lub przy użyciu procesu opisanego w [rozwiązań Dodaj usługi Azure Monitor z galerii rozwiązań](../../azure-monitor/insights/solutions.md).
2. Włączanie rejestrowania diagnostyki [bramy Application Gateway](../../application-gateway/application-gateway-diagnostics.md) chcesz monitorować.

#### <a name="enable-azure-application-gateway-diagnostics-in-the-portal"></a>Włączanie diagnostyki usługi Azure Application Gateway w portalu

1. W witrynie Azure portal przejdź do zasobu usługi Application Gateway do monitorowania.
2. Wybierz *dzienniki diagnostyczne* można otworzyć na następującej stronie.

   ![Obraz przedstawiający zasobów usługi Azure Application Gateway](media/azure-networking-analytics/log-analytics-appgateway-enable-diagnostics01.png)
3. Kliknij przycisk *Włącz diagnostykę* można otworzyć na następującej stronie.

   ![Obraz przedstawiający zasobów usługi Azure Application Gateway](media/azure-networking-analytics/log-analytics-appgateway-enable-diagnostics02.png)
4. Aby włączyć diagnostykę, kliknij *na* w obszarze *stan*.
5. Kliknij pole wyboru *wysyłanie do usługi Log Analytics*.
6. Wybierz istniejący obszar roboczy usługi Log Analytics lub Utwórz obszar roboczy.
7. Kliknij pole wyboru w obszarze **dziennika** dla każdego typu dziennika do zbierania.
8. Kliknij przycisk *Zapisz* Aby włączyć rejestrowanie diagnostyczne do usługi Azure Monitor.

#### <a name="enable-azure-network-diagnostics-using-powershell"></a>Włączanie diagnostyki sieci platformy Azure przy użyciu programu PowerShell

Poniższy skrypt programu PowerShell zawiera przykładowy sposób włączania diagnostyczne dla bram aplikacji.

```powershell
$workspaceId = "/subscriptions/d2e37fee-1234-40b2-5678-0b2199de3b50/resourcegroups/oi-default-east-us/providers/microsoft.operationalinsights/workspaces/rollingbaskets"

$gateway = Get-AzApplicationGateway -Name 'ContosoGateway'

Set-AzDiagnosticSetting -ResourceId $gateway.ResourceId  -WorkspaceId $workspaceId -Enabled $true
```

### <a name="use-azure-application-gateway-analytics"></a>Korzystanie z usługi Azure Application Gateway analytics
![Obraz przedstawiający usługi Azure Application Gateway analytics kafelka](media/azure-networking-analytics/log-analytics-appgateway-tile.png)

Po kliknięciu **usługi Azure Application Gateway analytics** kafelka w obszarze Przegląd Przejrzyj podsumowania dzienników i następnie przejść do szczegółów w ramach następujących kategorii:

* Dzienniki dostępu do bramy aplikacji
  * Błędy klienta i serwera w usłudze Application Gateway dostęp do dzienników
  * Żądania na godzinę dla każdej bramy aplikacji
  * Nieudane żądania na godzinę dla każdej bramy aplikacji
  * Błędy według agenta użytkownika dla bramy Application Gateway
* Wydajność usługi Application Gateway
  * Kondycja hosta dla usługi Application Gateway
  * Żądania zakończone niepowodzeniem maksymalne i 95. percentyl dla usługi Application Gateway

![Obraz przedstawiający pulpit nawigacyjny analizy usługi Azure Application Gateway](media/azure-networking-analytics/log-analytics-appgateway01.png)

![Obraz przedstawiający pulpit nawigacyjny analizy usługi Azure Application Gateway](media/azure-networking-analytics/log-analytics-appgateway02.png)

Na **usługi Azure Application Gateway analytics** pulpitu nawigacyjnego, przejrzyj dane podsumowania w jednym z bloków, a następnie kliknij jedną, aby wyświetlić szczegółowe informacje na stronie wyszukiwania dziennika.

Na wszystkich stronach wyszukiwania dziennika możesz wyświetlić wyników według czasu, szczegółowe wyniki i historię wyszukiwania dziennika. Można również filtrować według zestawu reguł, aby zawęzić wyniki.


## <a name="azure-network-security-group-analytics-solution-in-azure-monitor"></a>Rozwiązanie do analizy sieciowej grupy zabezpieczeń platformy Azure w usłudze Azure Monitor

![Symbol analiza sieciowej grupy zabezpieczeń platformy Azure](media/azure-networking-analytics/azure-analytics-symbol.png)

> [!NOTE]
> Rozwiązanie do analizy sieciowej grupy zabezpieczeń jest przenoszona do pomoc techniczna w społeczności, ponieważ jego funkcje zostały zastąpione [analizy ruchu](../../network-watcher/traffic-analytics.md).
> - To rozwiązanie jest teraz dostępna w [szablony szybkiego startu platformy Azure](https://azure.microsoft.com/resources/templates/oms-azurensg-solution/) i będzie dostępna już wkrótce w witrynie Azure Marketplace.
> - Dla istniejących klientów, którzy już dodane rozwiązanie do swojego obszaru roboczego będą nadal działać bez konieczności wprowadzania zmian.
> - Firmy Microsoft będą w dalszym ciągu obsługuje wysyłanie dzienników diagnostycznych sieciowej grupy zabezpieczeń do obszaru roboczego za pomocą ustawień diagnostycznych.

Obsługiwane są następujące dzienniki sieciowych grup zabezpieczeń:

* NetworkSecurityGroupEvent
* NetworkSecurityGroupRuleCounter

### <a name="install-and-configure-the-solution"></a>Instalowanie i konfigurowanie rozwiązania
Użyj poniższych instrukcji, aby zainstalować i skonfigurować rozwiązanie Azure Networking Analytics:

1. Włączanie rozwiązania analiza sieciowej grupy zabezpieczeń platformy Azure z [portalu Azure marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.AzureNSGAnalyticsOMS?tab=Overview) lub przy użyciu procesu opisanego w [rozwiązań Dodaj usługi Azure Monitor z galerii rozwiązań](../../azure-monitor/insights/solutions.md).
2. Włączanie rejestrowania diagnostyki [sieciowej grupy zabezpieczeń](../../virtual-network/virtual-network-nsg-manage-log.md) zasoby, które chcesz monitorować.

### <a name="enable-azure-network-security-group-diagnostics-in-the-portal"></a>Włącz diagnostykę w grupie zabezpieczeń sieci platformy Azure w portalu

1. W witrynie Azure portal przejdź do zasobu sieciowej grupy zabezpieczeń do monitorowania
2. Wybierz *dzienniki diagnostyczne* można otworzyć na następującej stronie

   ![Obraz przedstawiający zasobu sieciowej grupy zabezpieczeń platformy Azure](media/azure-networking-analytics/log-analytics-nsg-enable-diagnostics01.png)
3. Kliknij przycisk *Włącz diagnostykę* można otworzyć na następującej stronie

   ![Obraz przedstawiający zasobu sieciowej grupy zabezpieczeń platformy Azure](media/azure-networking-analytics/log-analytics-nsg-enable-diagnostics02.png)
4. Aby włączyć diagnostykę, kliknij *na* w obszarze *stanu*
5. Kliknij pole wyboru *wysyłanie do usługi Log Analytics*
6. Wybierz istniejący obszar roboczy usługi Log Analytics lub Utwórz obszar roboczy
7. Kliknij pole wyboru w obszarze **dziennika** dla każdego typu dziennika do zbierania
8. Kliknij przycisk *Zapisz* Aby włączyć rejestrowanie diagnostyczne do usługi Log Analytics

### <a name="enable-azure-network-diagnostics-using-powershell"></a>Włączanie diagnostyki sieci platformy Azure przy użyciu programu PowerShell

Poniższy skrypt programu PowerShell zawiera przykładowy sposób włączania diagnostyczne dla sieciowych grup zabezpieczeń
```powershell
$workspaceId = "/subscriptions/d2e37fee-1234-40b2-5678-0b2199de3b50/resourcegroups/oi-default-east-us/providers/microsoft.operationalinsights/workspaces/rollingbaskets"

$nsg = Get-AzNetworkSecurityGroup -Name 'ContosoNSG'

Set-AzDiagnosticSetting -ResourceId $nsg.ResourceId  -WorkspaceId $workspaceId -Enabled $true
```

### <a name="use-azure-network-security-group-analytics"></a>Analiza użycia Azure sieciowej grupy zabezpieczeń
Po kliknięciu **analiza sieciowej grupy zabezpieczeń platformy Azure** kafelka w obszarze Przegląd Przejrzyj podsumowania dzienników i następnie przejść do szczegółów w ramach następujących kategorii:

* Sieciowa grupa zabezpieczeń zablokowane przepływy
  * Reguły sieciowych grup zabezpieczeń z zablokowanymi przepływami
  * Adresy MAC z zablokowanymi przepływami
* Dozwolone przepływy sieciowej grupy zabezpieczeń
  * Reguły sieciowych grup zabezpieczeń z dozwolonymi przepływami
  * Adresy MAC z dozwolonymi przepływami

![Obraz przedstawiający pulpit nawigacyjny analiza sieciowej grupy zabezpieczeń platformy Azure](media/azure-networking-analytics/log-analytics-nsg01.png)

![Obraz przedstawiający pulpit nawigacyjny analiza sieciowej grupy zabezpieczeń platformy Azure](media/azure-networking-analytics/log-analytics-nsg02.png)

Na **analiza sieciowej grupy zabezpieczeń platformy Azure** pulpitu nawigacyjnego, przejrzyj dane podsumowania w jednym z bloków, a następnie kliknij jedną, aby wyświetlić szczegółowe informacje na stronie wyszukiwania dziennika.

Na wszystkich stronach wyszukiwania dziennika możesz wyświetlić wyników według czasu, szczegółowe wyniki i historię wyszukiwania dziennika. Można również filtrować według zestawu reguł, aby zawęzić wyniki.

## <a name="migrating-from-the-old-networking-analytics-solution"></a>Migracja ze starego rozwiązania analizy sieci
W styczniu 2017 r. obsługiwanych sposobem przesyłania dzienników z bramy aplikacji platformy Azure i grup zabezpieczeń sieci platformy Azure z obszarem roboczym usługi Log Analytics, zmienić. Te zmiany zapewniają następujące korzyści:
+ Dzienniki są zapisywane bezpośrednio w usłudze Azure Monitor bez konieczności używania konta magazynu
+ Mniejszych opóźnień, od momentu, gdy są generowane dzienniki do nich są dostępne w usłudze Azure Monitor
+ Mniejszej liczby czynności konfiguracyjnych
+ Wspólny format dla wszystkich typów diagnostyki platformy Azure

Korzystanie z rozwiązań zaktualizowane:

1. [Konfigurowanie diagnostyki być wysyłane bezpośrednio do usługi Azure Monitor z bramy aplikacji platformy Azure](#enable-azure-application-gateway-diagnostics-in-the-portal)
2. [Konfigurowanie diagnostyki być wysyłane bezpośrednio do usługi Azure Monitor z grup zabezpieczeń sieci platformy Azure](#enable-azure-network-security-group-diagnostics-in-the-portal)
2. Włącz *Azure Application Gateway Analytics* i *analizy grupy zabezpieczeń sieci platformy Azure* rozwiązania przy użyciu procesu opisanego w [rozwiązań Dodaj usługi Azure Monitor z Galeria rozwiązań](solutions.md)
3. Zaktualizuj wszelkie zapisane zapytania, pulpity nawigacyjne lub alerty, aby użyć nowego typu danych
   + Typ ma AzureDiagnostics. Aby odfiltrować dzienników sieci platformy Azure, można użyć typu zasobu.

     | Zamiast: | Użycie: |
     | --- | --- |
     | NetworkApplicationgateways &#124; gdzie OperationName == "ApplicationGatewayAccess" | AzureDiagnostics &#124; gdzie ResourceType = "APPLICATIONGATEWAYS" i OperationName == "ApplicationGatewayAccess" |
     | NetworkApplicationgateways &#124; gdzie OperationName == "ApplicationGatewayPerformance" | AzureDiagnostics &#124; gdzie ResourceType == "APPLICATIONGATEWAYS" i OperationName = ApplicationGatewayPerformance |
     | NetworkSecuritygroups | AzureDiagnostics &#124; where ResourceType=="NETWORKSECURITYGROUPS" |

   + Dla dowolnego pola, które ma sufiks \_s, \_d, lub \_g nazwę, zmienić pierwszy znak na małe litery
   + Dla dowolnego pola, które ma sufiks \_o w nazwie, dane zostanie podzielona na poszczególne pola na podstawie nazw pól zagnieżdżonych.
4. Usuń *Azure Networking Analytics (przestarzałe)* rozwiązania.
   + Jeśli używasz programu PowerShell użyć `Set-AzureOperationalInsightsIntelligencePack -ResourceGroupName <resource group that the workspace is in> -WorkspaceName <name of the log analytics workspace> -IntelligencePackName "AzureNetwork" -Enabled $false`

Dane zbierane, zanim zmiany nie jest widoczna w nowym rozwiązaniu. Można utworzyć zapytanie dotyczące tych danych za pomocą starego typu i nazwy pola.

## <a name="troubleshooting"></a>Rozwiązywanie problemów
[!INCLUDE [log-analytics-troubleshoot-azure-diagnostics](../../../includes/log-analytics-troubleshoot-azure-diagnostics.md)]

## <a name="next-steps"></a>Kolejne kroki
* Użyj [rejestrowania zapytań w usłudze Azure Monitor](../log-query/log-query-overview.md) Aby wyświetlić szczegółowe dane diagnostyczne platformy Azure.
