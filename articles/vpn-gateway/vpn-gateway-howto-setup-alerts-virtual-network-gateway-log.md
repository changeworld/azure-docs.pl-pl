---
title: 'Brama sieci VPN platformy Azure: konfigurowanie alertów dotyczących zdarzeń dziennika diagnostycznego'
description: Kroki konfigurowania alertów w zdarzeniach dziennika diagnostycznego bramy sieci VPN
services: vpn-gateway
author: anzaman
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 06/12/2019
ms.author: alzam
ms.openlocfilehash: 49510b26e0b2a9c69dd65faf0f343e86d1a068db
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/08/2020
ms.locfileid: "80878905"
---
# <a name="set-up-alerts-on-diagnostic-log-events-from-vpn-gateway"></a>Konfigurowanie alertów dotyczących zdarzeń dziennika diagnostycznego z bramy sieci VPN

Ten artykuł ułatwia konfigurowanie alertów na podstawie zdarzeń dziennika diagnostycznego z usługi Azure VPN Gateway przy użyciu usługi Azure Log Analytics. 

Następujące dzienniki są dostępne na platformie Azure:

|***Nazwa*** | ***Opis*** |
|---        | ---               |
|Dziennik diagnostyki bramy | Zawiera dzienniki diagnostyczne dla zdarzeń konfiguracji bramy, podstawowych zmian i zdarzeń konserwacji |
|TunnelDiagnosticLog | Zawiera zdarzenia zmiany stanu tunelu. Zdarzenia połączenia/rozłączania tunelu mają podsumowaną przyczynę zmiany stanu, jeśli ma to zastosowanie |
|Dziennik diagnostyki trasy | Rejestruje zmiany tras statycznych i zdarzeń BGP, które występują w bramie |
|IKEDiagnosticLog | Rejestruje komunikaty sterujące IKE i zdarzenia na bramie |
|P2SDiagnosticLog | Rejestruje komunikaty kontrolne typu "punkt-lokacja" i zdarzenia w bramie. Informacje o źródle połączenia są dostępne tylko dla połączeń IKEv2 |

## <a name="set-up-alerts-in-the-azure-portal"></a><a name="setup"></a>Konfigurowanie alertów w witrynie Azure portal

Poniższe kroki przykład utworzy alert dla zdarzenia rozłączenia, który obejmuje tunel sieci VPN lokacji do lokacji:


1. W witrynie Azure portal wyszukaj **usługę Log Analytics** w obszarze Wszystkie **usługi** i wybierz **obszary robocze usługi Log Analytics**.

   ![Wybory dotyczące przechodzenia do obszarów roboczych usługi Log Analytics](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert0.png "Utwórz")

2. Wybierz **pozycję Utwórz** na stronie **Analiza dzienników.**

   ![Strona Usługi Log Analytics za pomocą przycisku Utwórz](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert1.png  "Wybierz pozycję")

3. Wybierz **pozycję Utwórz nowy** i wypełnij szczegóły.

   ![Szczegóły dotyczące tworzenia obszaru roboczego usługi Log Analytics](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert2.png  "Wybierz pozycję")

4. Znajdź swoją bramę sieci VPN w bloku**Ustawienia diagnostyki** **monitora.** > 

   ![Wybór do znajdowania bramy sieci VPN w ustawieniach diagnostycznych](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert3.png  "Wybierz pozycję")

5. Aby włączyć diagnostykę, kliknij dwukrotnie bramę, a następnie wybierz pozycję **Włącz diagnostykę**.

   ![Wybór do włączania diagnostyki](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert4.png  "Wybierz pozycję")

6. Wypełnij szczegóły i upewnij się, że wybrano **opcję Wyślij do usługi Log Analytics** i **TunnelDiagnosticLog.** Wybierz obszar roboczy usługi Log Analytics utworzony w kroku 3.

   ![Zaznaczone pola wyboru](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert5.png  "Wybierz pozycję")

   > [!NOTE]
   > Może upłynąć kilka godzin, aby dane pojawiły się początkowo.

7. Przejdź do przeglądu zasobu bramy sieci wirtualnej i wybierz **alerty** na karcie **Monitorowanie.** Następnie utwórz nową regułę alertu lub edytuj istniejącą regułę alertu.

   ![Wybory do tworzenia nowej reguły alertu](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert6.png  "Wybierz pozycję")

   ![punkt do lokacji](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert6.png  "Wybierz pozycję")
8. Wybierz obszar roboczy usługi Log Analytics i zasób.

   ![Wybór obszaru roboczego i zasobu](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert7.png  "Wybierz pozycję")

9. Wybierz **niestandardowe wyszukiwanie dzienników** jako logikę sygnału w **obszarze Dodaj warunek**.

   ![Wybór niestandardowego wyszukiwania dziennika](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert8.png  "Wybierz pozycję")

10. Wprowadź następującą kwerendę w polu **tekstowym Kwerenda wyszukiwania.** Zastąp wartości w <> i TimeGenerated odpowiednio.

    ```
    AzureDiagnostics
    | where Category == "TunnelDiagnosticLog"
    | where _ResourceId == tolower("<RESOURCEID OF GATEWAY>")
    | where TimeGenerated > ago(5m) 
    | where remoteIP_s == "<REMOTE IP OF TUNNEL>"
    | where status_s == "Disconnected"
    | project TimeGenerated, OperationName, instance_s, Resource, ResourceGroup, _ResourceId 
    | sort by TimeGenerated asc
    ```

    Ustaw wartość progową na 0 i wybierz **opcję Gotowe**.

    ![Wprowadzanie kwerendy i wybieranie progu](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert9.png  "Wybierz pozycję")

11. Na stronie **Reguła tworzenia** wybierz pozycję **Utwórz nowy** w sekcji **GRUPY AKCJI.** Wypełnij szczegóły i wybierz **PRZYCISK OK**.

    ![Szczegółowe informacje o nowej grupie akcji](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert10.png  "Wybierz pozycję")

12. Na stronie **Tworzenie reguły** wprowadź szczegóły dotyczące **Dostosowywania akcji** i upewnij się, że w sekcji **NAZWA GRUPY AKCJI** pojawi się poprawna nazwa. Wybierz **pozycję Utwórz regułę alertu,** aby utworzyć regułę.

    ![Wybory do tworzenia reguły](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert11.png  "Wybierz pozycję")

## <a name="set-up-alerts-by-using-powershell"></a><a name="setuppowershell"></a>Konfigurowanie alertów przy użyciu programu PowerShell

Poniższe kroki przykładowe utworzyć alert dla zdarzenia rozłączenia, który obejmuje tunel sieci VPN lokacji do lokacji.

1. Tworzenie obszaru roboczego usługi Log Analytics:

   ```powershell
   $Location           = 'westus2'
   $ResourceGroupName  = 'TestRG1'
   $Sku                = 'pergb2018'
   $WorkspaceName      = 'LogAnalyticsWS123'

   New-AzOperationalInsightsWorkspace -Location $Location -Name $WorkspaceName -Sku $Sku -ResourceGroupName $ResourceGroupName
   ```

2. Włącz diagnostykę bramy sieci VPN:

   ```powershell
   $ResourceGroupName  = 'TestRG1'
   $VpnGatewayName     = 'VNet1GW'
   $WorkspaceName      = 'LogAnalyticsWS123'

   $VpnGateway         = Get-AzVirtualNetworkGateway -Name $VpnGatewayName -ResourceGroupName $ResourceGroupName
   $Workspace          = Get-AzOperationalInsightsWorkspace -Name $WorkspaceName -ResourceGroupName $ResourceGroupName

   Set-AzDiagnosticSetting `
       -Name 'VPN tunnel' `
       -ResourceId $VpnGateway.Id `
       -WorkspaceId $Workspace.ResourceId `
       -Enabled $true `
       -Category 'TunnelDiagnosticLog'
   ```

3. Tworzenie grupy akcji.

   Ten kod tworzy grupę akcji, która wysyła powiadomienie e-mail po wyzwoleniu alertu:

   ```powershell
   $ActionGroupName            = 'EmailAdmins'   # Max. 60 characters long
   $ActionGroupShortName       = 'EmailAdmins'   # Max. 12 characters long
   $ActionGroupReceiverName    = 'My receiver Name'
   $EmailAddress               = 'xyz@contoso.com'
   $ResourceGroupName          = 'TestRG1'

   $ActionGroupReceiver = New-AzActionGroupReceiver -Name $ActionGroupReceiverName -UseCommonAlertSchema -EmailReceiver -EmailAddress $EmailAddress

   Set-AzActionGroup `
      -ResourceGroupName $ResourceGroupName `
      -Name $ActionGroupName `
      -ShortName $ActionGroupShortName `
      -Receiver @($ActionGroupReceiver)
   ```

4. Utwórz regułę alertu na podstawie niestandardowego wyszukiwania w dzienniku:

   ```powershell
   $ActionGroupName    = 'EmailAdmins'
   $EmailSubject       = 'Redmond VPN tunnel is disconnected'
   $Location           = 'westus2'
   $RemoteIp           = '104.42.209.46'
   $ResourceGroupName  = 'TestRG1'
   $VpnGatewayName     = 'VNet1GW'
   $WorkspaceName      = 'LogAnalyticsWS123'

   $VpnGateway         = Get-AzVirtualNetworkGateway -Name $VpnGatewayName -ResourceGroupName $ResourceGroupName
   $Workspace          = Get-AzOperationalInsightsWorkspace -Name $WorkspaceName -ResourceGroupName $ResourceGroupName

   $Query = @"
   AzureDiagnostics |
   where Category == "TunnelDiagnosticLog" |
   where TimeGenerated > ago(5m) |
   where _ResourceId == tolower("$($VpnGateway.id)") |
   where remoteIP_s == "$($RemoteIp)" |
   where status_s == "Disconnected" |
   project TimeGenerated, OperationName, instance_s, Resource, ResourceGroup, _ResourceId |
   sort by TimeGenerated asc
   "@

   $Source             = New-AzScheduledQueryRuleSource -Query $Query -DataSourceId $Workspace.ResourceId
   $Schedule           = New-AzScheduledQueryRuleSchedule -FrequencyInMinutes 5 -TimeWindowInMinutes 5
   $TriggerCondition   = New-AzScheduledQueryRuleTriggerCondition -ThresholdOperator 'GreaterThan' -Threshold 0

   $ActionGroup        = Get-AzActionGroup -ResourceGroupName $ResourceGroupName -Name $ActionGroupName
   $AznsActionGroup    = New-AzScheduledQueryRuleAznsActionGroup -ActionGroup $ActionGroup.Id -EmailSubject $EmailSubject
   $AlertingAction     = New-AzScheduledQueryRuleAlertingAction -AznsAction $AznsActionGroup -Severity '1' -Trigger $TriggerCondition

   New-AzScheduledQueryRule `
       -ResourceGroupName $ResourceGroupName `
       -Location $Location `
       -Action $AlertingAction `
       -Enabled $true `
       -Description 'The tunnel between Azure and Redmond with IP address 104.42.209.46 is disconnected' `
       -Schedule $Schedule `
       -Source $Source `
       -Name 'The Azure to Redmond tunnel is disconnected'
   ```

## <a name="next-steps"></a>Następne kroki

Aby skonfigurować alerty dotyczące metryk tunelu, zobacz [Konfigurowanie alertów w metrykach bramy sieci VPN](vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric.md).
