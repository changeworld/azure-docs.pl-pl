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
ms.openlocfilehash: e518263fc7f427ade4943f91fdc840b62a235205
ms.sourcegitcommit: 71ee622bdba6e24db4d7ce92107b1ef1a4fa2600
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/17/2018
ms.locfileid: "53550027"
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

Nowe ustawienia zaczną obowiązywać w ciągu około 10 minut. Po tym dzienniki są wyświetlane w celu archiwizacji skonfigurowanego na **ustawień diagnostycznych** bloku. Aby uzyskać więcej informacji na temat konfigurowania diagnostyki zobacz [zbieranie i używanie dane dzienników z zasobów platformy azure](../articles/azure-monitor/platform/diagnostic-logs-overview.md).