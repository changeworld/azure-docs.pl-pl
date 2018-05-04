### <a name="enable-logging-with-diagnostics-settings"></a>Włącz rejestrowanie przy użyciu ustawień diagnostycznych

1. Zaloguj się do [portalu Azure] [ lnk-portal] i przejdź do Centrum IoT.
1. Wybierz **ustawień diagnostycznych**.
1. Wybierz **Włącz diagnostykę**.

   ![Włączanie diagnostyki][1]

1. Nadaj nazwę ustawienia diagnostyki.
1. Wybierz gdzie chcesz wysłać dzienniki. Można wybrać dowolną kombinację trzech opcji:
   * Zarchiwizuj na koncie magazynu
   * Przesyłaj strumieniowo do centrum zdarzeń
   * Wysyłanie do usługi Log Analytics
1. Wybierz operacje, które chcesz monitorować, a następnie włączyć dzienniki te operacje. Operacje, które będzie mógł zgłosić ustawień diagnostycznych są:
   * Połączenia
   * Telemetrii urządzenia
   * Komunikaty chmury do urządzenia
   * Operacje tożsamości urządzenia
   * Przekazywania plików
   * Kierowanie komunikatów
   * Operacje dwie chmury do urządzenia
   * Operacje dwie urządzenia do chmury
   * Dwie operacje
   * Operacje zadania
   * Metody bezpośrednie  
1. Zapisz nowe ustawienia. 

Jeśli chcesz włączyć ustawienia diagnostyki przy użyciu programu PowerShell, należy użyć poniższego kodu:

```azurepowershell
Connect-AzureRmAccount
Select-AzureRmSubscription -SubscriptionName <subscription that includes your IoT Hub>
Set-AzureRmDiagnosticSetting -ResourceId <your resource Id> -ServiceBusRuleId <your service bus rule Id> -Enabled $true
```

Nowe ustawienia zaczęły obowiązywać w ciągu około 10 minut. Po wykonaniu tej dzienników pojawia się w celu archiwizacji skonfigurowanych na **ustawień diagnostycznych** bloku. Aby uzyskać więcej informacji na temat konfigurowania diagnostyki, zobacz [zbierania i wykorzystywania danych dziennika z zasobów platformy azure][lnk-diagnostics-settings].

<!-- Images -->
[1]: ./media/iot-hub-diagnostics-settings/turnondiagnostics.png

<!-- Links -->
[lnk-portal]: https://portal.azure.com
[lnk-diagnostics-settings]: ../articles/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md
