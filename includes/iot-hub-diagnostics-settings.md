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
ms.openlocfilehash: 3778ec2fac13aee29ce361402a535ca70fd56c33
ms.sourcegitcommit: 380e3c893dfeed631b4d8f5983c02f978f3188bf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/08/2020
ms.locfileid: "75750708"
---
### <a name="enable-logging-with-diagnostics-settings"></a>Włączanie rejestrowania przy użyciu ustawień diagnostycznych

[!INCLUDE [updated-for-az](./updated-for-az.md)]

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) i przejdź do Centrum IoT hub.

2. Wybierz pozycję **Ustawienia diagnostyki**.

3. Wybierz **Włącz diagnostykę**.

   ![Włączanie diagnostyki](./media/iot-hub-diagnostics-settings/turnondiagnostics.png)

4. Nadaj nazwę ustawieniom diagnostycznym.

5. Wybierz miejsce, do którego chcesz wysłać dzienniki. Można wybrać dowolną kombinację trzech opcji:

   * Zarchiwizuj na koncie magazynu
   * Przesyłaj strumieniowo do centrum zdarzeń
   * Wysyłanie do usługi Log Analytics

6. Wybierz operacje, które chcesz monitorować, i Włącz dzienniki dla tych operacji. Dla operacji, które mogą być zgłaszane przez ustawienia diagnostyczne, są następujące:

   * Połączenia
   * Dane telemetryczne urządzenia
   * Komunikaty z chmury do urządzenia
   * Operacje tożsamości urządzeń
   * Operacje przekazywania plików
   * Routing komunikatów
   * Operacje na sznurze z chmury do urządzenia
   * Operacje wieloosiowe między urządzeniami a chmurą
   * Operacje na przędzce
   * Operacje zadań
   * Metody bezpośrednie  
   * Śledzenie rozproszone (wersja zapoznawcza)
   * Konfiguracje
   * Strumienie urządzeń
   * Metryki urządzeń

6. Zapisz nowe ustawienia. 

Jeśli chcesz włączyć ustawienia diagnostyki przy użyciu programu PowerShell, użyj następującego kodu:

```azurepowershell
Connect-AzAccount
Select-AzSubscription -SubscriptionName <subscription that includes your IoT Hub>
Set-AzDiagnosticSetting -ResourceId <your resource Id> -ServiceBusRuleId <your service bus rule Id> -Enabled $true
```

Nowe ustawienia zaczną obowiązywać w ciągu około 10 minut. Następnie dzienniki są wyświetlane w skonfigurowanym miejscu docelowym w bloku **Ustawienia diagnostyki** . Aby uzyskać więcej informacji o konfigurowaniu diagnostyki, zobacz [zbieranie i korzystanie z danych dzienników z zasobów platformy Azure](../articles/azure-monitor/platform/platform-logs-overview.md).
