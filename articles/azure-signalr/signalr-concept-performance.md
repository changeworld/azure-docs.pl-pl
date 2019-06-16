---
title: Wskazówki dotyczące wydajności dla usługi Azure SignalR Service
description: Przegląd wydajności usługi Azure SignalR Service.
author: sffamily
ms.service: signalr
ms.topic: conceptual
ms.date: 04/08/2019
ms.author: zhshang
ms.openlocfilehash: f7cc05c8c2a299d809c4386d119fef58fa2548d5
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "61269499"
---
# <a name="performance-guide-for-azure-signalr-service"></a>Wskazówki dotyczące wydajności dla usługi Azure SignalR Service

Jednym z kluczowych korzyści z używania usługi Azure SignalR Service jest łatwość skalowania aplikacji SignalR. W przypadku scenariusza na dużą skalę wydajność jest ważnym czynnikiem. 

W tym przewodniku wprowadzimy czynników wpływających na wydajność aplikacji SignalR. Opiszemy typowe wydajności w różnych scenariuszach przypadków użycia. Na koniec wprowadzimy środowiska i narzędzi służących do generowania raportu dotyczącego wydajności.

## <a name="term-definitions"></a>Definicje terminów

*Liczba przychodzących*: Wiadomości przychodzące do usługi Azure SignalR Service.

*Wychodzące*: Wychodzący komunikat z usługi Azure SignalR Service.

*Przepustowość*: Całkowity rozmiar wszystkich komunikatów w ciągu 1 sekundy.

*Domyślny tryb*: Domyślny pracy tryb wystąpienia usługi Azure SignalR Service została utworzona. Usługi Azure SignalR Service oczekuje, że serwer aplikacji nawiązać połączenie z nią przed akceptuje połączenia przez klientów.

*Tryb bez użycia serwera*: Tryb, w którym usługi Azure SignalR Service akceptuje tylko połączenia klienckie. Brak połączenia z serwerem jest dozwolone.

## <a name="overview"></a>Omówienie

Usługi Azure SignalR Service definiuje siedem warstwy standardowa o pojemności maksymalnej wydajności. Ten przewodnik odpowiedzi na następujące pytania:

-   Co to jest typowe wydajności usługi Azure SignalR Service dla poszczególnych warstw?

-   Usługa Azure SignalR Service spełnia moich wymagań dla komunikatów, przepływność (na przykład wysyłanie 100 000 komunikatów na sekundę)?

-   Dla mojego konkretnego scenariusza, która warstwa jest odpowiedni dla mnie? Lub w jaki sposób można wybrać odpowiednie warstwę?

-   Jakiego rodzaju aplikacji serwera (rozmiar maszyny Wirtualnej) jest odpowiedni dla mnie? Ile z nich należy wdrożyć?

Aby odpowiedzieć na następujące pytania, ten przewodnik zapewnia najpierw ogólne omówienie czynników wpływających na wydajność. Następnie przedstawiono maksymalna liczba komunikatów przychodzących i wychodzących dla każdej warstwy dla typowe przypadki użycia: **echo**, **emisji**, **wysyłania do grupy**, i **Wyślij do połączenie** (peer-to-peer przejmować).

Ten przewodnik nie można uwzględnić wszystkie scenariusze (i różnych przypadków użycia, rozmiary wiadomości wzorców wysyłania komunikatu i tak dalej). Ale zapewnia niektórych metod, aby ułatwić:

- Oceń swoje przybliżone wymagania dla komunikatów przychodzących lub wychodzących.
- Znajdź odpowiednie warstw, sprawdzając w tabeli wydajności.

## <a name="performance-insight"></a>Szczegółowe informacje o wydajności

W tej sekcji opisano metodologii oceny wydajności, a następnie wyświetla listę wszystkich czynników wpływających na wydajność. Na koniec zapewnia metody, aby ułatwić ocenę wymagań dotyczących wydajności.

### <a name="methodology"></a>Metodologia

*Przepływność* i *opóźnienie* dwie typowe kwestie sprawdzania wydajności. Dla usługi Azure SignalR Service każda warstwa jednostki SKU ma swój własny przepływności ograniczania zasad. Definiuje zasady *maksymalna dozwolona wartość przepływności (przepustowość ruchu przychodzącego i wychodzącego)* jako maksymalną osiągnięta przepływność, gdy czas oczekiwania, który jest mniejszy niż 1 sekundę 99 procent wiadomości.

Opóźnienie to przedział czasu za pośrednictwem połączenia wysyłania komunikatu do odbierania komunikatów odpowiedzi z usługi Azure SignalR Service. Weźmy **echo** jako przykład. Każde połączenie klienta dodaje sygnaturę czasową w komunikacie. Serwer aplikacji Centrum wysyła oryginalnej wiadomości z powrotem do klienta. Dlatego opóźnienia propagacji łatwo jest obliczana przez każde połączenie klienta. Sygnatura czasowa jest dołączony do każdej wiadomości w **emisji**, **wysyłania do grupy**, i **wysyłać połączenia**.

Aby symulować tysiące jednoczesnych połączeń klienta, wiele maszyn wirtualnych są tworzone w wirtualnej sieci prywatnej, na platformie Azure. Łączenie wszystkich tych maszyn wirtualnych w tym samym wystąpieniu usługi Azure SignalR Service.

W domyślnym trybie usługi Azure SignalR Service serwera aplikacji, maszyny wirtualne są wdrażane w tej samej prywatnej sieci wirtualnej jako klienta maszyn wirtualnych. Wszystkie maszyny wirtualne klienta i serwera aplikacji, maszyny wirtualne są wdrażane w tej samej sieci z tym samym regionie, aby uniknąć opóźnienia między regionami.

### <a name="performance-factors"></a>Czynnikami wydajnościowymi

Teoretycznie pojemność usługi Azure SignalR Service, jest ograniczona przez zasobów obliczeniowych: Procesor CPU, pamięci i sieci. Na przykład więcej połączeń do usługi Azure SignalR Service spowodować, że usługi w celu użycia większej ilości pamięci. Dla większych ruchu komunikatu (na przykład, każdy komunikat jest większa niż 2048 bajtów), usługa Azure SignalR Service trzeba poświęcić więcej cykli procesora CPU do przetwarzania ruchu. W tym samym czasie przepustowość sieci platformy Azure również nakłada limit maksymalny ruchu.

Typ transportu jest innym czynnikiem, który wpływa na wydajność. Są trzy typy [WebSocket](https://en.wikipedia.org/wiki/WebSocket), [-wysyłane — zdarzenie serwera](https://en.wikipedia.org/wiki/Server-sent_events), i [długiego sondowania](https://en.wikipedia.org/wiki/Push_technology). 

Protokół WebSocket jest dwukierunkowe i protokół komunikacyjny pełny dupleks za pośrednictwem jednego połączenia TCP. Server — wysyłane — zdarzenie jest protokołem jednokierunkowe do wypychania komunikatów z serwera do klienta. Długiego sondowania wymaga od klientów do okresowego wysyłania informacji z serwera za pomocą żądania HTTP. Dla tego samego interfejsu API w tych samych warunkach WebSocket ma najlepszą wydajność, Server-wysyłane — zdarzenia jest wolniejszy i długiego sondowania jest niższa. Usługi Azure SignalR Service zaleca WebSocket domyślnie.

Koszt routingu komunikatów ogranicza wydajność. Usługi Azure SignalR Service odgrywa rolę jako router wiadomość, która kieruje wiadomości z zestawu serwerów lub klientów do innych klientów i serwerów. Inny scenariusz lub interfejsu API wymaga różnych zasad routingu. 

Aby uzyskać **echo**, klient wysyła komunikat do samego siebie i routingu obiektem docelowym jest także sam. Ten wzorzec ma najniższą routingu kosztów. Ale w przypadku **emisji**, **wysyłania do grupy**, i **wysyłać do połączenia**, usługi Azure SignalR Service trzeba wyszukać połączenia docelowej przy użyciu danych rozproszonych wewnętrznych Struktura. To dodatkowe przetwarzanie używa więcej czasu Procesora, pamięci i przepustowość sieci. W rezultacie wydajność jest niższa.

W domyślnym trybie serwera aplikacji również mogą stać się wąskim gardłem w niektórych scenariuszach. Zawiera zestaw SDK usługi Azure SignalR do wywołania koncentratora, gdy ta funkcja obsługuje połączenia na żywo z każdego komputera klienckiego za pomocą sygnały pulsu.

W trybie bez użycia serwera klient wysyła komunikat przez post protokołu HTTP, który nie jest wydajne niż protokołu WebSocket.

Innym czynnikiem jest protokół: JSON i [MessagePack](https://msgpack.org/index.html). MessagePack jest mniejszy rozmiar i dostarczane szybciej niż JSON. MessagePack nie mogą jednak zwiększyć wydajność. Wydajność usługi Azure SignalR Service nie jest wrażliwa na protokoły, ponieważ jej nie dekodowania ładunku komunikatu podczas przekazywania wiadomości z klientów na serwerach lub na odwrót.

Podsumowanie następujące czynniki mają wpływ na wydajność dla ruchu przychodzącego i wychodzącego:

-   Warstwa jednostki SKU (Procesora/pamięci)

-   Liczba połączeń

-   Rozmiar komunikatu

-   wskaźnik wysyłania wiadomości

-   Typ transportu (WebSocket, Server-wysyłane — zdarzenia lub długiego sondowania)

-   Scenariusz przypadków użycia (koszt routingu)

-   połączenia server i usługę aplikacji (w trybie serwera)


### <a name="finding-a-proper-sku"></a>Znajdowanie odpowiednich jednostek SKU

Jak można obliczyć pojemności dla ruchu przychodzącego/wychodzącego lub odnaleźć warstwy, która jest odpowiednia dla specyficznego przypadku użycia?

Przyjęto założenie, że serwer aplikacji jest wystarczająca i nie stanowi wąskie gardło. Następnie sprawdź maksymalnej przepustowości dla ruchu przychodzącego i wychodzącego dla każdej warstwy.

#### <a name="quick-evaluation"></a>Szybką ocenę

Teraz uprościć oceny najpierw, zakładając, że niektóre ustawienia domyślne: 

- Typ transportu jest protokołu WebSocket.
- Rozmiar komunikatu wynosi 2048 bajtów.
- Wiadomość jest wysyłana co 1 s.
- Azure SignalR Service to w domyślnym trybie.

Każdą warstwę ma swoją własną maksymalnej przepustowości dla ruchu przychodzącego i wychodzącego przepustowości. Bezproblemowe komfortu nie jest gwarantowana po połączeń przychodzących lub wychodzących przekracza limit.

**Echo** zapewnia maksymalnej przepustowości dla ruchu przychodzącego, ponieważ ma ona routingu najniższy koszt. **Emisja** definiuje przepustowości maksymalnej wiadomości wychodzących.

Czy *nie* przekracza wyróżnione wartości w dwóch poniższych tabelach.

|       Echo                        | Unit1 | Unit2 | Unit5 | Unit10 | Unit20 | Unit50 | Unit100 |
|-----------------------------------|-------|-------|-------|--------|--------|--------|---------|
| Połączenia                       | 1000 | 2000 | 5,000 | 10 000 | 20,000 | 50,000 | 100,000 |
| **Przepustowości dla ruchu przychodzącego** | **2 MB/s**    | **4 MB/s**    | **10 MB/s**   | **20 MB/s**    | **40 MBps**    | **100 MB/s**   | **200 MB/s**    |
| Przepustowość ruchu wychodzącego | 2 MB/s   | 4 MB/s   | 10 MB/s  | 20 MB/s   | 40 MB/s   | 100 MB/s  | 200 MB/s   |


|     Emisja             | Unit1 | Unit2 | Unit5  | Unit10 | Unit20 | Unit50  | Unit100 |
|---------------------------|-------|-------|--------|--------|--------|---------|---------|
| Połączenia               | 1000 | 2000 | 5,000  | 10 000 | 20,000 | 50,000  | 100,000 |
| Przepustowości dla ruchu przychodzącego  | 4 Kb/s   | 4 Kb/s   | 4 Kb/s    | 4 Kb/s    | 4 Kb/s    | 4 Kb/s     | 4 Kb/s    |
| **Przepustowość ruchu wychodzącego** | **4 MB/s**    | **8 MB/s**    | **20 MB/s**    | **40 MBps**    | **80 MB/s**    | **200 MB/s**    | **400 MB/s**   |

*Liczba przychodzących przepustowości* i *przepustowości wychodzącej* rozmiar łączna liczba komunikatów na sekundę.  Poniżej przedstawiono formuł dla nich:
```
  inboundBandwidth = inboundConnections * messageSize / sendInterval
  outboundBandwidth = outboundConnections * messageSize / sendInterval
```

- *inboundConnections*: Liczba połączeń wysyłania komunikatu.

- *outboundConnections*: Liczba połączeń odbierania komunikatów.

- *messageSize*: Rozmiar pojedynczej wiadomości (wartość średnia). Małe komunikat, który to mniej niż 1024 bajty, ma wpływ na wydajność podobny komunikat 1024 bajtów.

- *SendInterval*: Godzina wysłania jeden komunikat. Zazwyczaj jest 1 sekundę na komunikat, co oznacza wysyłanie jeden komunikat co sekundę. Mniejszego oznacza wysyłanie więcej wiadomości w danym okresie. Na przykład 0,5 sekundy na komunikat oznacza, że wysyłanie komunikatów co sekundę.

- *Połączenia*: Zatwierdzone maksymalną wartość progową dla usługi Azure SignalR Service dla każdej warstwy. Jeśli dodatkowo zwiększa się liczba połączeń, będzie odczuwają ograniczania przepustowości połączenia.

#### <a name="evaluation-for-complex-use-cases"></a>Ocena dla przypadków użycia złożonych

##### <a name="bigger-message-size-or-different-sending-rate"></a>Większy rozmiar komunikatu lub inną szybkość wysyłania

Przypadek użycia w rzeczywistości jest bardziej skomplikowany. Jest w stanie może wysyłać komunikat większa niż 2048 bajtów lub szybkość wysyłania wiadomości jest nie jeden komunikat na sekundę. Na przykład można znaleźć ocenianie jego wydajność, Przyjrzyjmy się emisja Unit100 firmy.

W poniższej tabeli przedstawiono przypadek użycia w rzeczywistości **emisji**. Ale rozmiar komunikatu, liczba połączeń i częstotliwość wysyłania wiadomości różnią się od co firma Microsoft zakłada, że w poprzedniej sekcji. Pytanie jest o tym, jak firma Microsoft dowolnego z tych elementów (rozmiar komunikatu, liczba połączeń lub szybkość wysyłania wiadomości) wywnioskować gdy wiemy, że tylko dwa z nich.

| Emisja  | Rozmiar komunikatu | Komunikaty przychodzące na sekundę | Połączenia | Wyślij interwały |
|---|---------------------|--------------------------|-------------|-------------------------|
| 1 | 20 KB                | 1                        | 100,000     | 5 s                      |
| 2 | 256 KB               | 1                        | 8000       | 5 s                      |

Następująca formuła jest łatwe wywnioskować na podstawie poprzednie formuły:

```
outboundConnections = outboundBandwidth * sendInterval / messageSize
```

Dla Unit100 maksymalna przepustowość ruchu wychodzącego jest 400 MB z poprzedniej tabeli. Rozmiar komunikatu o rozmiarze 20-KB, maksymalna liczba połączeń wychodzących powinna być 400 MB \* 5 / 20 KB = 100 000, który pasuje do wartości rzeczywistych.

##### <a name="mixed-use-cases"></a>Zastosowań mieszanych

Przypadek użycia rzeczywistych ze sobą zwykle napisana cztery podstawowego zastosowań: **echo**, **emisji**, **wysyłania do grupy**, i **wysyłać połączenia**. Metodologia, która umożliwia ocenę pojemność jest:

1. Przypadki użycia mieszane należy podzielić na cztery podstawowego zastosowań.
1. Oblicz przepustowości maksymalnej wiadomości przychodzących i wychodzących za pomocą formuł poprzedniego oddzielnie.
1. Suma obliczenia przepustowości można pobrać całkowitej maksymalnej przepustowości dla ruchu przychodzącego/wychodzącego. 

Następnie wybierz odpowiednie warstwy z tabel maksymalnej przepustowości dla ruchu przychodzącego/wychodzącego.

> [!NOTE]
> Do wysyłania wiadomości do setek lub tysięcy mniejszym grupom lub tysięcy klientów wysyłania komunikatu do siebie nawzajem stanie się dominujący koszt routingu. Mieć wpływ na konto.

Dla przypadku użycia wysyłania wiadomości do klientów, upewnij się, czy serwer aplikacji jest *nie* wąskie gardło. W poniższej sekcji "Case study", zawiera wytyczne dotyczące liczby serwerów aplikacji należy i jak wiele połączeń z serwerem, należy skonfigurować.

## <a name="case-study"></a>Analiza przypadku

Poniższe sekcje przechodzą przez cztery typowe przypadki użycia dla transportu WebSocket: **echo**, **emisji**, **wysyłania do grupy**, i **wysyłać połączenia**. Dla każdego scenariusza w sekcji przedstawiono bieżąca pojemność dla ruchu przychodzącego i wychodzącego dla usługi Azure SignalR Service. Objaśniono także główne czynniki wpływające na wydajność.

W domyślnym trybie serwer aplikacja tworzy pięć połączenia z serwerem za pomocą usługi Azure SignalR Service. Domyślnie serwer aplikacji używa usługi Azure SignalR Service SDK. W następujących wyników testu wydajności połączenia z serwerem są podwyższone do 15 (lub więcej emisji i wysyłanie wiadomości do dużych grup).

Różnych przypadków użycia mają różne wymagania dotyczące serwerów aplikacji. **Emisja** wymaga niewielkiej liczby serwerów aplikacji. **Echo** lub **wysyłać połączenia** wymaga wielu serwerów aplikacji.

We wszystkich przypadkach użycia, domyślny rozmiar komunikatu wynosi 2048 bajtów i interwał wysyłania komunikatów wynosi 1 s.

### <a name="default-mode"></a>Tryb domyślny

Klientów, serwerów aplikacji sieci web i usługi Azure SignalR Service są zaangażowane w domyślnym trybie. Każdy klient oznacza jednego połączenia.

#### <a name="echo"></a>Echo

Po pierwsze aplikacji sieci web nawiązania połączenia z usługą Azure SignalR. Po drugie wielu klientów łączenie aplikacji sieci web, która przekierowuje klientów do usługi Azure SignalR Service przy użyciu tokenu dostępu i punktu końcowego. Następnie klienci ustanawiania połączeń protokołu WebSocket przy użyciu usługi Azure SignalR Service.

Po wszystkich klientów nawiązywać połączenia, mogą Rozpocznij wysyłanie wiadomość, która zawiera sygnaturę czasową do określonego koncentratora co sekundę. Centrum echa komunikat, przywracając jego oryginalny klient. Każdy klient oblicza opóźnienia, po odebraniu wiadomości echo ponownie.

Na poniższym diagramie 5 do 8 (ruch wyróżniony czerwonym) znajdują się w pętli. Pętla uruchamia domyślny czas trwania (5 minut) i pobiera statystyki wszystkich komunikatów opóźnienia.

![Ruch dla przypadku użycia echo](./media/signalr-concept-performance/echo.png)

Zachowanie **echo** ustali, że maksymalnej przepustowości dla ruchu przychodzącego jest równa maksymalnej przepustowości dla ruchu wychodzącego. Aby uzyskać szczegółowe informacje Zobacz poniższej tabeli.

|       Echo                        | Unit1 | Unit2 | Unit5 | Unit10 | Unit20 | Unit50 | Unit100 |
|-----------------------------------|-------|-------|-------|--------|--------|--------|---------|
| Połączenia                       | 1000 | 2000 | 5,000 | 10 000 | 20,000 | 50,000 | 100,000 |
| Dla ruchu przychodzącego/wychodzącego komunikatów na sekundę | 1000 | 2000 | 5,000 | 10 000 | 20,000 | 50,000 | 100,000 |
| Przepustowości dla ruchu przychodzącego/wychodzącego | 2 MB/s   | 4 MB/s   | 10 MB/s  | 20 MB/s   | 40 MB/s   | 100 MB/s  | 200 MB/s   |

W tym przypadku użycia każdego komputera klienckiego wywołuje koncentratora zdefiniowanego na serwerze aplikacji. Koncentrator, po prostu wywołuje metody zdefiniowanej w oryginalnym po stronie klienta. To Centrum jest najbardziej lekki Centrum **echo**.

```
        public void Echo(IDictionary<string, object> data)
        {
            Clients.Client(Context.ConnectionId).SendAsync("RecordLatency", data);
        }
```

Nawet w przypadku tego prostego koncentratora ciśnienie ruchu na serwerze aplikacji jest widoczne jako **echo** dla ruchu przychodzącego komunikatu wzrostu obciążenia. To wykorzystania ruchu wymaga wielu serwerów aplikacji w przypadku dużych jednostek SKU. W poniższej tabeli wymieniono liczby serwerów aplikacji, dla każdej warstwy.


|    Echo          | Unit1 | Unit2 | Unit5 | Unit10 | Unit20 | Unit50 | Unit100 |
|------------------|-------|-------|-------|--------|--------|--------|---------|
| Połączenia      | 1000 | 2000 | 5,000 | 10 000 | 20,000 | 50,000 | 100,000 |
| Liczba serwerów aplikacji | 2     | 2     | 2     | 3      | 3      | 10     | 20      |

> [!NOTE]
> Klient połączenia liczbę, rozmiar komunikatu, komunikat wysyłania szybkości, warstwa jednostki SKU i Procesora/pamięci serwera aplikacji mają wpływ na ogólną wydajność **echo**.

#### <a name="broadcast"></a>Emisja

Aby uzyskać **emisji**, gdy aplikacja internetowa odbierze komunikat, emituje do wszystkich klientów. Istnieje więcej klientów są emisji większego ruchu wiadomości na temat wszystkich klientów. Zobacz poniższy diagram.

![Ruch dla danego przypadku użycia emisji](./media/signalr-concept-performance/broadcast.png)

Emituje sygnał niewielkiej liczby klientów. Przepustowości dla ruchu przychodzącego komunikatu jest mały, ale przepustowość ruchu wychodzącego jest bardzo duży. Przepustowość ruchu wychodzącego wiadomości zwiększa się jako połączenie klienta lub zwiększenie współczynnika emisji.

W poniższej tabeli podsumowano, maksymalna liczba połączeń klientów, liczba komunikatów dla ruchu przychodzącego/wychodzącego i przepustowość.

|     Emisja             | Unit1 | Unit2 | Unit5  | Unit10 | Unit20 | Unit50  | Unit100 |
|---------------------------|-------|-------|--------|--------|--------|---------|---------|
| Połączenia               | 1000 | 2000 | 5,000  | 10 000 | 20,000 | 50,000  | 100,000 |
| Komunikaty przychodzące na sekundę  | 2     | 2     | 2      | 2      | 2      | 2       | 2       |
| Wiadomości wychodzących na sekundę | 2000 | 4,000 | 10 000 | 20,000 | 40,000 | 100,000 | 200,000 |
| Przepustowości dla ruchu przychodzącego  | 4 Kb/s   | 4 Kb/s   | 4 Kb/s    | 4 Kb/s    | 4 Kb/s    | 4 Kb/s     | 4 Kb/s     |
| Przepustowość ruchu wychodzącego | 4 MB/s   | 8 MB/s   | 20 MB/s   | 40 MB/s   | 80 MB/s   | 200 MB/s   | 400 MB/s   |

Klienci emisji, które umieszczać komunikaty są nie więcej niż cztery. Potrzebują mniejszej liczby serwerów aplikacji, w porównaniu z **echo** ponieważ kwota wiadomości przychodzących jest mała. Dwa serwery aplikacji są wystarczająco dużo, aby uzyskać informacje o umowie SLA i wydajności. Jednak należy zwiększyć domyślne połączenia z serwerem w celu uniknięcia nierównowagi, szczególnie w przypadku Unit50 i Unit100.

|   Emisja      | Unit1 | Unit2 | Unit5 | Unit10 | Unit20 | Unit50 | Unit100 |
|------------------|-------|-------|-------|--------|--------|--------|---------|
| Połączenia      | 1000 | 2000 | 5,000 | 10 000 | 20,000 | 50,000 | 100,000 |
| Liczba serwerów aplikacji | 2     | 2     | 2     | 2      | 2      | 2      | 2       |

> [!NOTE]
> Zwiększenie domyślnego połączenia serwera z zakresu od 5 do 40 na każdym serwerze aplikacji, aby uniknąć możliwych serwera niezrównoważone połączenia usługi Azure SignalR Service.
>
> Liczba połączeń klienta, rozmiar komunikatu, szybkość wysyłania wiadomości i warstwa jednostki SKU mają wpływ na ogólną wydajność dla **emisji**.

#### <a name="send-to-group"></a>Wyślij do grupy

**Wysyłania do grupy** przypadek użycia ma podobny wzorzec ruchu do **emisji**. Różnica polega na tym, że po klienci ustanawiają połączeń protokołu WebSocket przy użyciu usługi Azure SignalR Service, dołączenie do grupy przed wysłaniem wiadomości do określonej grupy. Na poniższym diagramie przedstawiono przepływ ruchu.

![Ruch dla danego przypadku użycia wysyłania do grupy](./media/signalr-concept-performance/sendtogroup.png)

Element członkowski grupy i liczba grup są dwa czynniki wpływające na wydajność. Aby uprościć analizy, możemy zdefiniować dwa rodzaje grup:

- **Małą grupę**: Każda grupa ma połączenia o szybkości 10. Numer grupy jest równa (liczba połączeń max) / 10. Na przykład dla Unit1, jeśli istnieją 1000 liczby połączeń, mamy jeszcze 1000 / 10 = 100 grupy.

- **Duże grupy**: Numer grupy zawsze wynosi 10. Liczba elementów członkowskich grupy jest równy (liczba połączeń max) / 10. Na przykład dla Unit1, jeśli istnieją 1000 liczby połączeń, następnie każda grupa ma 1000 / 10 = 100 elementów członkowskich.

**Wyślij do grupy** zapewnia routing kosztów do usługi Azure SignalR Service, ponieważ ma ona można znaleźć połączenia docelowej przy użyciu struktury danych rozproszonych. Jak zwiększyć wysyłania połączeń, zwiększa koszt.

##### <a name="small-group"></a>Małą grupę

Routing koszt jest znaczący do wysyłania wiadomości na wiele małych grup. Obecnie wdrożenia usługi Azure SignalR Service osiąga limit koszt routingu w Unit50. Dodawanie więcej czasu Procesora i pamięci nie pomoże, więc nie może poprawić Unit100 dalsze zgodnie z projektem. Jeśli potrzebujesz więcej ruchu przychodzącego przepustowości, należy się z pomocą techniczną.

|   Wyślij do małej grupy     | Unit1 | Unit2 | Unit5  | Unit10 | Unit20 | Unit50 | Unit100 |
|---------------------------|-------|-------|--------|--------|--------|--------|---------|
| Połączenia               | 1000 | 2000 | 5,000  | 10 000 | 20,000 | 50,000 | 100,000
| Liczba elementów członkowskich grupy        | 10    | 10    | 10     | 10     | 10     | 10     | 10 
| Liczba grup               | 100   | 200   | 500    | 1000  | 2000  | 5,000  | 10 000 
| Komunikaty przychodzące na sekundę  | 200   | 400   | 1000  | 2,500  | 4,000  | 7,000  | 7,000   |
| Przepustowości dla ruchu przychodzącego  | 400 KB/s  | 800 KB/s  | 2 MB/s     | 5 MB/s     | 8 MB/s     | 14 MB/s    | 14 MB/s     |
| Wiadomości wychodzących na sekundę | 2000 | 4,000 | 10 000 | 25,000 | 40,000 | 70,000 | 70,000  |
| Przepustowość ruchu wychodzącego | 4 MB/s    | 8 MB/s    | 20 MB/s    | 50 MB/s     | 80 MB/s    | 140 MB/s   | 140 MB/s    |

Wiele połączeń klienckich wywołujesz Centrum, więc numer serwera aplikacji jest również krytyczne znaczenie dla wydajności. W poniższej tabeli wymieniono liczby serwera sugerowanych aplikacji.

|  Wyślij do małej grupy   | Unit1 | Unit2 | Unit5 | Unit10 | Unit20 | Unit50 | Unit100 |
|------------------|-------|-------|-------|--------|--------|--------|---------|
| Połączenia      | 1000 | 2000 | 5,000 | 10 000 | 20,000 | 50,000 | 100,000 |
| Liczba serwerów aplikacji | 2     | 2     | 2     | 3      | 3      | 10     | 20      |

> [!NOTE]
> Klient połączenia liczbę, rozmiar komunikatu, komunikat wysyłania szybkości, routingu kosztów, warstwa jednostki SKU i Procesora/pamięci serwera aplikacji mają wpływ na ogólną wydajność **wysyłać małą grupę**.

##### <a name="big-group"></a>Grupa big Data

Dla **wysyłać do dużych grup**, przepustowość wychodzącą staje się wąskie gardło, zanim osiągnięcia routingu koszt limit. W poniższej tabeli przedstawiono maksymalną przepustowość wychodzącą są prawie takie same jak dla **emisji**.

|    Wyślij do dużych grup      | Unit1 | Unit2 | Unit5  | Unit10 | Unit20 | Unit50  | Unit100 |
|---------------------------|-------|-------|--------|--------|--------|---------|---------|
| Połączenia               | 1000 | 2000 | 5,000  | 10 000 | 20,000 | 50,000  | 100,000
| Liczba elementów członkowskich grupy        | 100   | 200   | 500    | 1000  | 2000  | 5,000   | 10 000 
| Liczba grup               | 10    | 10    | 10     | 10     | 10     | 10      | 10
| Komunikaty przychodzące na sekundę  | 20    | 20    | 20     | 20     | 20     | 20      | 20      |
| Przepustowości dla ruchu przychodzącego  | 80 Kb/s   | 40 Kb/s   | 40 Kb/s    | 20 kb/s    | 40 Kb/s    | 40 Kb/s     | 40 Kb/s     |
| Wiadomości wychodzących na sekundę | 2000 | 4,000 | 10 000 | 20,000 | 40,000 | 100,000 | 200,000 |
| Przepustowość ruchu wychodzącego | 8 MB/s    | 8 MB/s    | 20 MB/s    | 40 MB/s    | 80 MB/s    | 200 MB/s    | 400 MB/s    |

Wysyłanie liczba połączeń jest nie więcej niż 40. Obciążenia na serwerze aplikacji jest mała, więc sugerowane liczba aplikacji sieci web jest mały.

|  Wyślij do dużych grup  | Unit1 | Unit2 | Unit5 | Unit10 | Unit20 | Unit50 | Unit100 |
|------------------|-------|-------|-------|--------|--------|--------|---------|
| Połączenia      | 1000 | 2000 | 5,000 | 10 000 | 20,000 | 50,000 | 100,000 |
| Liczba serwerów aplikacji | 2     | 2     | 2     | 2      | 2      | 2      | 2       |

> [!NOTE]
> Zwiększenie domyślnego połączenia serwera z zakresu od 5 do 40 na każdym serwerze aplikacji, aby uniknąć możliwych serwera niezrównoważone połączenia usługi Azure SignalR Service.
> 
> Liczba połączeń klienta, rozmiar komunikatu, szybkość wysyłania wiadomości, koszt routingu i warstwy SKU mają wpływ na ogólną wydajność **wysyłać do dużych grup**.

#### <a name="send-to-connection"></a>Wyślij do połączenia

W **wysyłać połączenia** przypadek użycia, gdy klienci ustanawiają połączenia z usługi Azure SignalR Service, każdy klient wymaga specjalnych Centrum można pobrać własne identyfikator połączenia. Testów porównawczych wydajności zbiera wszystkie identyfikatory połączeń rozmieszcza je i ponownie przypisuje je do wszystkich klientów jako obiekt docelowy wysyłania. Klienci nadal wysyłania komunikatu do połączenie docelowe, dopóki nie zakończy się test wydajności.

![Ruch dla danego przypadku użycia wysłania do klienta](./media/signalr-concept-performance/sendtoclient.png)

Koszt marszruty **wysyłać połączenia** jest podobny do kosztów dla **wysyłać małą grupę**.

W miarę zwiększania liczby połączeń koszt routingu ogranicza ogólną wydajność. Unit50 osiągnęła limit. W rezultacie Unit100 nie może usprawnić posługiwanie.

Poniższej tabeli znajduje się Podsumowanie statystyczne po wielu rund uruchomiona **wysyłać połączenia** testów porównawczych.

|   Wyślij do połączenia   | Unit1 | Unit2 | Unit5 | Unit10 | Unit20 | Unit50          | Unit100         |
|------------------------------------|-------|-------|-------|--------|--------|-----------------|-----------------|
| Połączenia                        | 1000 | 2000 | 5,000 | 10 000 | 20,000 | 50,000          | 100,000         |
| Dla ruchu przychodzącego/wychodzącego komunikatów na sekundę | 1000 | 2000 | 5,000 | 8000  | 9,000  | 20,000 | 20,000 |
| Przepustowości dla ruchu przychodzącego/wychodzącego | 2 MB/s    | 4 MB/s    | 10 MB/s   | 16 MB/s    | 18 MB/s    | 40 MB/s       | 40 MB/s       |

Ten przypadek użycia wymaga dużym obciążeniem po stronie serwera aplikacji. Zobacz Serwer aplikacji sugerowane count w tabeli poniżej.

|  Wyślij do połączenia  | Unit1 | Unit2 | Unit5 | Unit10 | Unit20 | Unit50 | Unit100 |
|------------------|-------|-------|-------|--------|--------|--------|---------|
| Połączenia      | 1000 | 2000 | 5,000 | 10 000 | 20,000 | 50,000 | 100,000 |
| Liczba serwerów aplikacji | 2     | 2     | 2     | 3      | 3      | 10     | 20      |

> [!NOTE]
> Klient połączenia liczbę, rozmiar komunikatu, komunikat wysyłania szybkości, routingu kosztów, warstwa jednostki SKU i moc procesora CPU i pamięci dla programu server app mają wpływ na ogólną wydajność **wysyłać połączenia**.

#### <a name="aspnet-signalr-echo-broadcast-and-send-to-small-group"></a>Echo biblioteki SignalR platformy ASP.NET, emisji i wysłać do małej grupy

Usługi Azure SignalR Service zawiera samą pojemność wydajności biblioteki SignalR platformy ASP.NET. 

Test wydajności korzysta z usługi Azure Web Apps z [standardowa S3 planu usługi](https://azure.microsoft.com/pricing/details/app-service/windows/) dla biblioteki SignalR platformy ASP.NET.

W poniższej tabeli przedstawiono liczba sugerowane web app for ASP.NET SignalR **echo**.

|   Echo           | Unit1 | Unit2 | Unit5 | Unit10 | Unit20 | Unit50 | Unit100 |
|------------------|-------|-------|-------|--------|--------|--------|---------|
| Połączenia      | 1000 | 2000 | 5,000 | 10 000 | 20,000 | 50,000 | 100,000 |
| Liczba serwerów aplikacji | 2     | 2     | 4     | 4      | 8      | 32      | 40       |

W poniższej tabeli przedstawiono liczba sugerowane web app for ASP.NET SignalR **emisji**.

|  Emisja       | Unit1 | Unit2 | Unit5 | Unit10 | Unit20 | Unit50 | Unit100 |
|------------------|-------|-------|-------|--------|--------|--------|---------|
| Połączenia      | 1000 | 2000 | 5,000 | 10 000 | 20,000 | 50,000 | 100,000 |
| Liczba serwerów aplikacji | 2     | 2     | 2     | 2      | 2      | 2      | 2       |

W poniższej tabeli przedstawiono liczba sugerowane web app for ASP.NET SignalR **wysyłać małą grupę**.

|  Wyślij do małej grupy     | Unit1 | Unit2 | Unit5 | Unit10 | Unit20 | Unit50 | Unit100 |
|------------------|-------|-------|-------|--------|--------|--------|---------|
| Połączenia      | 1000 | 2000 | 5,000 | 10 000 | 20,000 | 50,000 | 100,000 |
| Liczba serwerów aplikacji | 2     | 2     | 4     | 4      | 8      | 32      | 40       |

### <a name="serverless-mode"></a>Tryb bez użycia serwera

Klienci i usługi Azure SignalR Service są zaangażowane w trybie bez użycia serwera. Każdy klient oznacza jednego połączenia. Klient wysyła komunikaty za pośrednictwem interfejsu API REST do innego klienta lub odpowiedniej do emisji komunikatów dla wszystkich.

Wysyłanie komunikatów o wysokiej gęstości za pośrednictwem interfejsu API REST nie jest tak wydajna, jak za pomocą protokołu WebSocket. Tworzenie nowego połączenia HTTP zawsze wymaga, a to dodatkowych opłat w trybie bez użycia serwera.

#### <a name="broadcast-through-rest-api"></a>Emisja za pośrednictwem interfejsu API REST
Wszyscy klienci ustanawiania połączeń protokołu WebSocket przy użyciu usługi Azure SignalR Service. Niektórzy klienci zacznij emisji za pośrednictwem interfejsu API REST. Wysyłanie (ruch przychodzący) komunikatów jest wspomaga Post protokołu HTTP, który nie jest efektywne w porównaniu z protokołu WebSocket.

|   Emisja za pośrednictwem interfejsu API REST     | Unit1 | Unit2 | Unit5  | Unit10 | Unit20 | Unit50  | Unit100 |
|---------------------------|-------|-------|--------|--------|--------|---------|---------|
| Połączenia               | 1000 | 2000 | 5,000  | 10 000 | 20,000 | 50,000  | 100,000 |
| Komunikaty przychodzące na sekundę  | 2     | 2     | 2      | 2      | 2      | 2       | 2       |
| Wiadomości wychodzących na sekundę | 2000 | 4,000 | 10 000 | 20,000 | 40,000 | 100,000 | 200,000 |
| Przepustowości dla ruchu przychodzącego  | 4 Kb/s    | 4 Kb/s    | 4 Kb/s     | 4 Kb/s     | 4 Kb/s     | 4 Kb/s      | 4 Kb/s      |
| Przepustowość ruchu wychodzącego | 4 MB/s    | 8 MB/s    | 20 MB/s    | 40 MB/s    | 80 MB/s    | 200 MB/s    | 400 MB/s    |

#### <a name="send-to-user-through-rest-api"></a>Wysłać do użytkownika za pośrednictwem interfejsu API REST
Testu porównawczego nazwy użytkowników są przypisywane do wszystkich klientów, przed rozpoczęciem nawiązywania połączenia z usługi Azure SignalR Service. Po klientów ustanawiania połączeń protokołu WebSocket przy użyciu usługi Azure SignalR Service, uruchamiania wysyłania wiadomości do innych użytkowników za pomocą żądania HTTP Post.

|   Wysłać do użytkownika za pośrednictwem interfejsu API REST | Unit1 | Unit2 | Unit5  | Unit10 | Unit20 | Unit50  | Unit100 |
|---------------------------|-------|-------|--------|--------|--------|---------|---------|
| Połączenia               | 1000 | 2000 | 5,000  | 10 000 | 20,000 | 50,000  | 100,000 |
| Komunikaty przychodzące na sekundę  | 300   | 600   | 900    | 1,300  | 2000  | 10 000  | 18,000  |
| Wiadomości wychodzących na sekundę | 300   | 600   | 900    | 1,300  | 2000  | 10 000  | 18,000 |
| Przepustowości dla ruchu przychodzącego  | 600 KB/s  | 1.2 MB/s  | 1.8 MB/s   | 2.6 MB/s   | 4 MB/s     | 10 MB/s     | 36 MB/s    |
| Przepustowość ruchu wychodzącego | 600 KB/s  | 1.2 MB/s  | 1.8 MB/s   | 2.6 MB/s   | 4 MB/s     | 10 MB/s     | 36 MB/s    |

## <a name="performance-test-environments"></a>Wydajność środowiska testowe

Dla wszystkich zastosowań wymienionych wcześniej, firma Microsoft prowadzone testów wydajnościowych — wszystko w środowisku platformy Azure. Co najwyżej użyliśmy 50 VMs klienta i serwera aplikacji 20 maszyn wirtualnych. Poniżej przedstawiono niektóre szczegóły:

- Rozmiar maszyny Wirtualnej klienta: StandardDS2V2 (2 procesory vCPU, pamięci 7G)

- Serwer aplikacji rozmiar maszyny Wirtualnej: StandardF4sV2 (4 vCPU, pamięci 8 K)

- Azure SignalR SDK połączeń serwera: 15

## <a name="performance-tools"></a>Narzędzia wydajności

Narzędzia do oceny wydajności dla usługi Azure SignalR Service można znaleźć na [GitHub](https://github.com/Azure/azure-signalr-bench/tree/master/SignalRServiceBenchmarkPlugin).

## <a name="next-steps"></a>Kolejne kroki

W tym artykule stało się z omówieniem usługi Azure SignalR Service wydajności w typowych scenariuszach przypadków użycia.

Aby uzyskać szczegółowe informacje na temat podstawy usługi i skalowania dla niego, przeczytaj następujące przewodniki:

* [Wewnętrzne elementy usługi Azure SignalR Service](signalr-concept-internals.md)
* [Usługi Azure SignalR Service skalowania](signalr-howto-scale-multi-instances.md)
