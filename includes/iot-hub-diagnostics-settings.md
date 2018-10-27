---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: iot-hub
author: dominicbetts
ms.service: iot-hub
ms.topic: include
ms.date: 05/17/2018
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: 0039c4eff3571a96cba1ab36136e0a588d78eb75
ms.sourcegitcommit: 48592dd2827c6f6f05455c56e8f600882adb80dc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/26/2018
ms.locfileid: "50159132"
---
### <a name="enable-logging-with-diagnostics-settings"></a>Włącz logowanie za pomocą ustawień diagnostycznych

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) i przejdź do Centrum IoT Hub.

2. Wybierz **ustawień diagnostycznych**.

3. Wybierz **Włącz diagnostykę**.

   ![Włączanie diagnostyki](./media/iot-hub-diagnostics-settings/turnondiagnostics.png)

4. Nazwij ustawień diagnostycznych.

5. Wybierz gdzie chcesz wysłać dzienniki. Możesz wybrać dowolną kombinację trzech opcji:

   * Zarchiwizuj na koncie magazynu
   * Przesyłaj strumieniowo do centrum zdarzeń
   * Wysyłanie do usługi Log Analytics

6. Wybierz operacje, które chcesz monitorować, a następnie włączyć dzienniki dla tych operacji. Dostępne są następujące operacje, które można raportować ustawień diagnostycznych:

   * Połączenia
   * Danych telemetrycznych z urządzenia
   * Komunikaty z chmury do urządzenia
   * Operacje dotyczące tożsamości urządzenia
   * Operacje przekazywania plików
   * Kierowanie komunikatów
   * Operacje bliźniaczej reprezentacji chmury do urządzenia
   * Operacje bliźniaczej reprezentacji urządzenia do chmury
   * Operacje bliźniaczych reprezentacji
   * Operacje zadania
   * Metody bezpośrednie  

6. Zapisz nowe ustawienia. 

Aby włączyć ustawień diagnostycznych przy użyciu programu PowerShell, należy użyć następującego kodu:

```azurepowershell
Connect-AzureRmAccount
Select-AzureRmSubscription -SubscriptionName <subscription that includes your IoT Hub>
Set-AzureRmDiagnosticSetting -ResourceId <your resource Id> -ServiceBusRuleId <your service bus rule Id> -Enabled $true
```

Nowe ustawienia zaczną obowiązywać w ciągu około 10 minut. Po tym dzienniki są wyświetlane w celu archiwizacji skonfigurowanego na **ustawień diagnostycznych** bloku. Aby uzyskać więcej informacji na temat konfigurowania diagnostyki zobacz [zbieranie i używanie dane dzienników z zasobów platformy azure](../articles/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md).