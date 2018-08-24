---
title: Usługi Azure Event Hubs — często zadawane pytania | Dokumentacja firmy Microsoft
description: Usługa Azure Event Hubs — często zadawane pytania (FAQ)
services: event-hubs
documentationcenter: na
author: ShubhaVijayasarathy
manager: timlt
ms.service: event-hubs
ms.topic: article
ms.date: 08/07/2018
ms.author: shvija
ms.openlocfilehash: 50491515c7a553107666e29dcba2b4bd0836f409
ms.sourcegitcommit: b5ac31eeb7c4f9be584bb0f7d55c5654b74404ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/23/2018
ms.locfileid: "42746233"
---
# <a name="event-hubs-frequently-asked-questions"></a>Usługa Event Hubs — często zadawane pytania

## <a name="general"></a>Ogólne

### <a name="what-is-the-difference-between-event-hubs-basic-and-standard-tiers"></a>Jaka jest różnica między warstwami standardowa i Event hubs w warstwie podstawowa?

Warstwy standardowa usługi Azure Event Hubs udostępnia funkcje, które są dostępne w warstwie podstawowa. Dołączone Standard są następujące funkcje:

* Dłuższy okres przechowywania zdarzeń
* Dodatkowe połączenia obsługiwane przez brokera z naliczeniem dodatkowych opłat za większa niż liczba uwzględnione
* Więcej niż jeden [grupy konsumentów](event-hubs-features.md#consumer-groups)
* [Przechwytywanie](event-hubs-capture-overview.md)

Aby uzyskać więcej informacji na temat cen warstwy, w tym Event hubs w warstwie dedykowana, zobacz [Event Hubs — cennik szczegóły](https://azure.microsoft.com/pricing/details/event-hubs/).

### <a name="what-are-event-hubs-throughput-units"></a>Co to są jednostki przepływności usługi Event Hubs?

Należy jawnie wybrać jednostek przepływności usługi Event Hubs, za pośrednictwem witryny Azure portal lub szablonów usługi Event Hubs Resource Manager. Jednostki przepływności dotyczą wszystkich centrów zdarzeń w przestrzeni nazw usługi Event Hubs, a każda jednostka przepływności uprawnia przestrzeń nazw do następujących funkcji:

* Do 1 MB na sekundę transferu danych przychodzących zdarzenia (zdarzenia wysyłane do Centrum zdarzeń), ale nie więcej niż 1000 przyjętych zdarzeń, operacji zarządzania lub formantu API wywołań na sekundę.
* Do 2 MB na sekundę zdarzeń (zdarzenia używane z Centrum zdarzeń), ale nie więcej niż 4096 zdarzeń.
* Do 84 GB magazynu zdarzeń (wystarcza na domyślny 24-godzinny okres przechowywania).

Jednostek przepływności usługi Event Hubs są rozliczane godzinowo na podstawie maksymalnej liczby jednostek wybranych w ciągu danej godziny. Można automatycznie [zwiększyć liczbę jednostek przepływności, używając automatyczne rozszerzanie](event-hubs-auto-inflate.md) w miarę zwiększania użycia.

### <a name="how-are-event-hubs-throughput-unit-limits-enforced"></a>Jak są wymuszane limity jednostek przepływności usługi Event Hubs?

Jeśli przepustowość łączny ruch przychodzący lub występowania zdarzeń łączny ruch przychodzący we wszystkich centrach zdarzeń w przestrzeni nazw przekroczy agregacji jednostek przepływności, nadawcy są ograniczane i otrzymywać komunikaty o błędach wskazujące, że przekroczony został przydział transferu danych przychodzących.

Jeśli przepływności łączny ruch wychodzący lub szybkość ruchu wychodzącego łączna liczba zdarzeń we wszystkich centrach zdarzeń w przestrzeni nazw przekroczy agregacji jednostek przepływności, odbiorcy są ograniczane i otrzymywać komunikaty o błędach wskazujące, że przekroczony został przydział ruchu wychodzącego. Przydziały transferu danych przychodzących i wychodzących są wymuszane oddzielnie, dlatego, że nadawca nie może spowodować spowolnienia przyjmowania zdarzeń, ani odbiorca uniemożliwi zdarzenia są wysyłane do Centrum zdarzeń.

### <a name="is-there-a-limit-on-the-number-of-throughput-units-that-can-be-selected"></a>Czy jest jakieś ograniczenie liczby jednostek przepływności, które można wybrać?

Obowiązuje domyślny przydział 20 jednostek przepływności na przestrzeń nazw. Aby zażądać większego limitu przydziału jednostek przepływności, wypełniając bilet pomocy technicznej. Po przekroczeniu limitu jednostek przepływności 20 pakiety są dostępne w 20 do 100 jednostek przepływności. Należy pamiętać, że za pomocą więcej niż 20 jednostek przepływności usuwa możliwość zmiany liczby jednostek przepływności bez wypełniając bilet pomocy technicznej.

Za pomocą [automatyczne rozszerzanie](event-hubs-auto-inflate.md) funkcji, możesz automatycznie zwiększać liczbę jednostek przepływności w miarę zwiększania użycia.

### <a name="can-i-use-a-single-amqp-connection-to-send-and-receive-from-multiple-event-hubs"></a>Wysyłanie i odbieranie z wielu usługi event hubs można używać przez pojedyncze połączenie AMQP?

Tak, o ile wszystkie centra zdarzeń znajdują się w tej samej przestrzeni nazw.

### <a name="what-is-the-maximum-retention-period-for-events"></a>Co to jest maksymalny okres przechowywania dla zdarzeń?

Event Hubs w warstwie standardowa warstwa obsługuje obecnie maksymalny okres przechowywania wynoszący 7 dni. Centra zdarzeń nie są przeznaczone do stałego przechowywania danych. Okresy przechowywania powyżej 24 godzin są przeznaczone dla scenariuszy, w których jest wygodne jest ponowne odtworzenie strumienia zdarzeń w tych samych systemach; na przykład w celu wytrenowania lub sprawdzenia nowego modelu uczenia maszynowego na istniejących danych. Potrzebujesz komunikatu przechowywanie danych powyżej 7 dni, co [przechwytywania usługi Event Hubs](event-hubs-capture-overview.md) zdarzenia Centrum ściąga dane z Centrum zdarzeń w na wybrane konto magazynu lub usługą Azure Data Lake wybrane. Włączenie funkcji Capture spowoduje naliczenie opłaty oparte na Twoje zakupionych jednostek przepływności.

### <a name="where-is-azure-event-hubs-available"></a>Gdzie jest usługi Azure Event Hubs?

Usługa Azure Event Hubs jest dostępna we wszystkich obsługiwanych regionach platformy Azure. Aby uzyskać listę, odwiedź stronę [regionów świadczenia usługi Azure](https://azure.microsoft.com/regions/) strony.  

## <a name="best-practices"></a>Najlepsze praktyki

### <a name="how-many-partitions-do-i-need"></a>Jak wiele partycji jest potrzebne?

Należy pamiętać, że liczba partycji w Centrum zdarzeń nie można zmodyfikować po zakończeniu instalacji. Mając to na uwadze ważne jest myśleć o partycjach ilu potrzebujesz przed rozpoczęciem pracy. 

Usługa Event Hubs umożliwia czytnik jednej partycji dla każdej grupy odbiorców. W większości przypadków użycia domyślne ustawienie cztery partycje jest wystarczająca. Jeśli chcesz skalować przetwarzanie zdarzeń, warto rozważyć dodatkowe partycje. Nie ma żadnego limitu określonego przepływność na partycję, jednak zagregowanej przepływności w przestrzeni nazw jest ograniczona przez liczbę jednostek przepływności. Podczas zwiększysz liczbę jednostek przepływności w przestrzeni nazw, może okazać się dodatkowe partycje, aby umożliwić współbieżnych czytników do osiągnięcia własnych maksymalną przepustowość.

Jednak w przypadku modelu, w którym aplikacja ma koligacji określonej partycji zwiększenie liczby partycji może nie być korzyści, użytkownik. Aby uzyskać więcej informacji na ten temat, zobacz [availability and consistency i](event-hubs-availability-and-consistency.md).

## <a name="pricing"></a>Cennik

### <a name="where-can-i-find-more-pricing-information"></a>Gdzie mogę znaleźć więcej informacji o cenach

Aby uzyskać pełne informacje na temat cen usługi Event Hubs, zobacz [Event Hubs — cennik szczegóły](https://azure.microsoft.com/pricing/details/event-hubs/).

### <a name="is-there-a-charge-for-retaining-event-hubs-events-for-more-than-24-hours"></a>Czy jest opłata za zachowywanie zdarzeń usługi Event Hubs przez więcej niż 24 godziny?

Warstwa standardowa centra zdarzeń umożliwiają przechowywanie komunikatów dłużej niż 24 godziny przez maksymalnie 7 dni. Jeśli z całkowitej liczby przechowywanych zdarzeń przekroczy limit magazynu dla liczby wybranych jednostek przepływności (84 GB na jednostkę), rozmiar który przekroczy limit jest rozliczany według stawki opublikowanych obiektów Blob platformy Azure storage. Limit magazynu dla każdej jednostki przepływności obejmuje wszystkie koszty przechowywania przez okres 24 godzin (ustawienie domyślne) nawet jeśli jednostka przepływności ma do limitu maksymalnej transferu danych przychodzących.

### <a name="how-is-the-event-hubs-storage-size-calculated-and-charged"></a>Jak jest rozmiar magazynu usługi Event Hubs obliczane i rozliczane?

Całkowity rozmiar wszystkich zapisanych zdarzeń wraz ze wszystkimi narzutami wewnętrznymi na nagłówki zdarzeń w strukturach przechowywania dysku we wszystkich centrach zdarzeń jest mierzony przez cały dzień. Na koniec dnia wyliczana jest szczytowa wielkość magazynu. Dzienny limit magazynu jest wyliczany na podstawie minimalnej liczby jednostek przepływności, które zostały wybrane w ciągu dnia (każda jednostka przepływności ma limit 84 GB). Jeśli całkowity rozmiar przekracza obliczeniowe dzienny limit magazynu, obszar magazynu przekraczający jest opłacie za magazyn obiektów Blob platformy Azure (w **magazyn lokalnie nadmiarowy** współczynnik).

### <a name="how-are-event-hubs-ingress-events-calculated"></a>Jak są obliczane zdarzenia transferu danych przychodzących usługi Event Hubs?

Każde zdarzenie przesłane do Centrum zdarzeń jest liczone jako płatny komunikat. *Transferu danych przychodzących zdarzenia* jest zdefiniowany jako jednostka danych o rozmiarze mniejszym niż 64 KB. Dowolne zdarzenie, która jest mniejsza niż lub równym 64 KB jest traktowane jako jako jedno płatne zdarzenie. Jeśli zdarzenie jest większa niż 64 KB, liczba płatnych zdarzeń jest obliczana rozmiar zdarzenia, wielokrotność 64 KB. Na przykład 8 KB wysłane do Centrum zdarzeń jest rozliczany jako jedno zdarzenie, ale komunikat rozmiarze 96 KB wysłany do Centrum zdarzeń jest rozliczany jako dwa zdarzenia.

Zdarzenia używane z Centrum zdarzeń, ponieważ także operacje zarządzania i wywołania kontrolki, takie jak punkty kontrolne, nie są liczone jako płatne przyjętych zdarzeń, ale są wliczane do limitu jednostek przepływności.

### <a name="do-brokered-connection-charges-apply-to-event-hubs"></a>Czy opłaty za połączenia obsługiwane przez brokera dotyczą usługi Event Hubs?

Opłaty za połączenia się tylko wtedy, gdy jest używany protokół AMQP. Nie ma opłat za połączenia związane z przesyłaniem zdarzeń przez protokół HTTP niezależnie od liczby systemów lub urządzeń wysyłających. Jeśli planowane jest użycie połączeń AMQP (na przykład, aby osiągnąć bardziej efektywnego strumieniowego przesyłania zdarzeń lub zapewnienia komunikacji dwukierunkowej w poleceniu IoT i kontrolować scenariuszy), zobacz [usługi Event Hubs, informacje o cenach](https://azure.microsoft.com/pricing/details/event-hubs/) strony, aby uzyskać szczegółowe informacje, o ile połączenia są uwzględnione w poszczególnych warstwach usług.

### <a name="how-is-event-hubs-capture-billed"></a>W jaki sposób są naliczane opłaty za funkcję przechwytywanie usługi Event Hubs?

Przechwytywanie jest włączona, gdy dowolne Centrum zdarzeń w przestrzeni nazw ma włączoną opcję przechwytywania. Funkcja przechwytywania usługi Event Hubs jest rozliczana co godzinę za zakupioną jednostkę przepływności. W miarę zwiększania lub zmniejszania liczby jednostek przepływności, rozliczanie usługi Event Hubs Capture odzwierciedla te zmiany wyrażane w pełnych. Aby uzyskać więcej informacji na temat rozliczeń przechwytywania usługi Event Hubs, zobacz [usługi Event Hubs, informacje o cenach](https://azure.microsoft.com/pricing/details/event-hubs/).

### <a name="will-i-be-billed-for-the-storage-account-i-select-for-event-hubs-capture"></a>Będę dostawać dla konta magazynu, wybrana do przechwytywania usługi Event Hubs?

Przechwytywanie korzysta z konta magazynu, który podajesz podczas włączone w Centrum zdarzeń. Ponieważ jest to Twoje konto magazynu, wszelkie zmiany w przypadku tej konfiguracji są doliczane do subskrypcji platformy Azure.

## <a name="quotas"></a>Przydziały

### <a name="are-there-any-quotas-associated-with-event-hubs"></a>Czy istnieją wykorzystani limitów przydziałów, skojarzone z usługą Event Hubs?

Aby uzyskać listę wszystkich przydziałów usługi Event Hubs, zobacz [przydziały](event-hubs-quotas.md).

## <a name="troubleshooting"></a>Rozwiązywanie problemów

### <a name="what-are-some-of-the-exceptions-generated-by-event-hubs-and-their-suggested-actions"></a>Co to są wyjątki generowane przez centra zdarzeń i ich sugerowane akcje?

Aby uzyskać listę możliwych wyjątków usługi Event Hubs, zobacz [Przegląd wyjątki](event-hubs-messaging-exceptions.md).

### <a name="diagnostic-logs"></a>Dzienniki diagnostyczne

Usługa Event Hubs obsługuje dwa typy [dzienniki diagnostyczne](event-hubs-diagnostic-logs.md) — Przechwytywanie dzienniki błędów i operacyjne dzienniki — które są reprezentowane w formacie json i może zostać włączona w witrynie Azure portal.

### <a name="support-and-sla"></a>Pomoc techniczna i umowa SLA

Pomoc techniczna dla usługi Event Hubs jest dostępna za pośrednictwem [forów społeczności użytkowników](https://social.msdn.microsoft.com/forums/azure/home?forum=servbus). Pomoc dotycząca rozliczeń i subskrypcji jest świadczona bezpłatnie.

Aby dowiedzieć się więcej o umowach SLA, zobacz [umowy dotyczące poziomu usług](https://azure.microsoft.com/support/legal/sla/) strony.

## <a name="next-steps"></a>Kolejne kroki

Następujące linki pozwalają dowiedzieć się więcej na temat usługi Event Hubs:

* [Omówienie usługi Event Hubs](event-hubs-what-is-event-hubs.md)
* [Tworzenie centrum zdarzeń](event-hubs-create.md)
* [Event Hubs automatyczne rozszerzanie](event-hubs-auto-inflate.md)
