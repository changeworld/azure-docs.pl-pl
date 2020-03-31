---
title: Usługa Azure Event Grid — włącz dzienniki diagnostyczne dla tematu
description: Ten artykuł zawiera instrukcje krok po kroku dotyczące włączania dzienników diagnostycznych dla tematu siatki zdarzeń platformy Azure.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: how-to
ms.date: 01/30/2020
ms.author: spelluru
ms.openlocfilehash: a32d26629bb9efed4a4f1f49eee2f0534e1873a9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76960505"
---
#  <a name="diagnostic-logs-for-an-azure-event-grid-topic"></a>Dzienniki diagnostyczne dla tematu siatki zdarzeń platformy Azure
Ustawienia diagnostyczne umożliwiają użytkownikom usługi Event Grid przechwytywanie i wyświetlanie dzienników błędów publikowania i dostarczania w jednym z następujących miejsc: konto magazynu platformy Azure, centrum zdarzeń lub obszar roboczy usługi Log Analytics. Ten artykuł zawiera instrukcje krok po kroku, aby włączyć dzienniki diagnostyczne dla tematu siatki zdarzeń.

## <a name="prerequisites"></a>Wymagania wstępne

- Temat siatki zdarzenia aprowizowanego
- A aprowizowana miejsce docelowe przechwytywania dzienników diagnostycznych. Może to być jedno z następujących miejsc docelowych:
    - Konto magazynu Azure
    - Centrum zdarzeń
    - Obszar roboczy usługi Log Analytics


## <a name="steps-for-enabling-diagnostic-logs-for-a-topic"></a>Kroki włączania dzienników diagnostycznych dla tematu

1. Zaloguj się do [Portalu Azure](https://portal.azure.com).
2. Przejdź do tematu siatki zdarzeń, dla którego chcesz włączyć ustawienia dziennika diagnostycznego. 
3. Wybierz **pozycję Ustawienia diagnostyczne** w obszarze **Monitorowanie** w menu po lewej stronie.
4. Na stronie **Ustawienia diagnostyczne** wybierz pozycję **Dodaj nowe ustawienie diagnostyczne**. 
    
    ![Przycisk Dodaj ustawienie diagnostyczne](./media/enable-diagnostic-logs-topic/diagnostic-settings-add.png)
5. Określ **nazwę** ustawienia diagnostycznego. 

    ![Ustawienia diagnostyczne - nazwa](./media/enable-diagnostic-logs-topic/diagnostic-settings-name.png)     
6. Włącz co najmniej jedno miejsce docelowe przechwytywania dzienników, a następnie skonfiguruj je, wybierając poprzedni utworzony zasób przechwytywania. 
    - Jeśli wybierzesz **Archiwum do konta magazynu,** wybierz **konto magazynu - Konfiguruj**, a następnie wybierz konto magazynu w subskrypcji platformy Azure. 

        ![Archiwizuj na koncie magazynu platformy Azure](./media/enable-diagnostic-logs-topic/archive-storage.png)
    - Jeśli wybierzesz **opcję Strumień do centrum zdarzeń,** wybierz pozycję Centrum zdarzeń — **Konfiguruj**, a następnie wybierz obszar nazw Centrum zdarzeń, Centrum zdarzeń i zasady dostępu. 
        ![Przesyłanie strumieniowe do centrum zdarzeń](./media/enable-diagnostic-logs-topic/archive-event-hub.png)
    - Jeśli wybierzesz **opcję Wyślij do usługi Log Analytics,** wybierz obszar roboczy Usługi Log Analytics.
        ![Wysyłanie do usługi Log Analytics](./media/enable-diagnostic-logs-topic/send-log-analytics.png)
7. Wybierz **DeliveryFailures** i **PublishFailures** opcje w **dzienniku** sekcji. 
    ![Wybierz błędy](./media/enable-diagnostic-logs-topic/log-failures.png)
8. Wybierz **pozycję Zapisz**. Wybierz **x** w prawym rogu, aby zamknąć stronę. 
9. Teraz wróć do strony **Ustawienia diagnostyczne,** upewnij się, że zostanie wyświetlony nowy wpis w **tabeli Ustawienia diagnostyki.** 
    ![Ustawienie diagnostyczne na liście](./media/enable-diagnostic-logs-topic/diagnostic-setting-list.png)

     Można również włączyć kolekcję wszystkich metryk dla tematu. 

## <a name="next-steps"></a>Następne kroki
Jeśli potrzebujesz więcej pomocy, opublikuj swój problem na [forum Stack Overflow](https://stackoverflow.com/questions/tagged/azure-eventgrid) lub otwórz [bilet pomocy technicznej](https://azure.microsoft.com/support/options/). 
