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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "75750708"
---
### <a name="enable-logging-with-diagnostics-settings"></a>Włączanie rejestrowania z ustawieniami diagnostycznymi

[!INCLUDE [updated-for-az](./updated-for-az.md)]

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) i przejdź do centrum IoT Hub.

2. Wybierz **ustawienia diagnostyki**.

3. Wybierz **włącz diagnostykę**.

   ![Włączanie diagnostyki](./media/iot-hub-diagnostics-settings/turnondiagnostics.png)

4. Nadaj nazwę ustawień diagnostycznych.

5. Wybierz miejsce, w którym chcesz wysłać dzienniki. Można wybrać dowolną kombinację trzech opcji:

   * Zarchiwizuj na koncie magazynu
   * Przesyłaj strumieniowo do centrum zdarzeń
   * Wysyłanie do usługi Log Analytics

6. Wybierz operacje, które chcesz monitorować, i włącz dzienniki dla tych operacji. Operacje, które ustawienia diagnostyczne mogą raportować są następujące:

   * Połączenia
   * Telemetria urządzenia
   * Komunikaty z chmury do urządzenia
   * Operacje tożsamości urządzenia
   * Operacje przekazywania plików
   * Routing komunikatów
   * Operacje bliźniaczeczki chmury na urządzenia
   * Operacje bliźniaczych między urządzeniami do chmury
   * Operacje bliźniacze
   * Operacje zlecenia
   * Metody bezpośrednie  
   * Śledzenie rozproszone (wersja zapoznawcza)
   * Konfiguracje
   * Strumienie urządzeń
   * Metryki urządzenia

6. Zapisz nowe ustawienia. 

Jeśli chcesz włączyć ustawienia diagnostyki za pomocą programu PowerShell, użyj następującego kodu:

```azurepowershell
Connect-AzAccount
Select-AzSubscription -SubscriptionName <subscription that includes your IoT Hub>
Set-AzDiagnosticSetting -ResourceId <your resource Id> -ServiceBusRuleId <your service bus rule Id> -Enabled $true
```

Nowe ustawienia zajmą się efektem około 10 minut. Następnie dzienniki są wyświetlane w skonfigurowanym celu archiwizacji w bloku **Ustawienia diagnostyki.** Aby uzyskać więcej informacji na temat konfigurowania diagnostyki, zobacz [Zbieranie i korzystanie z danych dziennika z zasobów platformy Azure](../articles/azure-monitor/platform/platform-logs-overview.md).
