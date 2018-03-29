---
title: Azure rozwiązania analizy sieci w Log Analytics | Dokumentacja firmy Microsoft
description: Rozwiązania Azure Networking analizy w analizy dzienników umożliwia Przejrzyj dzienniki grupy zabezpieczeń sieci platformy Azure i dzienniki bramy aplikacji Azure.
services: log-analytics
documentationcenter: ''
author: richrundmsft
manager: ewinner
editor: ''
ms.assetid: 66a3b8a1-6c55-4533-9538-cad60c18f28b
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/20/2018
ms.author: richrund
ms.openlocfilehash: 17dadd784d59a2cc0cab6ffbae144010f896b296
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2018
---
# <a name="azure-networking-monitoring-solutions-in-log-analytics"></a>Sieć platformy Azure monitorowanie rozwiązań w analizy dzienników

Analiza dzienników oferuje następujące rozwiązania do monitorowania sieci:
* Monitor wydajności sieci (NPM) do
 * Monitorowanie kondycji sieci
* Azure analytics bramy aplikacji, aby przejrzeć
 * Dzienniki bramy aplikacji Azure
 * Azure metryki bramy aplikacji
* Analiza Azure sieciowej grupy zabezpieczeń do przeglądania
 * Dzienniki grupy zabezpieczeń sieci platformy Azure

## <a name="network-performance-monitor-npm"></a>Monitor wydajności sieci (NPM)

[Monitora wydajności sieci](log-analytics-network-performance-monitor.md) rozwiązanie do zarządzania jest rozwiązania, który monitoruje kondycję, dostępności i uzyskiwanie sieci do monitorowania sieci.  Służy do monitorowania łączność między:

* Chmura publiczna i lokalnych
* centra danych i lokalizacje użytkownika (biurach oddziałów)
* podsieci hosting różnych warstw aplikacji wielowarstwowych.

Aby uzyskać więcej informacji, zobacz [monitora wydajności sieci](log-analytics-network-performance-monitor.md).

## <a name="azure-application-gateway-and-network-security-group-analytics"></a>Azure analytics bramy aplikacji i grupy zabezpieczeń sieci
Aby użyć rozwiązania:
1. Dodaj rozwiązanie do zarządzania stosowane do analizy dzienników i
2. Włącz diagnostykę, aby przekierować diagnostyki do obszaru roboczego analizy dzienników. Nie jest konieczne do zapisywania dzienników do magazynu obiektów Blob Azure.

Można włączyć diagnostyki i odpowiednie rozwiązanie dla jednej lub obu z bramy aplikacji i grup zabezpieczeń sieci.

Jeśli nie włączaj rejestrowania diagnostyki dla określonego typu zasobu, ale zainstalować rozwiązania, bloki pulpitu nawigacyjnego dla tego zasobu jest puste i wyświetlony komunikat o błędzie.

> [!NOTE]
> W stycznia 2017 r zmienić obsługiwanych sposób wysyłania dzienników z bramy aplikacji i grup zabezpieczeń sieci do analizy dzienników. Jeśli widzisz **Analytics sieci Azure (przestarzałe)** rozwiązania, zapoznaj się [migracji ze starego rozwiązania analizy sieci](#migrating-from-the-old-networking-analytics-solution) kroki należy wykonać.
>
>

## <a name="review-azure-networking-data-collection-details"></a>Przejrzyj szczegóły zbierania danych w sieci Azure
Analizy brama aplikacji w usłudze Azure i rozwiązań do zarządzania analytics sieciowej grupy zabezpieczeń zbierania dzienników diagnostycznych bezpośrednio z bramy aplikacji Azure i grup zabezpieczeń sieci. Nie jest konieczne do zapisywania dzienników do magazynu obiektów Blob platformy Azure i agent nie jest wymagany do zbierania danych.

W poniższej tabeli przedstawiono metody zbierania danych i inne szczegółowe informacje o sposobie dane są zbierane dla analityka brama aplikacji w usłudze Azure i analiza sieciowej grupy zabezpieczeń.

| Platforma | Bezpośrednie agenta | Agent menedżera operacji centrum systemów | Azure | Wymagane programu Operations Manager? | Danych agenta programu Operations Manager są wysyłane za pośrednictwem grupy zarządzania | Częstotliwość zbierania |
| --- | --- | --- | --- | --- | --- | --- |
| Azure |  |  |&#8226; |  |  |Podczas rejestrowania |


## <a name="azure-application-gateway-analytics-solution-in-log-analytics"></a>Azure rozwiązania analizy brama aplikacji w analizy dzienników

![Azure symbol analizy bramy aplikacji](./media/log-analytics-azure-networking/azure-analytics-symbol.png)

Dla bramy aplikacji obsługiwane są następujące dzienniki:

* ApplicationGatewayAccessLog
* ApplicationGatewayPerformanceLog
* ApplicationGatewayFirewallLog

Obsługiwane są następujące metryki dla bramy aplikacji:

* Przepływność 5 minut

### <a name="install-and-configure-the-solution"></a>Instalowanie i konfigurowanie rozwiązania
Użyj poniższych instrukcji, aby zainstalować i skonfigurować bramę aplikacji Azure rozwiązania analizy:

1. Włącz rozwiązania analizy bramy aplikacji Azure z [witrynę Azure marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.AzureAppGatewayAnalyticsOMS?tab=Overview) lub przy użyciu procesu opisanego w [rozwiązań dodać analizy dzienników z galerii rozwiązań](log-analytics-add-solutions.md).
2. Włącz rejestrowanie dla diagnostyki [bramy aplikacji](../application-gateway/application-gateway-diagnostics.md) chcesz monitorować.

#### <a name="enable-azure-application-gateway-diagnostics-in-the-portal"></a>Włącz diagnostykę bramy aplikacji Azure w portalu

1. W portalu Azure przejdź do zasobu bramy aplikacji do monitorowania
2. Wybierz *dzienników diagnostycznych* można otworzyć na następującej stronie

   ![obraz zasobu bramy aplikacji Azure](./media/log-analytics-azure-networking/log-analytics-appgateway-enable-diagnostics01.png)
3. Kliknij przycisk *Włącz diagnostykę* można otworzyć na następującej stronie

   ![obraz zasobu bramy aplikacji Azure](./media/log-analytics-azure-networking/log-analytics-appgateway-enable-diagnostics02.png)
4. Aby włączyć diagnostykę, kliknij przycisk *na* w obszarze *stanu*
5. Kliknij pole wyboru *wysyłać do analizy dzienników*
6. Wybierz istniejący obszar roboczy analizy dzienników lub tworzenie obszaru roboczego
7. Kliknij pole wyboru w obszarze **dziennika** dla każdego typu dziennika do zbierania
8. Kliknij przycisk *zapisać* Włączanie rejestrowania diagnostyki do analizy dzienników

#### <a name="enable-azure-network-diagnostics-using-powershell"></a>Włącz diagnostykę sieci platformy Azure przy użyciu programu PowerShell

Poniższy skrypt programu PowerShell przedstawiono przykład sposobu włączania diagnostyczne dla bramy aplikacji.

```powershell
$workspaceId = "/subscriptions/d2e37fee-1234-40b2-5678-0b2199de3b50/resourcegroups/oi-default-east-us/providers/microsoft.operationalinsights/workspaces/rollingbaskets"

$gateway = Get-AzureRmApplicationGateway -Name 'ContosoGateway'

Set-AzureRmDiagnosticSetting -ResourceId $gateway.ResourceId  -WorkspaceId $workspaceId -Enabled $true
```

### <a name="use-azure-application-gateway-analytics"></a>Użyj analizy bramy aplikacji Azure
![Obraz kafelka analytics bramy aplikacji Azure](./media/log-analytics-azure-networking/log-analytics-appgateway-tile.png)

Po kliknięciu **analytics Azure Application Gateway** kafelka w przeglądzie, wyświetlanie podsumowań dzienników i przejść do szczegółów do szczegółów w ramach następujących kategorii:

* Rejestruje dostęp do bramy aplikacji
  * Błędy na kliencie i serwerze dla dzienników dostępu bramy aplikacji
  * Liczba żądań na godzinę dla każdej bramy aplikacji
  * Nieudane żądania na godzinę dla każdej bramy aplikacji
  * Błędy przez agenta użytkownika dla bramy aplikacji
* Wydajność bramy aplikacji
  * Kondycja hosta bramy aplikacji
  * Maksymalne i 95 percentyl żądań bramy aplikacji nie powiodło się

![Obraz pulpitu nawigacyjnego analytics bramy aplikacji Azure](./media/log-analytics-azure-networking/log-analytics-appgateway01.png)

![Obraz pulpitu nawigacyjnego analytics bramy aplikacji Azure](./media/log-analytics-azure-networking/log-analytics-appgateway02.png)

Na **analytics Azure Application Gateway** pulpitu nawigacyjnego, sprawdź informacje w jednym z bloków, a następnie kliknij jedna, aby wyświetlić szczegółowe informacje na stronie dziennik wyszukiwania.

Na wszystkich stronach wyszukiwania dziennika można wyświetlić wyniki według czasu, szczegółowe wyniki i historię dziennik wyszukiwania. Można również filtrować według aspekty, aby zawęzić wyniki.


## <a name="azure-network-security-group-analytics-solution-in-log-analytics"></a>Grupy zabezpieczeń sieci Azure rozwiązania analizy w analizy dzienników

![Symbol Analytics sieciowej grupy zabezpieczeń usługi Azure](./media/log-analytics-azure-networking/azure-analytics-symbol.png)

Dla grup zabezpieczeń sieci obsługiwane są następujące dzienniki:

* NetworkSecurityGroupEvent
* NetworkSecurityGroupRuleCounter

### <a name="install-and-configure-the-solution"></a>Instalowanie i konfigurowanie rozwiązania
Użyj poniższych instrukcji, aby zainstalować i skonfigurować rozwiązania Azure Networking analizy:

1. Włącz rozwiązania analizy Azure sieciową grupę zabezpieczeń z [witrynę Azure marketplace](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/Microsoft.AzureNSGAnalyticsOMS?tab=Overview) lub przy użyciu procesu opisanego w [rozwiązań dodać analizy dzienników z galerii rozwiązań](log-analytics-add-solutions.md).
2. Włącz rejestrowanie dla diagnostyki [sieciowej grupy zabezpieczeń](../virtual-network/virtual-network-nsg-manage-log.md) zasoby, które chcesz monitorować.

### <a name="enable-azure-network-security-group-diagnostics-in-the-portal"></a>Włącz diagnostykę grupy zabezpieczeń sieci platformy Azure w portalu

1. W portalu Azure przejdź do zasobu grupy zabezpieczeń sieci do monitorowania
2. Wybierz *dzienników diagnostycznych* można otworzyć na następującej stronie

   ![Obraz zasobów Azure sieciowej grupy zabezpieczeń](./media/log-analytics-azure-networking/log-analytics-nsg-enable-diagnostics01.png)
3. Kliknij przycisk *Włącz diagnostykę* można otworzyć na następującej stronie

   ![Obraz zasobów Azure sieciowej grupy zabezpieczeń](./media/log-analytics-azure-networking/log-analytics-nsg-enable-diagnostics02.png)
4. Aby włączyć diagnostykę, kliknij przycisk *na* w obszarze *stanu*
5. Kliknij pole wyboru *wysyłać do analizy dzienników*
6. Wybierz istniejący obszar roboczy analizy dzienników lub tworzenie obszaru roboczego
7. Kliknij pole wyboru w obszarze **dziennika** dla każdego typu dziennika do zbierania
8. Kliknij przycisk *zapisać* Włączanie rejestrowania diagnostyki do analizy dzienników

### <a name="enable-azure-network-diagnostics-using-powershell"></a>Włącz diagnostykę sieci platformy Azure przy użyciu programu PowerShell

Poniższy skrypt programu PowerShell zawiera przykład sposobu włączania diagnostyczne dla grup zabezpieczeń sieci
```powershell
$workspaceId = "/subscriptions/d2e37fee-1234-40b2-5678-0b2199de3b50/resourcegroups/oi-default-east-us/providers/microsoft.operationalinsights/workspaces/rollingbaskets"

$nsg = Get-AzureRmNetworkSecurityGroup -Name 'ContosoNSG'

Set-AzureRmDiagnosticSetting -ResourceId $nsg.ResourceId  -WorkspaceId $workspaceId -Enabled $true
```

### <a name="use-azure-network-security-group-analytics"></a>Użyj grupy zabezpieczeń sieci Azure analityka
Po kliknięciu **analytics sieciowej grupy zabezpieczeń usługi Azure** kafelka w przeglądzie, wyświetlanie podsumowań dzienników i przejść do szczegółów do szczegółów w ramach następujących kategorii:

* Grupy zabezpieczeń sieci zablokowane przepływów
  * Reguły grupy zabezpieczeń sieci z przepływami zablokowanych
  * Adresy MAC z przepływami zablokowanych
* Dozwolone przepływów grupy zabezpieczeń sieci
  * Reguły grupy zabezpieczeń sieci z przepływami dozwolone
  * Adresy MAC z przepływami dozwolone

![Obraz pulpitu nawigacyjnego analytics sieciowej grupy zabezpieczeń usługi Azure](./media/log-analytics-azure-networking/log-analytics-nsg01.png)

![Obraz pulpitu nawigacyjnego analytics sieciowej grupy zabezpieczeń usługi Azure](./media/log-analytics-azure-networking/log-analytics-nsg02.png)

Na **analytics sieciowej grupy zabezpieczeń usługi Azure** pulpitu nawigacyjnego, sprawdź informacje w jednym z bloków, a następnie kliknij jedna, aby wyświetlić szczegółowe informacje na stronie dziennik wyszukiwania.

Na wszystkich stronach wyszukiwania dziennika można wyświetlić wyniki według czasu, szczegółowe wyniki i historię dziennik wyszukiwania. Można również filtrować według aspekty, aby zawęzić wyniki.

## <a name="migrating-from-the-old-networking-analytics-solution"></a>Migrowanie z starego rozwiązania analizy sieci
W stycznia 2017 r zmienić obsługiwanych sposób wysyłania dzienników z bramy aplikacji Azure i grup zabezpieczeń sieci Azure do analizy dzienników. Te zmiany zapewniają następujące korzyści:
+ Dzienniki są zapisywane bezpośrednio do analizy dzienników bez konieczności używania konta magazynu
+ Mniejszych opóźnień od czasu wygenerowania dzienników do nich są dostępne w analizy dzienników
+ Mniejszej liczby kroków konfiguracji
+ Format wspólne dla wszystkich typów Diagnostyka Azure

Aby użyć zaktualizowane rozwiązań:

1. [Skonfiguruj diagnostykę, aby być wysyłane bezpośrednio do analizy dzienników z bramy aplikacji Azure](#enable-azure-application-gateway-diagnostics-in-the-portal)
2. [Skonfiguruj diagnostykę, aby wysyłać z grup zabezpieczeń sieci Azure bezpośrednio do analizy dzienników](#enable-azure-network-security-group-diagnostics-in-the-portal)
2. Włącz *analizy bramy aplikacji Azure* i *analiza grupy zabezpieczeń sieci Azure* rozwiązania przy użyciu procesu opisanego w [rozwiązań dodać analizy dzienników Galeria rozwiązań](log-analytics-add-solutions.md)
3. Wszystkie zapisane kwerendy, pulpity nawigacyjne lub alerty, aby użyć nowego typu danych
  + Typ jest AzureDiagnostics. Do filtrowania Azure dzienniki sieci, można użyć typu zasobu.

    | Zamiast: | Za pomocą: |
    | --- | --- |
    | NetworkApplicationgateways &#124; gdzie OperationName == "ApplicationGatewayAccess" | AzureDiagnostics &#124; gdzie ResourceType = "APPLICATIONGATEWAYS" i OperationName == "ApplicationGatewayAccess" |
    | NetworkApplicationgateways &#124; gdzie OperationName == "ApplicationGatewayPerformance" | AzureDiagnostics &#124; gdzie ResourceType == "APPLICATIONGATEWAYS" i OperationName = ApplicationGatewayPerformance |
    | NetworkSecuritygroups | AzureDiagnostics &#124; gdzie ResourceType == "NETWORKSECURITYGROUP" |

   + Dla dowolnego pola, które sufiks \_s, \_d, lub \_g w nazwie, Zmień pierwszy znak na małe litery
   + Dla dowolnego pola, które sufiks \_o w polu Nazwa danych jest podzielony na poszczególnych pól na podstawie nazw pól zagnieżdżonych.
4. Usuń *Azure Networking Analytics (przestarzały)* rozwiązania.
  + Jeśli używasz programu PowerShell, użyj `Set-AzureOperationalInsightsIntelligencePack -ResourceGroupName <resource group that the workspace is in> -WorkspaceName <name of the log analytics workspace> -IntelligencePackName "AzureNetwork" -Enabled $false`

Dane zbierane przed zmiany nie jest widoczny w nowego rozwiązania. Można nadal wysyłać zapytania dotyczące tych danych za pomocą starego typu i nazwy pola.

## <a name="troubleshooting"></a>Rozwiązywanie problemów
[!INCLUDE [log-analytics-troubleshoot-azure-diagnostics](../../includes/log-analytics-troubleshoot-azure-diagnostics.md)]

## <a name="next-steps"></a>Kolejne kroki
* Użyj [Zaloguj wyszukiwania analizy dzienników](log-analytics-log-searches.md) Aby wyświetlić szczegółowe dane diagnostyczne platformy Azure.
