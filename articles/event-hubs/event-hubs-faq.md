---
title: Często zadawane pytania — usługa Azure Event Hubs | Dokumentacja firmy Microsoft
description: Ten artykuł zawiera listę często zadawanych pytań (FAQ) dla usługi Azure Event Hubs i odpowiedzi.
services: event-hubs
documentationcenter: na
author: ShubhaVijayasarathy
manager: timlt
ms.service: event-hubs
ms.topic: article
ms.custom: seodec18
ms.date: 05/15/2019
ms.author: shvija
ms.openlocfilehash: c5e58f7bc89fbe2d93f6610465abf4a92fd31406
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66476118"
---
# <a name="event-hubs-frequently-asked-questions"></a>Usługa Event Hubs — często zadawane pytania

## <a name="general"></a>Ogólne

### <a name="what-is-an-event-hubs-namespace"></a>Co to jest obszar nazw usługi Event Hubs?
Przestrzeń nazw jest kontenerem określania zakresu dla zdarzenia koncentratora/tematów platformy Kafka. Pozwala ona unikatową [FQDN](https://en.wikipedia.org/wiki/Fully_qualified_domain_name). Przestrzeń nazw służy jako kontener aplikacji, które mogą znajdować się wiele tematy usługi Event Hub/platformy Kafka. 

### <a name="when-do-i-create-a-new-namespace-vs-use-an-existing-namespace"></a>Podczas tworzenia nowej przestrzeni nazw, a użycie istniejącej przestrzeni nazw?
Alokacje pojemności ([jednostek przepływności (jednostek przepływności)](#throughput-units)) są naliczane na poziomie przestrzeni nazw. Przestrzeń nazw jest także skojarzone z regionem.

Można utworzyć nowy obszar nazw, zamiast używania istniejących jeden w jeden z następujących scenariuszy: 

- Należy Centrum zdarzeń skojarzone z nowym regionie.
- Należy Centrum zdarzeń skojarzone z innej subskrypcji.
- Potrzebujesz Centrum zdarzeń za pomocą alokacji distinct pojemności (czyli pojemności są wymagane dla przestrzeni nazw z Centrum zdarzeń dodano przekracza próg 40 jednostek Przepływności, a nie chcesz przejść do dedykowanych klastrów)  

### <a name="what-is-the-difference-between-event-hubs-basic-and-standard-tiers"></a>Jaka jest różnica między warstwami standardowa i Event hubs w warstwie podstawowa?

Warstwy standardowa usługi Azure Event Hubs udostępnia funkcje, które są dostępne w warstwie podstawowa. Dołączone Standard są następujące funkcje:

* Dłuższy okres przechowywania zdarzeń
* Dodatkowe połączenia obsługiwane przez brokera z naliczeniem dodatkowych opłat za większa niż liczba uwzględnione
* Więcej niż jeden [grupy konsumentów](event-hubs-features.md#consumer-groups)
* [Przechwytywanie](event-hubs-capture-overview.md)
* [Integracja z platformą Kafka](event-hubs-for-kafka-ecosystem-overview.md)

Aby uzyskać więcej informacji na temat cen warstwy, w tym Event hubs w warstwie dedykowana, zobacz [Event Hubs — cennik szczegóły](https://azure.microsoft.com/pricing/details/event-hubs/).

### <a name="where-is-azure-event-hubs-available"></a>Gdzie jest usługi Azure Event Hubs?

Usługa Azure Event Hubs jest dostępna we wszystkich obsługiwanych regionach platformy Azure. Aby uzyskać listę, odwiedź stronę [regionów świadczenia usługi Azure](https://azure.microsoft.com/regions/) strony.  

### <a name="can-i-use-a-single-amqp-connection-to-send-and-receive-from-multiple-event-hubs"></a>Wysyłanie i odbieranie z wielu usługi event hubs można używać przez pojedyncze połączenie AMQP?

Tak, o ile wszystkie centra zdarzeń znajdują się w tej samej przestrzeni nazw.

### <a name="what-is-the-maximum-retention-period-for-events"></a>Co to jest maksymalny okres przechowywania dla zdarzeń?

Event Hubs w warstwie standardowa warstwa obsługuje obecnie maksymalny okres przechowywania siedmiu dni. Usługa Event hubs nie jest przeznaczona do stałego przechowywania danych. Okresy przechowywania powyżej 24 godzin są przeznaczone dla scenariuszy, w których jest wygodne jest ponowne odtworzenie strumienia zdarzeń w tych samych systemach; na przykład w celu wytrenowania lub sprawdzenia nowego modelu uczenia maszynowego na istniejących danych. Potrzebujesz komunikat przechowywanie danych powyżej siedem dni, umożliwiając [przechwytywania usługi Event Hubs](event-hubs-capture-overview.md) zdarzenia Centrum ściąga dane z Centrum zdarzeń w na wybrane konto magazynu lub usługą Azure Data Lake wybrane. Włączenie funkcji Capture spowoduje naliczenie opłaty oparte na Twoje zakupionych jednostek przepływności.

### <a name="how-do-i-monitor-my-event-hubs"></a>Jak monitorować mojej usługi Event Hubs?
Usługa Event Hubs emituje wyczerpujący metryki, które zapewniają stan zasobów w celu [usługi Azure Monitor](../azure-monitor/overview.md). Pozwalają one również ocenić ogólną kondycję usługi Event Hubs, nie tylko na poziomie przestrzeni nazw, ale także na poziomie jednostki. Dowiedz się więcej o funkcji monitorowania, które jest oferowana w przypadku [usługi Azure Event Hubs](event-hubs-metrics-azure-monitor.md).

### <a name="what-ports-do-i-need-to-open-on-the-firewall"></a>Które porty należy otworzyć w zaporze? 
Następujące protokoły za pomocą usługi Azure Service Bus umożliwia wysyłanie i odbieranie wiadomości:

- Advanced Message Queuing Protocol (AMQP)
- HTTP
- Apache Kafka

Zobacz poniższą tabelę dla portów wychodzących, które należy otworzyć, aby używać tych protokołów do komunikacji z usługą Azure Event Hubs. 

| Protocol | Porty | Szczegóły | 
| -------- | ----- | ------- | 
| AMQP | 5671 i 5672 | Zobacz [przewodnik dotyczący protokołu AMQP](../service-bus-messaging/service-bus-amqp-protocol-guide.md) | 
| HTTP, HTTPS | 80, 443 |  |
| Kafka | 9093 | Zobacz [za pomocą Event Hubs z poziomu aplikacji platformy Kafka](event-hubs-for-kafka-ecosystem-overview.md)

### <a name="what-ip-addresses-do-i-need-to-whitelist"></a>Jakie adresy IP należy do listy dozwolonych?
Aby znaleźć odpowiednie adresy IP w celu białą listę połączeń, wykonaj następujące kroki:

1. Uruchom następujące polecenie w wierszu polecenia: 

    ```
    nslookup <YourNamespaceName>.servicebus.windows.net
    ```
2. Zanotuj adres IP zwrócony `Non-authoritative answer`. Ten adres IP jest statyczne. Tylko punkt, w czasie, które go zmienić jest, jeśli można przywrócić przestrzeni nazw do innego klastra.

Jeśli używasz nadmiarowości strefy dla swojego obszaru nazw, należy wykonać kilka dodatkowych kroków: 

1. Najpierw uruchom narzędzie nslookup w przestrzeni nazw.

    ```
    nslookup <yournamespace>.servicebus.windows.net
    ```
2. Zanotuj nazwę w **nieautorytatywnej odpowiedzi** sekcji, która znajduje się w jednej z następujących formatów: 

    ```
    <name>-s1.servicebus.windows.net
    <name>-s2.servicebus.windows.net
    <name>-s3.servicebus.windows.net
    ```
3. Uruchom narzędzie nslookup dla każdego z nich przy użyciu sufiksy s1, s2 i s3 w celu uzyskania adresów IP wszystkich trzech wystąpień w trzech strefach dostępności 

## <a name="apache-kafka-integration"></a>Integracja platformy Apache Kafka

### <a name="how-do-i-integrate-my-existing-kafka-application-with-event-hubs"></a>Jak zintegrować mojej istniejącej aplikacji platformy Kafka z usługą Event Hubs?
Usługa Event Hubs udostępnia punkt końcowy platformy Kafka, używany przy użyciu aplikacji platformy Apache Kafka, na podstawie istniejących. Zmiana konfiguracji jest wszystko, co jest wymagane do środowisko PaaS platformy Kafka. Zapewnia zamiast uruchamiania klastra Kafka. Usługa Event Hubs obsługuje Apache Kafka w wersji 1.0 i nowszych wersji klienta i współdziała z istniejącej aplikacji platformy Kafka, narzędzi i struktur. Aby uzyskać więcej informacji, zobacz [usługi Event Hubs dla platformy Kafka repozytorium](https://github.com/Azure/azure-event-hubs-for-kafka).

### <a name="what-configuration-changes-need-to-be-done-for-my-existing-application-to-talk-to-event-hubs"></a>Zmiany w konfiguracji potrzebnych do wykonania dla mojej istniejącej aplikacji, aby komunikować się z usługi Event Hubs?
Aby połączyć się wraz z Centrum zdarzeń z obsługą platformy Kafka, należy zaktualizować konfiguracje klienta platformy Kafka. Odbywa się przez utworzenie przestrzeni nazw usługi Event Hubs i uzyskania [parametry połączenia](event-hubs-get-connection-string.md). Zmień bootstrap.servers, aby wskazywał 9093 FQDN centra zdarzeń i numer portu. Aktualizacja sasl.jaas.config można kierować platformy Kafka klienta do punktu końcowego dla komputerów z obsługą platformy Kafka z usługi Event Hubs (czyli uzyskano parametry połączenia), z odpowiednimi uwierzytelniania, jak pokazano poniżej:

bootstrap.Servers={your. EVENTHUBS. Nazwa FQDN}: 9093 request.timeout.ms=60000 security.protocol=SASL_SSL sasl.mechanism=PLAIN sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule wymagana nazwa użytkownika = hasło "$ConnectionString" = "{YOUR. EVENTHUBS. POŁĄCZENIE. CIĄG} ";

Przykład:

bootstrap.Servers=dummynamespace.servicebus.Windows.NET:9093 request.timeout.ms=60000 security.protocol=SASL_SSL sasl.mechanism=PLAIN sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule wymagane username = "$ Password="Endpoint=sb://dummynamespace.servicebus.windows.net/ ConnectionString"; SharedAccessKeyName = DummyAccessKeyName; SharedAccessKey = 5dOntTRytoC24opYThisAsit3is2B + OGY1US/fuL3ly = ";

Uwaga: Jeśli sasl.jaas.config nie jest obsługiwaną konfiguracją w preferowanej struktury, można znaleźć konfiguracji, które są używane do ustawiania SASL nazwy użytkownika i hasło i zamiast tego użyj tych. Ustaw nazwę użytkownika $ConnectionString i hasło, aby parametry połączenia usługi Event Hubs.

### <a name="what-is-the-messageevent-size-for-kafka-enabled-event-hubs"></a>Jaki jest rozmiar komunikatu lub zdarzenia dla komputerów z obsługą platformy Kafka z usługi Event Hubs?
Maksymalny rozmiar komunikatu dozwolony dla komputerów z obsługą platformy Kafka z usługi Event Hubs to 1MB.

## <a name="throughput-units"></a>Jednostki przepływności

### <a name="what-are-event-hubs-throughput-units"></a>Co to są jednostki przepływności usługi Event Hubs?
Przepływności usługi Event hubs definiuje ilości danych w megabajty lub liczba zdarzeń 1 KB (w tysiącach), że przychodzący i wychodzący za pośrednictwem usługi Event Hubs. To przepływność jest mierzona w jednostkach przepływności (jednostek przepływności). Zakup jednostek przepływności, zanim rozpoczniesz korzystanie z usługi Event Hubs. Możesz jawnie wybrać jednostek przepływności centra zdarzeń przy użyciu portalu lub szablonów usługi Event Hubs Resource Manager. 


### <a name="do-throughput-units-apply-to-all-event-hubs-in-a-namespace"></a>Czy jednostki przepływności dotyczą wszystkich centrów zdarzeń w przestrzeni nazw?
Tak, jednostek przepływności (jednostek przepływności) mają zastosowanie do wszystkich centrów zdarzeń w przestrzeni nazw usługi Event Hubs. Oznacza, że zakupu jednostek przepływności na poziomie przestrzeni nazw i są współużytkowane przez centra zdarzeń w ramach tej przestrzeni nazw. Każdy jednostek Przepływności uprawnia przestrzeń nazw do następujących funkcji:

- Do 1 MB na sekundę transferu danych przychodzących zdarzenia (zdarzenia wysyłane do Centrum zdarzeń), ale nie więcej niż 1000 przyjętych zdarzeń, operacji zarządzania lub formantu API wywołań na sekundę.
- Do 2 MB na sekundę zdarzeń (zdarzenia używane z Centrum zdarzeń), ale nie więcej niż 4096 zdarzeń.
- Do 84 GB magazynu zdarzeń, (wystarczająca dla domyślnego okresu przechowywania 24-godzinny).

### <a name="how-are-throughput-units-billed"></a>Jak jednostki przepływności są rozliczane?
Jednostki przepływności (jednostek przepływności) są naliczane godzinowo. Rozliczenie opiera się na maksymalną liczbę jednostek, który został wybrany podczas danej godziny. 

### <a name="how-can-i-optimize-the-usage-on-my-throughput-units"></a>Jak zoptymalizować użycie na Moje jednostki przepływności
Rozpocznij najmniejsza jedną jednostkę przepływności (TU) i Włącz [automatyczne rozszerzanie](event-hubs-auto-inflate.md). Automatyczne rozszerzanie funkcja umożliwia Ci się rozwijać swoje jednostek przepływności w miarę wzrostu ruchu/ładunku. Można również ustawić górny limit liczby jednostek przepływności.

### <a name="how-does-auto-inflate-feature-of-event-hubs-work"></a>Jak działa automatyczne rozszerzanie funkcji usługi Event hubs?
Automatyczne rozszerzanie funkcji umożliwia skalowanie w górę jednostek przepływności (jednostek przepływności). Oznacza to, że można uruchomić kupując niski jednostek przepływności i automatyczne rozszerzanie skaluje się Twoje jednostek przepływności usługi transferu danych przychodzących wraz ze wzrostem. Oferuje on ekonomiczna opcja i pełną kontrolę nad liczbę jednostek przepływności do zarządzania. Ta funkcja jest **skalowanie w górę tylko** funkcji, a całkowicie kontrolowania skalowanie w dół liczbę jednostek przepływności, aktualizując je. 

Możesz zaczynać się niskim jednostek przepływności (jednostek przepływności), na przykład 2 jednostek przepływności. Jeśli przewidzieć, że ruch sieciowy może przekroczyć 15 jednostek przepływności, wykonując automatyczne rozszerzanie funkcji w ramach przestrzeni nazw, a następnie ustaw maksymalny limit 15 jednostek przepływności. Możesz teraz rozwinąć swoje jednostek przepływności automatycznie wraz ze wzrostem natężenia ruchu.

### <a name="is-there-a-cost-associated-when-i-turn-on-the-auto-inflate-feature"></a>Wiążą się po włączeniu automatyczne rozszerzanie funkcji?
Brak **bez kosztów** skojarzone z tą funkcją. 

### <a name="how-are-throughput-limits-enforced"></a>Jak są wymuszane limity przepustowości?
Jeśli przepustowość łączny ruch przychodzący lub występowania zdarzeń łączny ruch przychodzący we wszystkich centrach zdarzeń w przestrzeni nazw przekroczy agregacji jednostek przepływności, nadawcy są ograniczane i otrzymywać komunikaty o błędach wskazujące, że przekroczony został przydział transferu danych przychodzących.

Jeśli przepływności łączny ruch wychodzący lub szybkość ruchu wychodzącego łączna liczba zdarzeń we wszystkich centrach zdarzeń w przestrzeni nazw przekroczy agregacji jednostek przepływności, odbiorcy są ograniczane i otrzymywać komunikaty o błędach wskazujące, że przekroczony został przydział ruchu wychodzącego. Przydziały transferu danych przychodzących i wychodzących są wymuszane oddzielnie, dlatego, że nadawca nie może spowodować spowolnienia przyjmowania zdarzeń, ani odbiorca uniemożliwi zdarzenia są wysyłane do Centrum zdarzeń.

### <a name="is-there-a-limit-on-the-number-of-throughput-units-tus-that-can-be-reservedselected"></a>Czy istnieje limit liczby jednostek przepływności (jednostek przepływności), które mogą być zarezerwowana wybrane?
W wielu dzierżawców, oferty jednostki przepływności można powiększać maksymalnie 40 jednostek przepływności (można wybrać maksymalnie 20 jednostek przepływności w portalu i zgłosić bilet pomocy technicznej, aby podnieść go do 40 jednostek przepływności na tej samej przestrzeni nazw). Po przekroczeniu 40 jednostek przepływności, usługa Event Hubs zapewnia modelu opartego na zasobach/pojemności, o nazwie **Event hubs w warstwie dedykowana klastrów**. Dedykowane klastry są sprzedawane w jednostek pojemności (CUs).

## <a name="dedicated-clusters"></a>Dedykowane klastry

### <a name="what-are-event-hubs-dedicated-clusters"></a>Co to są klastry usługi Event hubs w warstwie dedykowana?
Event hubs w warstwie dedykowana klastrów oferują wdrożenia pojedynczej dzierżawy dla klientów o najbardziej wygórowanych wymaganiach. Ta oferta tworzy klaster oparty na pojemności, który nie jest powiązany za jednostki przepływności. Oznacza to, że można wykorzystywać klastra i dotyczące odbierania oraz przesyłanie strumieniowe danych zgodnie z ustawieniem wykorzystanie Procesora i pamięci klastra. Aby uzyskać więcej informacji, zobacz [Event hubs w warstwie dedykowana klastrów](event-hubs-dedicated-overview.md).

### <a name="how-much-does-a-single-capacity-unit-let-me-achieve"></a>Jaka jednostki pojemności pojedynczego Pozwól mi osiągnąć?
W przypadku dedykowanego klastra ile pozyskiwania i przesyłanie strumieniowe zależy od różnych czynników, takich jak usługi producentów konsumentów, szybkość, z jaką są wprowadzane i przetwarzania i wiele więcej. 

Poniższej tabeli przedstawiono wyniki testów porównawczych, że możemy osiągnąć podczas testowania naszych:

| Kształt ładunku | Odbiorniki | Przepustowość ruchu przychodzącego| Komunikaty przychodzące | Przepustowość danych wychodzących | Komunikaty wychodzące | Łączna liczba jednostek przepływności | Jednostek przepływności na jednostkę pojemności |
| ------------- | --------- | ---------------- | ------------------ | ----------------- | ------------------- | --------- | ---------- |
| Partie 100x1KB | 2 | 400 MB/s | 400 wiad KB/s | 800 MB/s | 800 KB wiad. / s | 400 jednostek przepływności | 100 jednostek przepływności | 
| Partie 10x10KB | 2 | 666 MB/s | 66.6 wiad KB/s | 1.33 GB/s | 133 wiad KB/s | 666 jednostek przepływności | 166 jednostek przepływności |
| Partie 6x32KB | 1 | 1,05 GB/s | 34 wiad KB / s | 1,05 GB/s | 34 wiad KB/s | 1000 jednostek przepływności | 250 jednostek przepływności |

Podczas testowania, użyto następujących kryteriów:

- Dedykowany klaster usługi Event Hubs przy użyciu czterech jednostek pojemności (CUs) został użyty. 
- Centrum zdarzeń, umożliwiający wprowadzanie ma partycje 200. 
- Dane, które zostało pozyskane została odebrana przez dwie aplikacje odbiorcy odbieranie od wszystkich partycji.

Wyniki daje wyobrażenie o perspektywę wykraczającą z klastrem dedykowany usługi Event Hubs. Ponadto klaster Dedykuj jest dostarczany z usługi Event Hubs Capture włączony dla Twoich scenariuszy micro-batch- i długoterminowego przechowywania.

### <a name="how-do-i-create-an-event-hubs-dedicated-cluster"></a>Jak utworzyć klaster usługi Event hubs w warstwie dedykowana?
Utworzyć dedykowany klaster usługi Event Hubs, przesyłając [żądania pomocy technicznej zwiększenia limitu przydziału](https://portal.azure.com/#create/Microsoft.Support) lub kontaktując się [zespołu usługi Event Hubs](mailto:askeventhubs@microsoft.com). Trwa zwykle około dwóch tygodni, można pobrać klastrów wdrożone i przekazywany do użytku przez Ciebie. Ten proces jest tymczasowy do momentu kompletna Samoobsługa są udostępniane za pośrednictwem witryny Azure portal lub szablonów usługi Azure Resource Manager, który trwa około 2 godziny do wdrożenia klastra.

## <a name="best-practices"></a>Najlepsze praktyki

### <a name="how-many-partitions-do-i-need"></a>Jak wiele partycji jest potrzebne?

Liczba partycji w Centrum zdarzeń nie można zmodyfikować po zakończeniu instalacji. Mając to na uwadze ważne jest myśleć o partycjach ilu potrzebujesz przed rozpoczęciem pracy. 

Usługa Event Hubs umożliwia czytnik jednej partycji dla każdej grupy odbiorców. W większości przypadków użycia domyślne ustawienie cztery partycje jest wystarczająca. Jeśli chcesz skalować przetwarzanie zdarzeń, warto rozważyć dodatkowe partycje. Nie ma żadnego limitu określonego przepływność na partycję, jednak zagregowanej przepływności w przestrzeni nazw jest ograniczona przez liczbę jednostek przepływności. Podczas zwiększysz liczbę jednostek przepływności w przestrzeni nazw, może okazać się dodatkowe partycje, aby umożliwić współbieżnych czytników do osiągnięcia własnych maksymalną przepustowość.

Jednak w przypadku modelu, w którym aplikacja ma koligacji określonej partycji zwiększenie liczby partycji może nie być korzyści, użytkownik. Aby uzyskać więcej informacji, zobacz [availability and consistency i](event-hubs-availability-and-consistency.md).

## <a name="pricing"></a>Cennik

### <a name="where-can-i-find-more-pricing-information"></a>Gdzie mogę znaleźć więcej informacji o cenach

Aby uzyskać pełne informacje na temat cen usługi Event Hubs, zobacz [Event Hubs — cennik szczegóły](https://azure.microsoft.com/pricing/details/event-hubs/).

### <a name="is-there-a-charge-for-retaining-event-hubs-events-for-more-than-24-hours"></a>Czy jest opłata za zachowywanie zdarzeń usługi Event Hubs przez więcej niż 24 godziny?

Warstwa standardowa centra zdarzeń umożliwiają przechowywanie komunikatów dłużej niż 24 godziny przez maksymalnie siedem dni. Jeśli z całkowitej liczby przechowywanych zdarzeń przekroczy limit magazynu dla liczby wybranych jednostek przepływności (84 GB na jednostkę), rozmiar który przekroczy limit jest rozliczany według stawki opublikowanych obiektów Blob platformy Azure storage. Limit magazynu dla każdej jednostki przepływności obejmuje wszystkie koszty przechowywania przez okres 24 godzin (ustawienie domyślne) nawet jeśli jednostka przepływności ma do limitu maksymalnej transferu danych przychodzących.

### <a name="how-is-the-event-hubs-storage-size-calculated-and-charged"></a>Jak jest rozmiar magazynu usługi Event Hubs obliczane i rozliczane?

Całkowity rozmiar wszystkich zapisanych zdarzeń wraz ze wszystkimi narzutami wewnętrznymi na nagłówki zdarzeń w strukturach przechowywania dysku we wszystkich centrach zdarzeń jest mierzony przez cały dzień. Na koniec dnia wyliczana jest szczytowa wielkość magazynu. Dzienny limit magazynu jest wyliczany na podstawie minimalnej liczby jednostek przepływności, które zostały wybrane w ciągu dnia (każda jednostka przepływności ma limit 84 GB). Jeśli całkowity rozmiar przekracza obliczeniowe dzienny limit magazynu, obszar magazynu przekraczający jest opłacie za magazyn obiektów Blob platformy Azure (w **magazyn lokalnie nadmiarowy** współczynnik).

### <a name="how-are-event-hubs-ingress-events-calculated"></a>Jak są obliczane zdarzenia transferu danych przychodzących usługi Event Hubs?

Każde zdarzenie przesłane do Centrum zdarzeń jest liczone jako płatny komunikat. *Transferu danych przychodzących zdarzenia* jest zdefiniowany jako jednostka danych o rozmiarze mniejszym niż 64 KB. Dowolne zdarzenie, która jest mniejsza niż lub równym 64 KB jest traktowane jako jako jedno płatne zdarzenie. Jeśli zdarzenie jest większa niż 64 KB, liczba płatnych zdarzeń jest obliczana rozmiar zdarzenia, wielokrotność 64 KB. Na przykład 8 KB wysłane do Centrum zdarzeń jest rozliczany jako jedno zdarzenie, ale komunikat rozmiarze 96 KB wysłany do Centrum zdarzeń jest rozliczany jako dwa zdarzenia.

Zdarzenia używane z Centrum zdarzeń, ponieważ także operacje zarządzania i wywołania kontrolki, takie jak punkty kontrolne, nie są liczone jako płatne przyjętych zdarzeń, ale są wliczane do limitu jednostek przepływności.

### <a name="do-brokered-connection-charges-apply-to-event-hubs"></a>Czy opłaty za połączenia obsługiwane przez brokera dotyczą usługi Event Hubs?

Opłaty za połączenia się tylko wtedy, gdy jest używany protokół AMQP. Nie ma opłat za połączenia związane z przesyłaniem zdarzeń przez protokół HTTP niezależnie od liczby systemów lub urządzeń wysyłających. Jeśli planowane jest użycie połączeń AMQP (na przykład, aby osiągnąć bardziej efektywnego strumieniowego przesyłania zdarzeń lub zapewnienia komunikacji dwukierunkowej w poleceniu IoT i kontrolować scenariuszy), zobacz [usługi Event Hubs, informacje o cenach](https://azure.microsoft.com/pricing/details/event-hubs/) strony, aby uzyskać szczegółowe informacje, o ile połączenia są uwzględnione w poszczególnych warstwach usług.

### <a name="how-is-event-hubs-capture-billed"></a>W jaki sposób są naliczane opłaty za funkcję przechwytywanie usługi Event Hubs?

Przechwytywanie jest włączona, gdy dowolne Centrum zdarzeń w przestrzeni nazw ma włączoną opcję przechwytywania. Funkcja przechwytywania usługi Event Hubs jest rozliczana co godzinę za zakupioną jednostkę przepływności. W miarę zwiększania lub zmniejszania liczby jednostek przepływności, rozliczanie usługi Event Hubs Capture odzwierciedla te zmiany wyrażane w pełnych. Aby uzyskać więcej informacji na temat rozliczeń przechwytywania usługi Event Hubs, zobacz [usługi Event Hubs, informacje o cenach](https://azure.microsoft.com/pricing/details/event-hubs/).

### <a name="do-i-get-billed-for-the-storage-account-i-select-for-event-hubs-capture"></a>Zostanie naliczona za konto magazynu wybrane dla przechwytywania usługi Event Hubs?

Przechwytywanie korzysta z konta magazynu, który podajesz podczas włączone w Centrum zdarzeń. Jako jest kontem magazynu, wszelkie zmiany w przypadku tej konfiguracji są doliczane do subskrypcji platformy Azure.

## <a name="quotas"></a>Przydziały

### <a name="are-there-any-quotas-associated-with-event-hubs"></a>Czy istnieją wykorzystani limitów przydziałów, skojarzone z usługą Event Hubs?

Aby uzyskać listę wszystkich przydziałów usługi Event Hubs, zobacz [przydziały](event-hubs-quotas.md).

## <a name="troubleshooting"></a>Rozwiązywanie problemów

### <a name="why-am-i-not-able-to-create-a-namespace-after-deleting-it-from-another-subscription"></a>Dlaczego nie mogę utworzyć przestrzeń nazw po usunięciu jej z inną subskrypcję? 
Jeśli usuniesz przestrzeni nazw z subskrypcji, poczekaj przez 4 godziny przed odtworzenia go o tej samej nazwie w innej subskrypcji. W przeciwnym razie może być wyświetlony następujący komunikat o błędzie: `Namespace already exists`. 

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
