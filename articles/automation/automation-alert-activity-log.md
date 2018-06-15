---
title: Monitorowanie z alertami dzienniki działania elementu runbook usługi Automatyzacja Azure
description: W tym artykule przedstawiono monitorowania elementu runbook usługi Automatyzacja Azure z dziennika aktywności
services: automation
ms.service: automation
author: georgewallace
ms.author: gwallace
ms.date: 05/17/2018
ms.topic: article
manager: carmonm
ms.openlocfilehash: a0bd291af98477308cda898580fd52e33b1e6bbd
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/20/2018
ms.locfileid: "34360386"
---
# <a name="monitoring-runbooks-with-azure-activity-log-alerts"></a>Monitorowanie elementów runbook z alertami dziennik aktywności platformy Azure

W tym artykule należy dowiedzieć się, jak tworzyć alerty na podstawie stanu ukończenia elementów runbook.

## <a name="log-in-to-azure"></a>Zaloguj się do platformy Azure.

Logowanie do platformy Azure w https://portal.azure.com

## <a name="create-alert"></a>Utwórz alert

Alerty umożliwiają definiowanie warunek do monitorowania i akcji w sytuacji, gdy ten warunek jest spełniony.

W portalu Azure, przejdź do **wszystkie usługi** i wybierz **Monitor**. Na stronie monitora wybierz **alerty** i kliknij przycisk **+ nową regułę alertu**.

### <a name="define-the-alert-condition"></a>Zdefiniuj warunek alertu

1. W obszarze **1. Zdefiniuj warunek alertu**, kliknij przycisk **+ wybierz docelowy**. Wybierz subskrypcję, a następnie w obszarze **Filtruj według typu zasobu**, wybierz pozycję **kont automatyzacji**. Wybierz konto automatyzacji, a następnie kliknij przycisk **gotowe**.

   ![Wybierz zasób dla alertu](./media/automation-alert-activity-log/select-resource.png)

### <a name="configure-alert-criteria"></a>Skonfiguruj kryteria alertu

1. Kliknij przycisk **+ Dodaj kryteria**. Wybierz **metryki** dla **sygnału typu**i wybierz polecenie **zadań** z tabeli.

1. **Konfiguruj sygnał logiki** strona jest, gdzie został zdefiniowany logiki wyzwalać alert. W obszarze wykresu historycznych, dostępne są dwa wymiary **nazwy elementu Runbook** i **stan**. Wymiary są inne właściwości dla metryki, który może służyć do filtrowania wyników. Aby uzyskać **nazwy elementu Runbook**, wybierz element runbook ma być alert po wystąpieniu lub pozostaw puste pole alertu na wszystkich elementów runbook. Aby uzyskać **stanu**, wybierz z listy rozwijanej, aby monitorować stan. Element runbook nazwę i stan wartości wyświetlane na liście rozwijanej są tylko w przypadku zadań uruchomionych w zeszłym tygodniu.

   Jeśli alert po wystąpieniu stanu lub elementu runbook, który nie jest wyświetlany na liście rozwijanej kliknij przycisk **\+** obok wymiaru. Spowoduje to otwarcie okna dialogowego, które pozwala na wprowadzenie w niestandardowej wartości, która nie ma wysyłanego dla tego wymiaru na ostatnio. Jeśli zostanie wprowadzona wartość, która nie istnieje dla właściwości alertu nie zostanie wyzwolone.

1. W obszarze **alertów logiki**, Zdefiniuj warunek i wartość progową alertu. Poniżej przedstawiono Podgląd warunku zdefiniowane.

1. W obszarze **Evaluated na podstawie** wybierz zakres czasu dla zapytania i jak często ma zapytania został uruchomiony. Na przykład, jeśli wybierzesz **w ciągu ostatnich 5 minut** dla **okres** i **co minutę 1** dla **częstotliwość**, alert wyszukuje numer elementów runbook, spełnieniu kryteriów w ciągu ostatnich 5 minut. To zapytanie jest uruchomione co minutę, a po kryteria alertu, który zdefiniowano już nie znaleziono w oknie 5-minutowy, alert rozpoznaje samej siebie. Gdy skończysz, kliknij przycisk **gotowe**.

   ![Wybierz zasób dla alertu](./media/automation-alert-activity-log/configure-signal-logic.png)

### <a name="define-alert-details"></a>Zdefiniuj szczegóły alertu

1. W obszarze **2. Zdefiniuj szczegóły alertu**, nadaj alert przyjazną nazwę i opis. Ustaw **ważność** odpowiadające warunku alertu. Brak pięciu ważności z zakresu od 0 do 5. Alerty są traktowane niezależne od tego samego priorytetu, można dopasować ważność odpowiadające logiki biznesowej.

1. W dolnej części sekcji jest przycisku, który pozwala na włączenie reguły po zakończeniu. Domyślnie zasady są włączone podczas tworzenia. Jeśli wybierzesz przycisk nie, można utworzyć alertu i jest tworzony w **wyłączone** stanu. Z **reguły** strony w monitorze Azure, zaznacz go i kliknij **włączyć** umożliwia alert, gdy wszystko będzie gotowe.

### <a name="define-the-action-to-take"></a>Zdefiniuj akcję do wykonania

1. W obszarze **3. Zdefiniuj grupy akcji**, kliknij przycisk **+ nowe grupy akcji**. Akcja grupa jest grupą akcji, które można użyć w wielu alertów. Te mogą obejmować, ale nie są ograniczone do wiadomości e-mail powiadomienia, elementy runbook, elementów webhook i wiele innych. Aby dowiedzieć się więcej o grupach akcji, zobacz [Utwórz i Zarządzaj grupami akcji](../monitoring-and-diagnostics/monitoring-action-groups.md)

1. W **nazwy grupy akcji** polu, nadaj przyjazną nazwę i krótką nazwę. Krótka nazwa jest używana zamiast akcji Pełna nazwa grupy, podczas powiadomienia są wysyłane przy użyciu tej grupy.

1. W **akcje** w obszarze **typ akcji**, wybierz pozycję **E-mail/SMS/wypychanej/głosu**.

1. Na **E-mail/SMS/wypychanej/głosu** strony, nadaj mu nazwę. Sprawdź **E-mail** wyboru i wpisz w prawidłowy adres e-mail ma być używany.

   ![Skonfiguruj grupy akcji poczty e-mail](./media/automation-alert-activity-log/add-action-group.png)

1. Kliknij przycisk **OK** na **E-mail/SMS/wypychanej/głosu** strony, aby je zamknąć, a następnie kliknij przycisk **OK** zamknąć **Dodaj grupę akcji** strony. Nazwa określona na tej stronie jest zapisywany jako **Nazwa akcji**.

1. Po zakończeniu kliknij przycisk **Zapisz**. Spowoduje to utworzenie reguły, która ostrzega w przypadku elementu runbook została ukończona z określonym stanie.

> [!NOTE]
> Podczas dodawania adresu e-mail do grupy akcji, są wysyłane powiadomienia e-mail, informujący, że adres został dodany do grupy akcji.

## <a name="notification"></a>Powiadomienia

Po spełnieniu kryteriów alertu grupy akcji uruchamia czynność zdefiniowana. W tym artykule przykład zostanie wysłana wiadomość e-mail. Poniższa ilustracja jest przykładem wiadomości e-mail, który pojawi się po wyzwoleniu alertu:

![Alerty e-mail](./media/automation-alert-activity-log/alert-email.png)

Po Metryka nie jest już poza wartość progową, określone, alert jest dezaktywowana i grupy akcji uruchamia czynność zdefiniowana. Jeśli wybrano typ akcji poczty e-mail, wiadomość e-mail z rozwiązania są wysyłane, informujący, że został rozwiązany.

## <a name="next-steps"></a>Kolejne kroki

Nadal następujący artykuł, aby dowiedzieć się więcej o innych metodach integrowanie alertings na koncie automatyzacji.

> [!div class="nextstepaction"]
> [Użyj alertu można wyzwolić elementu runbook usługi Automatyzacja Azure](automation-create-alert-triggered-runbook.md)