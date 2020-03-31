---
title: Rozwiązywanie problemów z alertami i powiadomieniami usługi Azure Monitor
description: Typowe problemy z alertami usługi Azure Monitor i możliwymi rozwiązaniami.
author: ofirmanor
ms.author: ofmanor
ms.topic: reference
ms.date: 03/16/2020
ms.subservice: alerts
ms.openlocfilehash: beb47f961c6f24453bd49aa5807c9d801fc199a3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80132325"
---
# <a name="troubleshooting-problems-in-azure-monitor-alerts"></a>Rozwiązywanie problemów w alertach usługi Azure Monitor 

W tym artykule omówiono typowe problemy w alertach usługi Azure Monitor.

Alerty usługi Azure Monitor proaktywnie powiadamiają o znalezieniu ważnych warunków w danych monitorowania. Umożliwiają one identyfikowanie i rozwiązywanie problemów, zanim użytkownicy systemu zauważą je. Aby uzyskać więcej informacji na temat [alertów, zobacz Omówienie alertów na platformie Microsoft Azure](alerts-overview.md).

## <a name="action-or-notification-on-my-alert-did-not-work-as-expected"></a>Działanie lub powiadomienie w moim wpisie nie działało zgodnie z oczekiwaniami

Jeśli widzisz zwolniony alert w witrynie Azure portal, ale masz problem z niektórymi jego akcjami lub powiadomieniami, zobacz poniższe sekcje.

## <a name="did-not-receive-expected-email"></a>Nie otrzymałem oczekiwanej wiadomości e-mail

Jeśli alert został wyświetlony w witrynie Azure portal, ale nie otrzymałeś skonfigurowanego adresu e-mail na ten temat, wykonaj następujące kroki: 

1. **Czy wiadomość e-mail została pominięta przez [regułę akcji?](alerts-action-rules.md)** 

    Sprawdź, klikając na odpalony alert w portalu i spójrz na kartę historii dla [pominiętych grup akcji:](action-groups.md) 

    ![Historia wygaszania reguł akcji alertów](media/alerts-troubleshoot/history-action-rule.png)

1. **Czy typ akcji "Rola usługi Azure Resource Manager e-mail"?**

    Ta akcja dotyczy tylko przypisań ról usługi Azure Resource Manager, które znajdują się w zakresie subskrypcji, i typu *Użytkownik*.  Upewnij się, że przypisano rolę na poziomie subskrypcji, a nie na poziomie zasobów lub grupy zasobów.

1. **Czy twój serwer poczty e-mail i skrzynka pocztowa akceptują zewnętrzne wiadomości e-mail?**

    Sprawdź, czy wiadomości e-mail z tych trzech adresów nie są blokowane:
      - azure-noreply@microsoft.com  
      - azureemail-noreply@microsoft.com
      - alerts-noreply@mail.windowsazure.com

    Często wewnętrzne listy korespondujące lub listy dystrybucyjne blokują wiadomości e-mail z zewnętrznych adresów e-mail. Musisz wpisać powyższe adresy e-mail na białą listę.  
    Aby przetestować, dodaj zwykły służbowy adres e-mail (a nie listę adresową) do grupy akcji i sprawdź, czy alerty docierają do tej wiadomości e-mail. 

1. **Czy wiadomość e-mail była przetwarzana przez reguły skrzynki odbiorczej lub filtr spamu?** 

    Sprawdź, czy nie ma reguł skrzynki odbiorczej, które usuwają te wiadomości e-mail lub przenoszą je do folderu bocznego. Na przykład reguły skrzynki odbiorczej mogą przechwytywane określonych nadawców lub określonych słów w temacie.

    Sprawdź również:
    
      - ustawienia spamu klienta poczty e-mail (np.
      - limity nadawców / ustawienia spamu / kwarantanny serwera poczty e-mail (np.
      - ustawienia urządzenia zabezpieczającego pocztę e-mail, jeśli istnieją (jak Barracuda, Cisco). 

1. **Czy przypadkowo wypisałeś się z grupy akcji?** 

    Wiadomości e-mail z alertami zawierają łącze umożliwiające anulowanie subskrypcji grupy akcji. Aby sprawdzić, czy przypadkowo anulowano subskrypcję tej grupy akcji:

    1. Otwórz grupę akcji w portalu i sprawdź kolumnę Stan:

    ![kolumna stan grupy akcji](media/alerts-troubleshoot/action-group-status.png)

    2. Wyszukaj w wiadomości e-mail potwierdzenie anulowania subskrypcji:

    ![anulowanie subskrypcji z grupy akcji alertów](media/alerts-troubleshoot/unsubscribe-action-group.png)

    Aby ponownie zasubskrybować — użyj linku w otrzymanej wiadomości e-mail z potwierdzeniem anulowania subskrypcji lub usuń adres e-mail z grupy akcji, a następnie dodaj go ponownie. 
 
1. **Czy zostałeś oceniony jako ograniczony ze względu na wiele wiadomości e-mail, które będą trafiać na jeden adres e-mail?** 

    E-mail jest [ograniczona](alerts-rate-limiting.md) do nie więcej niż 100 e-maili co godzinę na każdy adres e-mail. Jeśli przekroczysz ten próg, dodatkowe powiadomienia e-mail zostaną usunięte.  Sprawdź, czy otrzymałeś wiadomość informującą, że Twój adres e-mail został tymczasowo ograniczony: 
 
   ![Ograniczona stawka poczty e-mail](media/alerts-troubleshoot/email-paused.png)

   Jeśli chcesz otrzymywać duże ilości powiadomień bez ograniczania szybkości, należy rozważyć użycie innej akcji, takiej jak element webhook, aplikacja logiki, funkcja platformy Azure lub elementy runbook automatyzacji, z których żadna nie jest ograniczona. 

## <a name="did-not-receive-expected-sms-voice-call-or-push-notification"></a>Nie otrzymałem oczekiwanych wiadomości SMS, połączeń głosowych ani powiadomień push

Jeśli alert został wyświetlony w portalu, ale nie otrzymałeś smsa, połączenia głosowego ani powiadomienia wypychanego, które zostało skonfigurowane na ten temat, wykonaj następujące czynności: 

1. **Czy akcja została pominięta przez [regułę akcji?](alerts-action-rules.md)** 

    Sprawdź, klikając na odpalony alert w portalu i spójrz na kartę historii dla [pominiętych grup akcji:](action-groups.md) 

    ![Historia wygaszania reguł akcji alertów](media/alerts-troubleshoot/history-action-rule.png)

   Jeśli to było niezamierzone, można zmodyfikować, wyłączyć lub usunąć regułę akcji.
 
1. **SMS / głos: Czy Twój numer telefonu jest poprawny?**

   Sprawdź akcję SMS pod kątem literówek w kodzie kraju lub numerze telefonu. 
 
1. **SMS / głos: czy stawka była ograniczona?** 

    Połączenia SMS i głosowe są ograniczone do nie więcej niż jednego powiadomienia co pięć minut na numer telefonu. Jeśli przekroczysz ten próg, powiadomienia zostaną usunięte. 

      - Połączenie głosowe — sprawdź historię połączeń i sprawdź, czy w ciągu ostatnich pięciu minut było inne połączenie niż platforma Azure. 
      - SMS - sprawdź historię WIADOMOŚCI SMS, aby uzyskać wiadomość wskazującą, że twój numer telefonu został ograniczony. 

    Jeśli chcesz otrzymywać duże ilości powiadomień bez ograniczania szybkości, należy rozważyć użycie innej akcji, takiej jak element webhook, aplikacja logiki, funkcja platformy Azure lub elementy runbook automatyzacji, z których żadna nie jest ograniczona. 
 
1. **SMS: Czy przypadkowo wypisałeś się z grupy akcji?**

    Otwórz historię wiadomości SMS i sprawdź, czy zrezygnowano z dostarczania wiadomości SMS z tej konkretnej grupy akcji (za pomocą odpowiedzi action_group_short_name wyłącz) lub ze wszystkich grup akcji (za pomocą odpowiedzi STOP). Aby ponownie zasubskrybować subskrypcję, wyślij odpowiednie polecenie SMS (WŁĄCZ action_group_short_name lub START) lub usuń akcję SMS z grupy akcji, a następnie dodaj ją ponownie.  Aby uzyskać więcej informacji, zobacz [zachowanie alertów programu SMS w grupach akcji](alerts-sms-behavior.md).

1. **Czy przypadkowo zablokowałeś powiadomienia w telefonie?**

   Większość telefonów komórkowych umożliwia blokowanie połączeń lub wiadomości SMS z określonych numerów telefonów lub krótkich kodów lub blokowanie powiadomień wypychanych z określonych aplikacji (takich jak aplikacja mobilna platformy Azure). Aby sprawdzić, czy przypadkowo zablokowałeś powiadomienia w telefonie, przeszukaj dokumentację dotyczącą systemu operacyjnego i modelu telefonu lub przetestuj przy tym przy innym numerze telefonu i numerze telefonu. 

## <a name="expected-another-type-of-action-to-trigger-but-it-did-not"></a>Oczekiwano innego rodzaju akcji do uruchomienia, ale nie 

Jeśli w portalu zostanie wyświetlony uruchomiony alert, ale jego skonfigurowana akcja nie wyzwoliła się, wykonaj następujące kroki: 

1. **Czy akcja została pominięta przez regułę akcji?** 

    Sprawdź, klikając na odpalony alert w portalu i spójrz na kartę historii dla [pominiętych grup akcji:](action-groups.md) 

    ![Historia wygaszania reguł akcji alertów](media/alerts-troubleshoot/history-action-rule.png)
 
    Jeśli to było niezamierzone, można zmodyfikować, wyłączyć lub usunąć regułę akcji. 

1. **Czy element webhook nie wyzwolił?**

    1. **Czy źródłowe adresy IP zostały zablokowane?**
    
       Umieszczaj na białej liście [adresy IP,](action-groups.md#action-specific-information) z których wywoływany jest element webhook.

    1. **Czy punkt końcowy elementu webhook działa poprawnie?**

       Sprawdź, czy skonfigurowany punkt końcowy elementu webhook jest poprawny, a punkt końcowy działa poprawnie. Sprawdź dzienniki webhook lub instrument jego kodu, dzięki czemu można zbadać (na przykład, rejestrować ładunek przychodzący). 

    1. **Czy dzwonisz do Slack lub Microsoft Teams?**  
    Każdy z tych punktów końcowych oczekuje określonego formatu JSON. Postępuj zgodnie z [tymi instrukcjami,](action-groups-logic-app.md) aby zamiast tego skonfigurować akcję aplikacji logiki.

    1. **Czy twój element webhook przestał odpowiadać lub zwracał błędy?** 

        Nasz limit czasu dla odpowiedzi elementu webhook wynosi 10 sekund. Wywołanie elementu webhook zostanie ponowione maksymalnie dwa dodatkowe razy, gdy zwracane są następujące kody stanu HTTP: 408, 429, 503, 504 lub gdy punkt końcowy HTTP nie odpowiada. Pierwsze ponowienie próby odbywa się po 10 sekundach. Druga i ostatnia ponowna próby nastąpi po 100 sekundach. Jeśli drugie ponowienie zakończy się niepowodzeniem, punkt końcowy nie zostanie ponownie wywołany przez 30 minut dla żadnej grupy akcji.

## <a name="action-or-notification-happened-more-than-once"></a>Działanie lub powiadomienie miało miejsce więcej niż jeden raz 

Jeśli otrzymasz powiadomienie o alertie (takim jak wiadomość e-mail lub SMS) więcej niż jeden raz lub akcja alertu (na przykład element webhook lub funkcja platformy Azure) została wyzwolona wiele razy, wykonaj następujące kroki: 

1. **Czy to naprawdę ten sam alert?** 

    W niektórych przypadkach wiele podobnych alertów są uruchamiane w tym samym czasie. Tak, może się wydawać, że ten sam alert wyzwolił swoje działania wiele razy. Na przykład reguła alertu dziennika aktywności może być skonfigurowana do wypalania zarówno po rozpoczęciu zdarzenia, jak i po jego zakończeniu (powiodło się lub nie powiodło się), nie filtrując w polu stanu zdarzenia. 

    Aby sprawdzić, czy te akcje lub powiadomienia pochodzą z różnych alertów, sprawdź szczegóły alertu, takie jak jego sygnatura czasowa i identyfikator alertu lub jego identyfikator korelacji. Alternatywnie sprawdź listę zwolnionych alertów w portalu. W takim przypadku należy dostosować logikę reguły alertu lub w inny sposób skonfigurować źródło alertu. 

1. **Czy akcja powtarza się w wielu grupach akcji?** 

    Po uruchomieniu alertu każda z jego grup akcji jest przetwarzana niezależnie. Jeśli więc akcja (na przykład adres e-mail) pojawi się w wielu wyzwolonych grupach akcji, zostanie wywołana raz na grupę akcji. 

    Aby sprawdzić, które grupy akcji zostały wyzwolone, sprawdź kartę historii alertów. Zostaną wyświetlona zarówno grupy akcji zdefiniowane w regule alertów, jak i grupy akcji dodane do alertu przez reguły akcji: 

    ![Akcja powtarzana w wielu grupach akcji](media/alerts-troubleshoot/action-repeated-multi-action-groups.png)

## <a name="action-or-notification-has-an-unexpected-content"></a>Akcja lub powiadomienie ma nieoczekiwaną zawartość

Jeśli alert został odebrany, ale uważasz, że brakuje niektórych jego pól lub jest on nieprawidłowy, wykonaj następujące czynności: 

1. **Czy wybrałeś odpowiedni format akcji?** 

    Każdy typ akcji (e-mail, element webhook itp.) ma dwa formaty – domyślny, starszy format i [nowszy wspólny format schematu.](alerts-common-schema.md) Podczas tworzenia grupy akcji należy określić format, który ma być dla każdego działania — różne akcje w grupach akcji mogą mieć różne formaty. 

    Na przykład dla akcji webhook: 

    ![Opcja schematu akcji elementu webhook](media/alerts-troubleshoot/webhook.png)

    Sprawdź, czy format określony na poziomie akcji jest to, czego oczekujesz. Na przykład może być opracowany kod, który odpowiada na alerty (webhook, funkcja, aplikacja logiki, itp.), oczekując jednego formatu, ale później w akcji ty lub inna osoba określiła inny format.  

    Sprawdź również format ładunku (JSON) [dla alertów dziennika aktywności](activity-log-alerts-webhook.md), dla [alertów wyszukiwania dziennika](alerts-log-webhook.md) (zarówno Application Insights i analizy dziennika), dla [alertów metryk,](alerts-metric-near-real-time.md#payload-schema)dla [wspólnego schematu alertów](alerts-common-schema-definitions.md)i przestarzałych [alertów metryki klasycznej](alerts-webhooks.md).

 
1. **Alerty dziennika aktywności: Czy informacje są dostępne w dzienniku aktywności?** 

    [Alerty dziennika aktywności](activity-log-alerts.md) to alerty oparte na zdarzeniach zapisanych w dzienniku aktywności platformy Azure, takich jak zdarzenia dotyczące tworzenia, aktualizowania lub usuwania zasobów platformy Azure, zdarzeń kondycji i kondycji zasobów usługi lub wyników z usługi Azure Advisor i zasad platformy Azure. Jeśli został odebrany alert na podstawie dziennika aktywności, ale niektóre pola, które są potrzebne brakuje lub niepoprawne, najpierw sprawdź zdarzenia w dzienniku aktywności. Jeśli zasób platformy Azure nie napisał pól, których szukasz w jego zdarzeniu dziennika aktywności, te pola nie zostaną uwzględnione w odpowiednim alertie. 

## <a name="action-rule-is-not-working-as-expected"></a>Reguła akcji nie działa zgodnie z oczekiwaniami 

Jeśli w portalu zostanie wyświetlony alert o zwolnieniu, ale powiązana reguła akcji nie działa zgodnie z oczekiwaniami, wykonaj następujące kroki: 

1. **Czy reguła akcji jest włączona?** 

    Sprawdź kolumnę stanu reguły akcji, aby sprawdzić, czy rola akcji pokrewnych jest włączona. 

    ![Graficzny](media/alerts-troubleshoot/action-rule-status.png) 

    Jeśli nie jest włączona, można włączyć regułę akcji, zaznaczając ją i klikając przycisk Włącz. 

1. **Czy jest to alert kondycji usługi?** 

    Alerty kondycji usługi (usługa monitorowania = "Kondycja usługi") nie są objęte regułami akcji. 

1. **Czy reguła działania działała w twoim wpisie?** 

    Sprawdź, czy reguła akcji przetworzyła alert, klikając wystrzelony alert w portalu, i spójrz na kartę historii.

    Oto przykład reguły akcji pomijającej wszystkie grupy akcji: 
 
     ![Historia wygaszania reguł akcji alertów](media/alerts-troubleshoot/history-action-rule.png)

    Oto przykład reguły akcji dodającej inną grupę akcji:

    ![Akcja powtarzana w wielu grupach akcji](media/alerts-troubleshoot/action-repeated-multi-action-groups.png)
 

1. **Czy zakres reguły akcji i filtr są zgodne z odpalony alert?** 

    Jeśli uważasz, że reguła akcji powinna była zostać zwolniona, ale nie, lub że nie powinna była zostać zwolniona, ale tak, dokładnie zbadaj zakres reguły akcji i warunki filtrowania w porównaniu z właściwościami zwolnionego alertu. 


## <a name="how-to-find-the-alert-id-of-a-fired-alert"></a>Jak znaleźć identyfikator alertu odpalony alert

Podczas otwierania sprawy o określonym zwolnionym alertie (na przykład - jeśli nie otrzymałeś powiadomienia e-mail), musisz podać identyfikator alertu. 

Aby go zlokalizować, wykonaj następujące kroki:

1. W witrynie Azure portal przejdź do listy zwolnionych alertów i znajdź ten konkretny alert. Możesz użyć filtrów, aby pomóc ci go zlokalizować. 

1. Kliknij alert, aby otworzyć szczegóły alertu. 

1. Przewiń w dół w polach alertów pierwszej karty (karta podsumowanie), aż ją zlokalizujesz, i skopiuj. To pole zawiera również przycisk pomocnika "Kopiuj do schowka", którego możesz użyć.  

    ![znajdowanie identyfikatora alertu](media/alerts-troubleshoot/get-alert-id.png)

## <a name="problem-creating-updating-or-deleting-action-rules-in-the-azure-portal"></a>Problem z tworzeniem, aktualizowaniem lub usuwaniem reguł akcji w witrynie Azure portal

Jeśli podczas próby utworzenia, zaktualizowania lub usunięcia [reguły akcji](alerts-action-rules.md)wystąpił błąd, wykonaj następujące czynności: 

1. **Czy wystąpił błąd uprawnień?**  

    Należy mieć [wbudowaną rolę monitorowania współautora](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-contributor)lub określone uprawnienia związane z regułami akcji i alertami.

1. **Czy zweryfikowałeś parametry reguły akcji?**  

    Sprawdź [dokumentację reguły akcji](alerts-action-rules.md)lub [polecenie PowerShell rule Set-AzActionRule.](https://docs.microsoft.com/powershell/module/az.alertsmanagement/Set-AzActionRule?view=azps-3.5.0) 


## <a name="next-steps"></a>Następne kroki
- Jeśli używasz alertu dziennika, zobacz także [Rozwiązywanie problemów z alertami dziennika](alert-log-troubleshoot.md).
- Wróć do [witryny Azure portal,](https://portal.azure.com) aby sprawdzić, czy problem został rozwiązany za pomocą powyższych wskazówek 
