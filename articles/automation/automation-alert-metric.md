---
title: Monitorowanie z alertami metryki elementu runbook usługi Automatyzacja Azure
description: W tym artykule przedstawiono monitorowania na podstawie metryk elementu runbook usługi Automatyzacja Azure
services: automation
ms.service: automation
author: georgewallace
ms.author: gwallace
ms.date: 05/17/2018
ms.topic: article
manager: carmonm
ms.openlocfilehash: a8a4b24e6b2503f64cc3fd7f4fd8c7400c547d4d
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34655075"
---
# <a name="monitoring-runbooks-with-metric-alerts"></a>Monitorowanie elementów runbook z alertami metryki

W tym artykule należy dowiedzieć się, jak tworzyć alerty na podstawie stanu ukończenia elementów runbook.

## <a name="log-in-to-azure"></a>Zaloguj się do platformy Azure.

Zaloguj się do platformy Azure na stronie https://portal.azure.com

## <a name="create-alert"></a>Utwórz alert

Alerty umożliwiają definiowanie warunek do monitorowania i akcji w sytuacji, gdy ten warunek jest spełniony.

W portalu Azure, przejdź do **wszystkie usługi** i wybierz **Monitor**. Na stronie monitora wybierz **alerty** i kliknij przycisk **+ nową regułę alertu**.

### <a name="define-the-alert-condition"></a>Zdefiniuj warunek alertu

1. W obszarze **1. Zdefiniuj warunek alertu**, kliknij przycisk **+ Wybierz docelowy**. Wybierz subskrypcję, a następnie w obszarze **Filtruj według typu zasobu**, wybierz pozycję **kont automatyzacji**. Wybierz konto automatyzacji, a następnie kliknij przycisk **gotowe**.

   ![Wybierz zasób dla alertu](./media/automation-alert-activity-log/select-resource.png)

### <a name="configure-alert-criteria"></a>Skonfiguruj kryteria alertu

1. Kliknij przycisk **+ Dodaj kryteria**. Wybierz **metryki** dla **sygnału typu**i wybierz polecenie **zadań** z tabeli.

1. **Konfiguruj sygnał logiki** strona jest, gdzie został zdefiniowany logiki wyzwalać alert. W obszarze wykresu historycznych, dostępne są dwa wymiary **nazwy elementu Runbook** i **stan**. Wymiary są inne właściwości dla metryki, który może służyć do filtrowania wyników. Aby uzyskać **nazwy elementu Runbook**, wybierz element runbook ma być alert po wystąpieniu lub pozostaw puste pole alertu na wszystkich elementów runbook. Aby uzyskać **stanu**, wybierz z listy rozwijanej, aby monitorować stan. Element runbook nazwę i stan wartości wyświetlane na liście rozwijanej są tylko w przypadku zadań uruchomionych w zeszłym tygodniu.

   Jeśli alert po wystąpieniu stanu lub elementu runbook, który nie jest wyświetlany na liście rozwijanej kliknij przycisk **\+** obok wymiaru. Spowoduje to otwarcie okna dialogowego, które pozwala na wprowadzenie w niestandardowej wartości, która nie ma wysyłanego dla tego wymiaru na ostatnio. Jeśli zostanie wprowadzona wartość, która nie istnieje dla właściwości alertu nie zostanie wyzwolone.

1. W obszarze **alertów logiki**, Zdefiniuj warunek i wartość progową alertu. Poniżej przedstawiono Podgląd warunku zdefiniowane.

1. W obszarze **Evaluated na podstawie** wybierz zakres czasu dla zapytania i jak często ma zapytania został uruchomiony. Na przykład, jeśli wybierzesz **w ciągu ostatnich 5 minut** dla **okres** i **co minutę 1** dla **częstotliwość**, alert wyszukuje numer elementów runbook, spełnieniu kryteriów w ciągu ostatnich 5 minut. To zapytanie jest uruchomione co minutę, a po kryteria alertu, który zdefiniowano już nie znaleziono w oknie 5-minutowy, alert rozpoznaje samej siebie. Na koniec kliknij przycisk **Gotowe**.

   ![Wybierz zasób dla alertu](./media/automation-alert-activity-log/configure-signal-logic.png)

### <a name="define-alert-details"></a>Zdefiniuj szczegóły alertu

1. W obszarze **2. Zdefiniuj szczegóły alertu**, nadaj mu przyjazną nazwę i podaj opis. Ustaw **ważność** odpowiadające warunku alertu. Brak pięciu ważności z zakresu od 0 do 5. Alerty są traktowane niezależne od tego samego priorytetu, można dopasować ważność odpowiadające logiki biznesowej.

1. W dolnej części sekcji jest przycisku, który pozwala na włączenie reguły po zakończeniu. Domyślnie zasady są włączone podczas tworzenia. Jeśli wybierzesz przycisk nie, można utworzyć alertu i jest tworzony w **wyłączone** stanu. Z **reguły** strony w monitorze Azure, zaznacz go i kliknij **włączyć** umożliwia alert, gdy wszystko będzie gotowe.

### <a name="define-the-action-to-take"></a>Zdefiniuj akcję do wykonania

1. W obszarze **3. Zdefiniuj grupę akcji** kliknij przycisk **+ Nowa grupa akcji**. Grupa akcji to grupa składająca się z akcji, których można używać w wielu alertach. Mogą one obejmować powiadomienia e-mail, elementy runbook i webhook oraz wiele innych. Aby dowiedzieć się więcej o grupach akcji, zobacz [Tworzenie i zarządzanie grupami akcji](../monitoring-and-diagnostics/monitoring-action-groups.md)

1. W polu **Nazwa grupy akcji** wpisz przyjazną nazwę i nazwę krótką. Nazwa krótka jest używana zamiast pełnej nazwy grupy akcji podczas przesyłania powiadomień przy użyciu danej grupy.

1. W **akcje** w obszarze **typ akcji**, wybierz pozycję **E-mail/SMS/wypychanej/głosu**.

1. Na stronie **E-mail/SMS/Push/Głos** nadaj nazwę. Zaznacz pole wyboru **E-mail** i wpisz prawidłowy adres e-mail, który ma być używany.

   ![Skonfiguruj grupę akcji poczty e-mail](./media/automation-alert-activity-log/add-action-group.png)

1. Kliknij przycisk **OK** na stronie **E-mail/SMS/Push/Głos**, aby ją zamknąć, a następnie kliknij przycisk **OK**, aby zamknąć stronę **Dodawanie grupy akcji**. Nazwa określona na tej stronie jest zapisywany jako **Nazwa akcji**.

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