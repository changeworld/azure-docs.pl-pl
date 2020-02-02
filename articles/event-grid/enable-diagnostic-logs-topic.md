---
title: Azure Event Grid — Włączanie dzienników diagnostycznych tematu
description: Ten artykuł zawiera instrukcje krok po kroku dotyczące włączania dzienników diagnostycznych tematu usługi Azure Event Grid.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: how-to
ms.date: 01/30/2020
ms.author: spelluru
ms.openlocfilehash: a32d26629bb9efed4a4f1f49eee2f0534e1873a9
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/01/2020
ms.locfileid: "76960505"
---
#  <a name="diagnostic-logs-for-an-azure-event-grid-topic"></a>Dzienniki diagnostyczne tematu usługi Azure Event Grid
Ustawienia diagnostyczne umożliwiają użytkownikom Event Grid przechwytywanie i wyświetlanie dzienników błędów publikowania i dostarczania w jednym z następujących miejsc: konto usługi Azure Storage, centrum zdarzeń lub obszar roboczy Log Analytics. Ten artykuł zawiera instrukcje krok po kroku dotyczące włączania dzienników diagnostycznych tematu usługi Event Grid.

## <a name="prerequisites"></a>Wymagania wstępne

- Temat zainicjowanej siatki zdarzeń
- Obsługiwane miejsce docelowe przechwytywania dzienników diagnostycznych. Może być jednym z następujących miejsc docelowych:
    - Konto magazynu Azure
    - Centrum zdarzeń
    - Obszar roboczy usługi Log Analytics


## <a name="steps-for-enabling-diagnostic-logs-for-a-topic"></a>Procedura włączania dzienników diagnostycznych w temacie

1. Zaloguj się do [Portalu Azure](https://portal.azure.com).
2. Przejdź do tematu usługi Event Grid, dla którego chcesz włączyć ustawienia dziennika diagnostycznego. 
3. Wybierz pozycję **Ustawienia diagnostyczne** w obszarze **monitorowanie** w menu po lewej stronie.
4. Na stronie **Ustawienia diagnostyczne** wybierz opcję **Dodaj nowe ustawienie diagnostyczne**. 
    
    ![Dodaj przycisk ustawień diagnostycznych](./media/enable-diagnostic-logs-topic/diagnostic-settings-add.png)
5. Określ **nazwę** ustawienia diagnostycznego. 

    ![Ustawienia diagnostyczne — nazwa](./media/enable-diagnostic-logs-topic/diagnostic-settings-name.png)     
6. Włącz co najmniej jeden z lokalizacji docelowych przechwytywania dla dzienników, a następnie skonfiguruj je, wybierając poprzedni utworzony zasób przechwytywania. 
    - Jeśli wybierzesz opcję **Archiwizuj na koncie magazynu**, wybierz pozycję **konto magazynu — konfiguracja**, a następnie wybierz konto magazynu w ramach subskrypcji platformy Azure. 

        ![Archiwizowanie na koncie usługi Azure Storage](./media/enable-diagnostic-logs-topic/archive-storage.png)
    - W przypadku wybrania opcji **strumień do centrum zdarzeń**wybierz pozycję **centrum zdarzeń — Skonfiguruj**, a następnie wybierz Event Hubs przestrzeń nazw, centrum zdarzeń i zasady dostępu. 
        ![strumienia do centrum zdarzeń](./media/enable-diagnostic-logs-topic/archive-event-hub.png)
    - W przypadku wybrania opcji **Wyślij do log Analytics**wybierz obszar roboczy log Analytics.
        ![Wyślij do Log Analytics](./media/enable-diagnostic-logs-topic/send-log-analytics.png)
7. Wybierz opcje **DeliveryFailures** i **PublishFailures** w sekcji **Dziennik** . 
    ![wybrać błędy](./media/enable-diagnostic-logs-topic/log-failures.png)
8. Wybierz pozycję **Zapisz**. W prawym górnym rogu wybierz pozycję **X** , aby zamknąć stronę. 
9. Teraz wróć na stronę **Ustawienia diagnostyczne** , aby potwierdzić, że w tabeli **ustawień diagnostycznych** jest wyświetlany nowy wpis. 
    ![ustawienia diagnostyki na liście](./media/enable-diagnostic-logs-topic/diagnostic-setting-list.png)

     Możesz również włączyć zbieranie wszystkich metryk dla tematu. 

## <a name="next-steps"></a>Następne kroki
Jeśli potrzebujesz więcej pomocy, Opublikuj swój problem na [forum Stack Overflow](https://stackoverflow.com/questions/tagged/azure-eventgrid) lub Otwórz [bilet pomocy technicznej](https://azure.microsoft.com/support/options/). 
