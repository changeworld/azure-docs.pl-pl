---
title: Usługa Azure Event Hubs — często zadawane pytania | Dokumentacja firmy Microsoft
description: Usługa Azure Event Hubs — często zadawane pytania (FAQ)
services: event-hubs
documentationcenter: na
author: sethmanheim
manager: timlt
editor: ''
ms.assetid: bfa10984-eb22-4671-861a-f377a90d9372
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/30/2018
ms.author: sethm
ms.openlocfilehash: 8a30b8cb92b98606ca3d859ada329b8301806b10
ms.sourcegitcommit: c52123364e2ba086722bc860f2972642115316ef
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/11/2018
---
# <a name="event-hubs-frequently-asked-questions"></a>Usługa Event Hubs — często zadawane pytania

## <a name="general"></a>Ogólne

### <a name="what-is-the-difference-between-event-hubs-basic-and-standard-tiers"></a>Jaka jest różnica między warstwy standardowa i podstawowa centra zdarzeń?

Warstwy standardowa usługi Azure Event hubs udostępnia funkcje poza co to jest dostępne w warstwie podstawowej. Standard dołączono następujące funkcje:
* Dłużej przechowywania zdarzeń
* Dodatkowe połączeń obsługiwanych przez brokera dodatkowy nadwyżkowe dla więcej niż numer zawarty
* Więcej niż jednej grupy odbiorców
* [Przechwytywania](https://docs.microsoft.com/azure/event-hubs/event-hubs-capture-overview)

Aby uzyskać więcej informacji na temat cen warstwy dedykowanego centra zdarzeń w tym temacie [szczegóły cennika usługi Event Hubs](https://azure.microsoft.com/pricing/details/event-hubs/).

### <a name="what-are-event-hubs-throughput-units"></a>Co to są jednostek przepływności usługi Event Hubs?

Należy wybrać jawnie jednostek przepływności usługi Event Hubs, za pomocą portalu Azure lub szablony Menedżera zasobów centrów zdarzeń. Jednostki przepływności dotyczą wszystkich centra zdarzeń w przestrzeni nazw usługi Event Hubs i każdej jednostki przepływności uprawnia przestrzeni nazw do następujące możliwości:

* Się do 1 MB na sekundę zdarzenia transfer danych przychodzących (zdarzenia wysyłane do Centrum zdarzeń), ale nie więcej niż 1000 wejściowych zdarzenia, operacji zarządzania lub formantu wywołania interfejsu API na sekundę.
* Do 2 MB na sekundę wyjście zdarzenia (zdarzenia używane z Centrum zdarzeń).
* Do 84 GB magazynu zdarzeń (wystarcza na domyślny 24-godzinny okres przechowywania).

Jednostki przepływności centra zdarzeń są rozliczane godzinowo, oparte na maksymalną liczbę jednostek wybranych w ciągu danego godziny. Można automatycznie [zwiększyć liczbę jednostek przepływności](event-hubs-auto-inflate.md) miarę wzrostu użycie.

### <a name="how-are-event-hubs-throughput-unit-limits-enforced"></a>Jak wymusić ograniczenia jednostki przepływności usługi Event Hubs
Jeśli przepustowość łączny ruch przychodzący lub występowania zdarzeń wejściowych całkowita między wszystkie centra zdarzeń w przestrzeni nazw przekracza dodatki jednostki łącznej przepływności, nadawców są ograniczane i wskazujący, że przekroczono przydział transfer danych przychodzących z błędami.

Jeśli przepustowość wyjście całkowita lub szybkość wyjście całkowita liczba zdarzeń na wszystkie centra zdarzeń w przestrzeni nazw przekracza dodatki jednostki łącznej przepływności, odbiorcy są ograniczane i odbieranie błędów wskazujących, że przekroczono przydział wyjście. Przydziały przychodzące i wychodzące są wymuszane oddzielnie, umożliwiając nadawcy może spowodować użycie zdarzeń spowalniać ani odbiornika uniemożliwia zdarzenia są wysyłane do Centrum zdarzeń.

### <a name="is-there-a-limit-on-the-number-of-throughput-units-that-can-be-selected"></a>Czy jest jakieś ograniczenie liczby jednostek przepływności, które można wybrać?
Brak domyślnego przydziału 20 jednostek przepływności na przestrzeni nazw. Można zażądać większego limitu przydziału jednostek przepływności, zgłaszając bilet pomocy technicznej. Przekroczenie limitu jednostki przepływności 20 pakiety są dostępne w 20 i 100 jednostek przepływności. Należy pamiętać, że korzystanie z więcej niż 20 jednostek przepływności usuwa możliwość zmiany liczby jednostek przepływności bez zgłoszenia biletu pomocy technicznej.

Przy użyciu [zwiększyć automatycznie](event-hubs-auto-inflate.md) funkcji, można automatycznie zwiększyć liczbę jednostek przepływności jak zwiększa użycie.

### <a name="can-i-use-a-single-amqp-connection-to-send-and-receive-from-multiple-event-hubs"></a>Wysyłanie i odbieranie z wielu centrów zdarzeń można używać pojedynczego połączenia protokołu AMQP?
Tak, jak długo są wszystkie usługi event hubs w tej samej przestrzeni nazw.

### <a name="what-is-the-maximum-retention-period-for-events"></a>Co to jest maksymalny okres przechowywania dla zdarzeń?
Warstwy standardowa centra zdarzeń obsługuje obecnie okres maksymalny czas przechowywania danych w ciągu 7 dni. Centra zdarzeń nie są przeznaczone do stałego przechowywania danych. Okresy przechowywania dłuższego niż 24 godziny są przeznaczone dla scenariuszy, w których jest wygodne powtarzania strumienia zdarzeń w tym samym systemy; na przykład uczenia lub sprawdź, czy nowy model uczenia maszynowego na istniejących danych. Jeśli konieczne komunikatu przechowywania poza 7 dni, włączanie [przechwytywania centra zdarzeń](https://docs.microsoft.com/azure/event-hubs/event-hubs-capture-overview) na zdarzenie Centrum pobiera dane z Centrum zdarzeń do konta magazynu lub konta usługi Azure Data Lake usługi wybrane. Włączanie przechwytywania wiąże opłat oparte na sieci zakupionych jednostek przepływności.

### <a name="where-is-azure-event-hubs-available"></a>Gdzie jest usługa Azure Event Hubs?
Usługa Azure Event Hubs jest dostępna we wszystkich obsługiwanych regionach platformy Azure. Lista, odwiedź stronę [regiony platformy Azure](https://azure.microsoft.com/regions/) strony.  

## <a name="best-practices"></a>Najlepsze praktyki

### <a name="how-many-partitions-do-i-need"></a>Jak wiele partycji potrzebuję?

Należy pamiętać, że liczba partycji w Centrum zdarzeń nie można zmodyfikować po zakończeniu instalacji. Z tym pamiętać należy wziąć pod uwagę liczbę partycji należy przed rozpoczęciem pracy. 

Usługa Event Hubs umożliwia czytnik jedną partycję na grupę odbiorców. W większości przypadków użycia wystarczające jest ustawieniem domyślnym cztery partycje. Jeśli szukasz skalowanie przetwarzania zdarzeń, można rozważyć dodanie dodatkowe partycje. Nie ma żadnego limitu określonego przepływności na partycji, jednak łącznej przepływności w przestrzeni nazw jest ograniczona przez liczbę jednostek przepływności. Jak zwiększyć liczbę jednostek przepływności w przestrzeni nazw, można dodatkowe partycje umożliwiają współbieżnych czytników do osiągnięcia własnych maksymalną przepustowość.

Jednak jeśli modelu, w którym aplikacja ma koligacji do określonej partycji, zwiększenie liczby partycji nie można korzyści dla użytkownika. Aby uzyskać więcej informacji na ten temat, zobacz [dostępności i spójności](event-hubs-availability-and-consistency.md).

## <a name="pricing"></a>Cennik

### <a name="where-can-i-find-more-pricing-information"></a>Gdzie można znaleźć informacje o cenach więcej?
Aby uzyskać pełne informacje na temat cen usługi Event Hubs, zobacz [szczegóły cennika usługi Event Hubs](https://azure.microsoft.com/pricing/details/event-hubs/).

### <a name="is-there-a-charge-for-retaining-event-hubs-events-for-more-than-24-hours"></a>Czy istnieje opłata przechowywania zdarzeń usługi Event Hubs przez więcej niż 24 godziny?
Warstwy standardowa centra zdarzeń Zezwalaj przechowywania wiadomości okresów dłużej niż 24 godziny, maksymalnie 7 dni. Jeśli rozmiar całkowitej liczby zdarzeń przechowywanych przekracza dozwolony magazynu dla liczby jednostek przepływności wybranego (84 GB na jednostkę przepływności), rozmiar przekracza dozwolony jest pobierana z szybkością opublikowanych magazynu obiektów Blob platformy Azure. Dodatek magazynu w każdej jednostki przepływności obejmuje kosztów magazynowania wszystkich okresów przechowywania 24 godzin (domyślnie), nawet jeśli jednostka przepływności jest wykorzystane do dodatku maksymalną transfer danych przychodzących.

### <a name="how-is-the-event-hubs-storage-size-calculated-and-charged"></a>Jak jest rozmiar magazynu usługi Event Hubs obliczone i pobierane?
Całkowity rozmiar wszystkich zdarzeń przechowywanych, w tym wszelkie koszty wewnętrzny dla nagłówków zdarzeń lub struktur na dysku magazynu w wszystkie usługi event hubs, jest mierzony w ciągu dnia. Na koniec dnia wyliczana jest szczytowa wielkość magazynu. Dzienny limit magazynu jest wyliczany na podstawie minimalnej liczby jednostek przepływności, które zostały wybrane w ciągu dnia (każda jednostka przepływności ma limit 84 GB). Jeśli łączny rozmiar przekracza obliczeniowej codzienne dodatek magazynu, nadmiarowe magazyn jest rozliczany przy użyciu stawek obowiązujących dla magazynu obiektów Blob platformy Azure (w **magazyn lokalnie nadmiarowy** szybkość).

### <a name="how-are-event-hubs-ingress-events-calculated"></a>Jak są obliczane zdarzeń wejściowych centra zdarzeń
Każdego zdarzenia wysyłane do Centrum zdarzeń jest traktowana jako rozliczeniowy wiadomości. *Zdarzeń wejściowych* jest zdefiniowany jako jednostka danych, która jest mniejsza niż 64 KB. Wszystkie zdarzenia, która jest mniejsza niż lub równe 64 KB rozmiaru jest uważana za jedno zdarzenie rozliczeniowy. Jeśli zdarzenie jest większa niż 64 KB, liczbę zdarzeń rozliczeniowy jest obliczane na podstawie rozmiar zdarzenia wielokrotności 64 KB. Na przykład zdarzenia 8 KB wysyłane do Centrum zdarzeń jest on rozliczany jako jedno zdarzenie, ale 96 KB komunikat wysyłany do Centrum zdarzeń jest on rozliczany jako dwa zdarzenia.

Zdarzenia używane z Centrum zdarzeń, jak również jako operacji zarządzania i kontroli wywołań, taką jak punkty kontrolne nie są liczone jako zdarzeń wejściowych rozliczeniowy, ale naliczania do dodatku jednostkę przepływności.

### <a name="do-brokered-connection-charges-apply-to-event-hubs"></a>Opłaty za połączenia obsługiwanych przez brokera są stosowane do usługi Event Hubs?
Opłaty za połączenia mają zastosowanie tylko wtedy, jeśli jest używany protokół AMQP. Nie ma opłat za połączenia związane z przesyłaniem zdarzeń przez protokół HTTP niezależnie od liczby systemów lub urządzeń wysyłających. Jeśli planujesz używać protokołu AMQP (na przykład osiągnąć większą wydajność przesyłania strumieniowego zdarzeń lub Włącz komunikację dwukierunkową w poleceniu IoT i kontrolować scenariusze), zobacz temat [uzyskać informacje o cenach usługi Event Hubs](https://azure.microsoft.com/pricing/details/event-hubs/) strony, aby uzyskać szczegółowe informacje, o ile połączeń są uwzględnione w poszczególnych warstwach usług.

### <a name="how-is-event-hubs-capture-billed"></a>W jaki sposób są naliczane opłaty za funkcję przechwytywanie usługi Event Hubs?
Przechwytywanie jest włączona, gdy wszystkie Centrum zdarzeń w przestrzeni nazw włączono opcję przechwytywania. Przechwyć centra zdarzeń są rozliczane godzinowo na jednostkę przepływności zakupionych. Liczba jednostek przepływności jest zwiększyć lub zmniejszyć, rozliczeń przechwytywania centra zdarzeń odzwierciedla te zmiany w całej godz. Aby uzyskać więcej informacji dotyczących rozliczeń przechwytywania centra zdarzeń, zobacz [uzyskać informacje o cenach usługi Event Hubs](https://azure.microsoft.com/pricing/details/event-hubs/).

### <a name="will-i-be-billed-for-the-storage-account-i-select-for-event-hubs-capture"></a>I naliczania dla konta magazynu, wybranym Capture centra zdarzeń?
Przechwytywanie używa konta magazynu, podane przez użytkownika po włączeniu w Centrum zdarzeń. Jak jest to Twoje konto magazynu, wszystkie zmiany dla tej konfiguracji są rozliczane do subskrypcji platformy Azure.

## <a name="quotas"></a>Przydziały

### <a name="are-there-any-quotas-associated-with-event-hubs"></a>Czy istnieją wszystkie przydziały skojarzony z usługą Event Hubs?
Aby uzyskać listę wszystkich przydziałów centra zdarzeń, zobacz [przydziały](event-hubs-quotas.md).

## <a name="troubleshooting"></a>Rozwiązywanie problemów

### <a name="what-are-some-of-the-exceptions-generated-by-event-hubs-and-their-suggested-actions"></a>Co to są wyjątki generowane przez centra zdarzeń i ich sugerowane akcje?
Listę możliwych wyjątków usługi Event Hubs, zobacz [omówienie wyjątki](event-hubs-messaging-exceptions.md).

### <a name="diagnostic-logs"></a>Dzienniki diagnostyczne
Usługa Event Hubs obsługuje dwa typy [dzienników diagnostycznych](event-hubs-diagnostic-logs.md) -przechwytywania dzienniki błędów i operacyjne dzienniki — które są reprezentowane w formacie json i można włączyć za pomocą portalu Azure.

### <a name="support-and-sla"></a>Pomoc techniczna i umowa SLA
Pomoc techniczna dla usługi Event Hubs jest dostępna za pośrednictwem [Fora społeczności użytkowników](https://social.msdn.microsoft.com/forums/azure/home?forum=servbus). Pomoc dotycząca rozliczeń i subskrypcji jest świadczona bezpłatnie.

Aby dowiedzieć się więcej na temat naszych umowy SLA, zobacz [umowy dotyczące poziomu usług](https://azure.microsoft.com/support/legal/sla/) strony.

## <a name="next-steps"></a>Kolejne kroki
Następujące linki pozwalają dowiedzieć się więcej na temat usługi Event Hubs:

* [Omówienie usługi Event Hubs](event-hubs-what-is-event-hubs.md)
* [Tworzenie centrum zdarzeń](event-hubs-create.md)
* [Auto zwiększyć zdarzeń koncentratory](event-hubs-auto-inflate.md)
