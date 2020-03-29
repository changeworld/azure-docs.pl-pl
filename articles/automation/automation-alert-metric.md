---
title: Monitorowanie uruchomieniu witryny Azure Automation za pomocą alertów metryk
description: W tym artykule znajdziesz informacje o monitorowaniu ceł ceł usługi Azure Automation opartych na metrykach
services: automation
ms.date: 11/01/2018
ms.topic: article
ms.openlocfilehash: 9bd028157b33817898ef69f9e47cb8b5d9b8f381
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75367097"
---
# <a name="monitoring-runbooks-with-metric-alerts"></a>Monitorowanie y eks-ów z alertami metryk

W tym artykule dowiesz się, jak tworzyć alerty na podstawie stanu ukończenia śmięty.

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Zaloguj się do platformy Azure w witrynie https://portal.azure.com

## <a name="create-alert"></a>Tworzenie alertu

Alerty umożliwiają zdefiniowanie warunku do monitorowania i akcji do podjęcia po spełnieniu tego warunku.

W witrynie Azure portal przejdź do konta automatyzacji. W obszarze **Monitorowanie**wybierz pozycję **Alerty** i kliknij pozycję **+ Nowa reguła alertu**. Zakres obiektu docelowego jest już zdefiniowany na koncie automatyzacji.

### <a name="configure-alert-criteria"></a>Konfigurowanie kryteriów alertów

1. Kliknij **przycisk + Dodaj kryteria**. Wybierz **pozycję Metryki** dla **typu sygnału**i wybierz pozycję **Całkowita liczba zadań** z tabeli.

2. Strona **Konfigurowanie logiki sygnału** jest, gdzie można zdefiniować logikę, która wyzwala alert. W obszarze wykresu historycznego są prezentowane dwa wymiary, **Nazwa ekwifonu** i **stan**. Wymiary są różne właściwości metryki, które mogą służyć do filtrowania wyników. W obszarze **Nazwa uruchomieniu**wybierz projekt runbook, na który chcesz alertować, lub pozostaw puste pole do alertu we wszystkich ystawach runbook. W obszarze **Stan**wybierz stan z listy rozwijanej, dla której chcesz monitorować. Nazwa i wartości stanu uruchomieniu, które pojawiają się w rozwijaniu, są tylko dla zadań, które zostały uruchomione w ciągu ostatniego tygodnia.

   Jeśli chcesz ostrzec o stanie lub elementu runbook, który nie **\+** jest wyświetlany na rozwijaniu, kliknij obok wymiaru. Ta akcja otwiera okno dialogowe, które umożliwia wprowadzenie wartości niestandardowej, która nie została ostatnio wyemitowana dla tego wymiaru. Jeśli wprowadzisz wartość, która nie istnieje dla właściwości, alert nie zostanie wyzwolony.

   > [!NOTE]
   > Jeśli nie zastosujesz nazwy dla wymiaru **RunbookName,** jeśli istnieją jakieś elementy runbook, które spełniają kryteria stanu, które obejmują ukryte elementy runbook systemowe, otrzymasz alert.

3. W obszarze **Logika alertu**zdefiniuj warunek i próg alertu. Pod spodem zostanie wyświetlony podgląd zdefiniowanego stanu.

4. W obszarze **Oceniane na podstawie**wybierz czas dla kwerendy i jak często ma być wyszukiwana ta kwerenda. Jeśli na przykład wybierzesz **opcję W ciągu ostatnich 5 minut** dla **okresu** i **co 1 minuta** dla **częstotliwości,** alert wyszukuje liczbę umajnych, które spełniają twoje kryteria w ciągu ostatnich 5 minut. Ta kwerenda jest uruchamiana co minutę, a gdy zdefiniowane kryteria alertu nie będą już dostępne w 5-minutowym oknie, alert zostanie rozpoznany. Na koniec kliknij przycisk **Gotowe**.

   ![Wybieranie zasobu dla alertu](./media/automation-alert-activity-log/configure-signal-logic.png)

### <a name="define-alert-details"></a>Definiowanie szczegółów alertu

1. Poniżej **2. Zdefiniuj szczegóły alertu,** nadaj alertowi przyjazną nazwę i opis. Ustaw **ważność,** aby dopasować warunek alertu. Istnieje pięć nasilenia od 0 do 5. Alerty są traktowane tak samo niezależnie od ważności, można dopasować ważność, aby dopasować logikę biznesową.

1. W dolnej części sekcji znajduje się przycisk, który umożliwia włączenie reguły po zakończeniu. Domyślnie reguły są włączone podczas tworzenia. Jeśli wybierzesz opcję Nie, możesz utworzyć alert i zostanie on utworzony w stanie **Wyłączone.** Na stronie **Reguły** w usłudze Azure Monitor możesz go wybrać i kliknąć przycisk **Włącz,** aby włączyć alert, gdy wszystko będzie gotowe.

### <a name="define-the-action-to-take"></a>Zdefiniuj akcję, która ma być podejmowana

1. Poniżej **3. Definiuj grupę akcji**, kliknij **+ Nowa grupa akcji**. Grupa akcji to grupa akcji, których można użyć w więcej niż jednym alertie. Mogą one obejmować między innymi powiadomienia e-mail, programy runbook, elementów webhook i wiele innych. Aby dowiedzieć się więcej o grupach akcji, zobacz [Tworzenie i zarządzanie grupami akcji](../azure-monitor/platform/action-groups.md)

1. W polu **Nazwa grupy akcji** wpisz przyjazną nazwę i nazwę krótką. Nazwa krótka jest używana zamiast pełnej nazwy grupy akcji podczas przesyłania powiadomień przy użyciu danej grupy.

1. W sekcji **Akcje** w obszarze **TYP AKCJI**wybierz **pozycję E-mail/SMS/Push/Voice**.

1. Na stronie **E-mail/SMS/Push/Głos** nadaj nazwę. Zaznacz pole wyboru **E-mail** i wpisz prawidłowy adres e-mail, który ma być używany.

   ![Konfigurowanie grupy akcji dla poczty e-mail](./media/automation-alert-activity-log/add-action-group.png)

1. Kliknij przycisk **OK** na stronie **E-mail/SMS/Push/Głos**, aby ją zamknąć, a następnie kliknij przycisk **OK**, aby zamknąć stronę **Dodawanie grupy akcji**. Nazwa określona na tej stronie jest zapisywana jako **NAZWA AKCJI**.

1. Po zakończeniu kliknij przycisk **Zapisz**. Ta akcja tworzy regułę, która ostrzega po zakończeniu uruchomieniu ok z określonym stanem.

> [!NOTE]
> Podczas dodawania adresu e-mail do grupy akcji jest wysyłana wiadomość e-mail z powiadomieniem z informacją, że adres został dodany do grupy akcji.

## <a name="notification"></a>Powiadomienie

Po spełnieniu kryteriów alertu grupa akcji uruchamia zdefiniowaną akcję. W tym artykule jest wysyłany e-mail. Poniższy obraz jest przykładem wiadomości e-mail otrzymanej po wyzwoleniu alertu:

![Alert e-mail](./media/automation-alert-activity-log/alert-email.png)

Gdy metryka nie jest już poza zdefiniowanym progiem, alert jest dezaktywowany, a grupa akcji uruchamia zdefiniowaną akcję. Jeśli wybrano typ akcji e-mail, zostanie wysłana wiadomość e-mail z informacją, że została rozwiązana.

## <a name="next-steps"></a>Następne kroki

Przejdź do poniższego artykułu, aby dowiedzieć się więcej o innych sposobach integracji alertów z kontem automatyzacji.

> [!div class="nextstepaction"]
> [Wyzwalanie uruchomieniu księgi runbook usługi Azure Automation za pomocą alertu](automation-create-alert-triggered-runbook.md)
