---
title: Monitorowanie usługi Azure Automation runbook z alertów dotyczących metryk
description: Ten artykuł przeprowadzi Cię przez elementy runbook usługi Azure Automation, na podstawie metryk monitorowania
services: automation
ms.service: automation
author: georgewallace
ms.author: gwallace
ms.date: 11/01/2018
ms.topic: article
manager: carmonm
ms.openlocfilehash: 7932d057a348957d369ba325044055ac8dfe3428
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "62119898"
---
# <a name="monitoring-runbooks-with-metric-alerts"></a>Monitorowanie elementów runbook z alertów dotyczących metryk

W tym artykule dowiesz się, jak tworzyć alerty na podstawie stanu ukończenia elementów runbook.

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Zaloguj się do platformy Azure w witrynie https://portal.azure.com

## <a name="create-alert"></a>Tworzenie alertu

Alerty umożliwiają zdefiniowanie warunku na potrzeby monitorowania i akcję do wykonania w przypadku spełnienia warunku.

W witrynie Azure portal przejdź do swojego konta usługi Automation. W obszarze **monitorowanie**, wybierz opcję **alerty** i kliknij przycisk **+ Nowa reguła alertu**. Zakres docelowy został już zdefiniowany do konta usługi Automation.

### <a name="configure-alert-criteria"></a>Skonfiguruj kryteria alertu

1. Kliknij przycisk **+ Dodaj kryteria**. Wybierz **metryki** dla **sygnał typu**i wybierz polecenie **łącznej liczby zadań** z tabeli.

2. **Konfigurowanie logiki sygnału** strona jest, gdzie należy zdefiniować logikę, która wyzwala alert. W obszarze wykresu historycznych, dostępne są dwa wymiary **nazwa elementu Runbook** i **stan**. Wymiary są różne właściwości metryk, który może służyć do filtrowania wyników. Aby uzyskać **nazwa elementu Runbook**, wybierz element runbook ma być alert po wystąpieniu lub pozostaw pustą wartość alert na wszystkich elementów runbook. Aby uzyskać **stan**, wybierz stan z listy rozwijanej, aby monitorować. Element runbook nazwę i stan wartości, które pojawiają się na liście rozwijanej są tylko w przypadku zadań uruchomionych w ostatnim tygodniu.

   Jeśli chcesz utworzyć alerty dotyczące stanu lub element runbook, który nie jest wyświetlana na liście rozwijanej, kliknij przycisk **\+** obok wymiaru. Ta akcja powoduje otwarcie okna dialogowego, która pozwala na wprowadź wartość niestandardową nie zostało ostatnio wyemitowane dla tego wymiaru. Jeśli zostanie wprowadzona wartość, która nie istnieje dla właściwości alertu nie będą wyzwalane.

   > [!NOTE]
   > Jeśli nie zastosujesz nazwę **RunbookName** wymiaru, jeśli istnieją wszelkie elementy runbook spełniają kryteria stanu, w tym systemie ukryte elementy runbook, zostanie wyświetlony alert.

3. W obszarze **Alert logic**, Zdefiniuj warunek i wartość progową alertu. Podgląd warunku zdefiniowany jest wyświetlana poniżej.

4. W obszarze **Evaluated na podstawie**, wybierz przedział czasu dla zapytania i jak często ma to zapytanie został uruchomiony. Na przykład, jeśli wybierzesz **w ciągu ostatnich 5 minut** dla **okres** i **co 1 minutę** dla **częstotliwość**, alert szuka numer elementy runbook zostały spełnione kryteria w ciągu ostatnich 5 minut. To zapytanie jest uruchamiany co minutę, oraz po kryteria alertu, zdefiniowane przez użytkownika nie jest już znajduje się w oknie 5-minutowych, alert rozwiązanie sam. Na koniec kliknij przycisk **Gotowe**.

   ![Wybierz zasób dla alertu](./media/automation-alert-activity-log/configure-signal-logic.png)

### <a name="define-alert-details"></a>Definiowanie szczegółów alertu

1. W obszarze **2. Zdefiniuj szczegóły alertu**, nadaj mu przyjazną nazwę i podaj opis. Ustaw **ważność** do dopasowania warunek alertu. Istnieje pięć ważności z zakresu od 0 do 5. Alerty są traktowane niezależnie od tego samego priorytetu, można dopasować ważności, aby dopasować logikę biznesową.

1. W dolnej części sekcji jest dostępny przycisk, który pozwala włączyć regułę po jego ukończeniu. Domyślnie reguły są włączone w momencie jego tworzenia. Jeśli wybierzesz nie, można utworzyć alertu i jest tworzony w **wyłączone** stanu. Z **reguły** strony w usłudze Azure Monitor, zaznacz go i kliknij **Włącz** umożliwiające alert, gdy wszystko będzie gotowe.

### <a name="define-the-action-to-take"></a>Zdefiniuj akcję do wykonania

1. W obszarze **3. Zdefiniuj grupę akcji** kliknij przycisk **+ Nowa grupa akcji**. Grupy akcji jest grupą akcji, które można użyć w więcej niż jeden alert. Te mogą obejmować, ale nie są ograniczone do wiadomości e-mail powiadomienia, elementy runbook, elementy webhook i wiele innych. Aby dowiedzieć się więcej o grupach akcji, zobacz [Tworzenie i zarządzanie grupami akcji](../azure-monitor/platform/action-groups.md)

1. W polu **Nazwa grupy akcji** wpisz przyjazną nazwę i nazwę krótką. Nazwa krótka jest używana zamiast pełnej nazwy grupy akcji podczas przesyłania powiadomień przy użyciu danej grupy.

1. W **akcje** sekcji **typ akcji**, wybierz opcję **poczty E-mail/SMS/wypychania/rejestr**.

1. Na stronie **E-mail/SMS/Push/Głos** nadaj nazwę. Zaznacz pole wyboru **E-mail** i wpisz prawidłowy adres e-mail, który ma być używany.

   ![Skonfiguruj grupę akcji poczty e-mail](./media/automation-alert-activity-log/add-action-group.png)

1. Kliknij przycisk **OK** na stronie **E-mail/SMS/Push/Głos**, aby ją zamknąć, a następnie kliknij przycisk **OK**, aby zamknąć stronę **Dodawanie grupy akcji**. Nazwa określona na tej stronie zostanie zapisany jako **nazwy akcji**.

1. Po zakończeniu kliknij przycisk **Zapisz**. Ta akcja tworzy regułę, która ostrzega, gdy element runbook została ukończona z określonym stanie.

> [!NOTE]
> Podczas dodawania adresu e-mail do grupy akcji, wiadomość e-mail z powiadomieniem są wysyłane, informujący, że adres został dodany do grupy akcji.

## <a name="notification"></a>Powiadomienie

Po spełnieniu kryteriów alertu grupy akcji uruchamia czynność zdefiniowana. W przykładzie w tym artykule zostanie wysłana wiadomość e-mail. Poniższy rysunek jest przykładem wiadomość e-mail, który pojawi się po wyzwoleniu alertu:

![Alert e-mail](./media/automation-alert-activity-log/alert-email.png)

Po Metryka nie jest już poza próg zdefiniowany, dezaktywacji alert i grupę akcji uruchamia czynność zdefiniowana. Wybranie typu akcji poczty e-mail zostanie wysłana e-mail rozdzielczości, informujący, że zostanie rozwiązany.

## <a name="next-steps"></a>Kolejne kroki

Kontynuuj z następującym artykułem, aby dowiedzieć się więcej o innych metodach, że można zintegrować alertings konta usługi Automation.

> [!div class="nextstepaction"]
> [Użyj alertów do wyzwalania elementu runbook usługi Azure Automation](automation-create-alert-triggered-runbook.md)
