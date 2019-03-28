---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: iot-hub
author: dominicbetts
ms.service: iot-hub
ms.topic: include
ms.date: 02/20/2019
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: dd5dc53311c8611a4ca4d174401bba797fe5c4b1
ms.sourcegitcommit: f24fdd1ab23927c73595c960d8a26a74e1d12f5d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2019
ms.locfileid: "58505859"
---
### <a name="enable-logging-with-diagnostics-settings"></a>Włącz logowanie za pomocą ustawień diagnostycznych

[!INCLUDE [updated-for-az](./updated-for-az.md)]

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) i przejdź do Centrum IoT hub.

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
   * Rozproszonego śledzenia (wersja zapoznawcza)
   * Konfiguracje
   * Strumienie urządzenia
   * Metryki urządzenia

6. Zapisz nowe ustawienia. 

Aby włączyć ustawień diagnostycznych przy użyciu programu PowerShell, należy użyć następującego kodu:

```azurepowershell
Connect-AzAccount
Select-AzSubscription -SubscriptionName <subscription that includes your IoT Hub>
Set-AzDiagnosticSetting -ResourceId <your resource Id> -ServiceBusRuleId <your service bus rule Id> -Enabled $true
```

Nowe ustawienia zaczną obowiązywać w ciągu około 10 minut. Po tym dzienniki są wyświetlane w celu archiwizacji skonfigurowanego na **ustawień diagnostycznych** bloku. Aby uzyskać więcej informacji na temat konfigurowania diagnostyki zobacz [zbieranie i używanie dane dzienników z zasobów platformy azure](../articles/azure-monitor/platform/diagnostic-logs-overview.md).
