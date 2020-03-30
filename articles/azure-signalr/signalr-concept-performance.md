---
title: Przewodnik dotyczący wydajności usługi Azure SignalR Service
description: Omówienie wydajności i testu porównawczego usługi Azure SignalR. Kluczowe metryki, które należy wziąć pod uwagę podczas planowania pojemności.
author: sffamily
ms.service: signalr
ms.topic: conceptual
ms.date: 11/13/2019
ms.author: zhshang
ms.openlocfilehash: 68cad32be177fa20794399157fca89e87c2f8f59
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74157669"
---
# <a name="performance-guide-for-azure-signalr-service"></a>Przewodnik dotyczący wydajności usługi Azure SignalR Service

Jedną z kluczowych zalet korzystania z usługi Azure SignalR service jest łatwość skalowania aplikacji SignalR. W scenariuszu na dużą skalę wydajność jest ważnym czynnikiem. 

W tym przewodniku przedstawimy czynniki wpływające na wydajność aplikacji SignalR. Opiszemy typową wydajność w różnych scenariuszach przypadków użycia. Na koniec wprowadzimy środowisko i narzędzia, których można użyć do wygenerowania raportu wydajności.

## <a name="term-definitions"></a>Definicje terminów

*Przychodzące:* przychodząca wiadomość do usługi Azure SignalR.

*Wychodzące:* komunikat wychodzący z usługi Azure SignalR.

*Przepustowość:* Całkowity rozmiar wszystkich wiadomości w ciągu 1 sekundy.

*Tryb domyślny:* Domyślny tryb pracy podczas tworzenia wystąpienia usługi Azure SignalR. Usługa Azure SignalR oczekuje, że serwer aplikacji nawiązuje połączenie z nim, zanim zaakceptuje połączenia klienta.

*Tryb bezserwerowy:* tryb, w którym usługa Azure SignalR service akceptuje tylko połączenia klientów. Połączenie z serwerem nie jest dozwolone.

## <a name="overview"></a>Omówienie

Usługa Azure SignalR service definiuje siedem warstw standardowych dla różnych pojemności wydajności. W tym przewodniku odpowiedzieć na następujące pytania:

-   Jaka jest typowa wydajność usługi Azure SignalR dla każdej warstwy?

-   Czy usługa Azure SignalR spełnia moje wymagania dotyczące przepływności wiadomości (na przykład wysyłanie 100 000 wiadomości na sekundę)?

-   Dla mojego konkretnego scenariusza, która warstwa jest odpowiednia dla mnie? Albo jak mogę wybrać odpowiednią warstwę?

-   Jaki rodzaj serwera aplikacji (rozmiar maszyny wirtualnej) jest odpowiedni dla mnie? Ile z nich należy wdrożyć?

Aby odpowiedzieć na te pytania, ten przewodnik zawiera najpierw wyjaśnienie wysokiego poziomu czynników, które wpływają na wydajność. Następnie ilustruje maksymalną liczbę wiadomości przychodzących i wychodzących dla każdej warstwy dla typowych przypadków użycia: **echo,** **emisja,** **wysyłanie do grupy**i wysyłanie do **połączenia** (czat peer-to-peer).

Ten przewodnik nie obejmuje wszystkich scenariuszy (i różnych przypadków użycia, rozmiarów wiadomości, wzorców wysyłania wiadomości i tak dalej). Ale zapewnia kilka metod, które pomogą Ci:

- Oceń swoje przybliżone wymagania dotyczące wiadomości przychodzących lub wychodzących.
- Znajdź odpowiednie warstwy, sprawdzając tabelę wydajności.

## <a name="performance-insight"></a>Wgląd w wydajność

W tej sekcji opisano metodologie oceny wydajności, a następnie wymieniono wszystkie czynniki wpływające na wydajność. W końcu zawiera metody ułatwiające ocenę wymagań dotyczących wydajności.

### <a name="methodology"></a>Metodologia

*Przepływność* i *opóźnienie* są dwa typowe aspekty sprawdzania wydajności. W przypadku usługi Azure SignalR każda warstwa jednostki SKU ma własne zasady ograniczania przepływności. Zasady definiuje *maksymalną dozwoloną przepływność (przepustowość przychodzącą i wychodzącą)* jako maksymalną osiągniętą przepływność, gdy 99 procent wiadomości ma opóźnienie mniejsze niż 1 sekundę.

Opóźnienie to przedział czasu od połączenia wysyłającego wiadomość do odebrania komunikatu odpowiedzi z usługi Azure SignalR. Weźmy **echo** jako przykład. Każde połączenie klienta dodaje sygnaturę czasową w wiadomości. Centrum serwera aplikacji wysyła oryginalną wiadomość z powrotem do klienta. Tak więc opóźnienie propagacji jest łatwo obliczane przez każde połączenie klienta. Sygnatura czasowa jest dołączona dla każdej wiadomości w **emisji,** **wyślij do grupy**i wyślij do **połączenia**.

Aby symulować tysiące równoczesnych połączeń klientów, wiele maszyn wirtualnych są tworzone w wirtualnej sieci prywatnej na platformie Azure. Wszystkie te maszyny wirtualne łączą się z tym samym wystąpieniem usługi Azure SignalR Service.

W trybie domyślnym usługi Azure SignalR maszyny wirtualne serwera aplikacji są wdrażane w tej samej wirtualnej sieci prywatnej co maszyny wirtualne klienta. Wszystkie maszyny wirtualne klienta i maszyny wirtualne serwera aplikacji są wdrażane w tej samej sieci tego samego regionu, aby uniknąć opóźnienia między regionami.

### <a name="performance-factors"></a>Czynniki wydajności

Teoretycznie pojemność usługi Azure SignalR service jest ograniczona przez zasoby obliczeniowe: procesor, pamięć i sieć. Na przykład więcej połączeń z usługą Azure SignalR service spowodować, że usługa użyć więcej pamięci. W przypadku większego ruchu komunikatów (na przykład każda wiadomość jest większa niż 2048 bajtów), usługa Azure SignalR musi wydać więcej cykli procesora CPU na przetwarzanie ruchu. Tymczasem przepustowość sieci platformy Azure nakłada również limit maksymalnego ruchu.

Typ transportu jest kolejnym czynnikiem wpływającym na wydajność. Trzy typy to [WebSocket](https://en.wikipedia.org/wiki/WebSocket), [Server-Sent-Event](https://en.wikipedia.org/wiki/Server-sent_events)i [Long-Polling](https://en.wikipedia.org/wiki/Push_technology). 

WebSocket jest dwukierunkowym i pełnodupleksowym protokołem komunikacyjnym za pośrednictwem jednego połączenia TCP. Server-Sent-Event jest jednokierunkowym protokołem do wypychania wiadomości z serwera do klienta. Long-Polling wymaga od klientów okresowego sondowania informacji z serwera za pośrednictwem żądania HTTP. Dla tego samego interfejsu API w tych samych warunkach WebSocket ma najlepszą wydajność, Server-Sent-Event jest wolniejszy, a Long-Polling jest najwolniejszy. Usługa Azure SignalR domyślnie zaleca WebSocket.

Koszt routingu wiadomości ogranicza również wydajność. Usługa Azure SignalR odgrywa rolę jako router wiadomości, który kieruje wiadomość z zestawu klientów lub serwerów do innych klientów lub serwerów. Inny scenariusz lub interfejs API wymaga innej zasady routingu. 

W przypadku **echo**klient wysyła wiadomość do siebie, a miejsce docelowe routingu jest również samo. Ten wzorzec ma najniższy koszt routingu. Ale dla **emisji,** **wyślij do grupy**i **wysłać do połączenia,** usługa Azure SignalR musi wyszukać połączenia docelowe za pośrednictwem wewnętrznej struktury danych rozproszonych. To dodatkowe przetwarzanie zużywa więcej procesora, pamięci i przepustowości sieci. W rezultacie wydajność jest wolniejsza.

W trybie domyślnym serwer aplikacji może również stać się wąskim gardłem dla niektórych scenariuszy. Zestaw SDK sygnału azure musi wywołać koncentrator, podczas gdy utrzymuje połączenie na żywo z każdym klientem za pośrednictwem sygnałów pulsu.

W trybie bezserwerowym klient wysyła wiadomość przez wpis HTTP, który nie jest tak wydajny jak WebSocket.

Innym czynnikiem jest protokół: JSON i [MessagePack](https://msgpack.org/index.html). MessagePack jest mniejszy i dostarczany szybciej niż JSON. MessagePack może jednak nie poprawić wydajności. Wydajność usługi Azure SignalR nie jest wrażliwa na protokoły, ponieważ nie dekoduje ładunku wiadomości podczas przekazywania wiadomości z klientów na serwery lub odwrotnie.

Podsumowując, następujące czynniki wpływają na pojemność przychodzącą i wychodzącą:

-   Warstwa SKU (procesor/pamięć)

-   Liczba połączeń

-   Rozmiar komunikatu

-   Szybkość wysyłania wiadomości

-   Typ transportu (WebSocket, Server-Sent-Event lub Long-Polling)

-   Scenariusz przypadku użycia (koszt marszruty)

-   Połączenia serwera aplikacji i usługi (w trybie serwera)


### <a name="finding-a-proper-sku"></a>Znajdowanie właściwej jednostki SKU

Jak można ocenić pojemność przychodzącą/wychodzącą lub znaleźć warstwę odpowiednią dla określonego przypadku użycia?

Załóżmy, że serwer aplikacji jest wystarczająco wydajny i nie jest wąskim gardłem wydajności. Następnie sprawdź maksymalną przepustowość przychodzącą i wychodzącą dla każdej warstwy.

#### <a name="quick-evaluation"></a>Szybka ocena

Uprośćmy najpierw ocenę, zakładając pewne ustawienia domyślne: 

- Typ transportu jest WebSocket.
- Rozmiar wiadomości wynosi 2048 bajtów.
- Wiadomość jest wysyłana co 1 sekundę.
- Usługa Azure SignalR jest w trybie domyślnym.

Każda warstwa ma własną maksymalną przepustowość przychodzącą i przepustowość wychodzącą. Płynne środowisko użytkownika nie jest gwarantowane po przekroczeniu limitu połączenia przychodzącego lub wychodzącego.

**Echo** zapewnia maksymalną przepustowość przychodzącą, ponieważ ma najniższy koszt routingu. **Emisja** definiuje maksymalną przepustowość wiadomości wychodzących.

*Nie* należy przekraczać wyróżnionych wartości w następujących dwóch tabelach.

|       Echo                        | Jednostka 1 | Jednostka 2 | Jednostka 5 | Jednostka10 | Jednostka 20 | Jednostka50 | Jednostka100 |
|-----------------------------------|-------|-------|-------|--------|--------|--------|---------|
| Połączenia                       | 1000 | 2000 | 5000 | 10 000 | 20 000 | 50 000 | 100 000 |
| **Przepustowość przychodząca** | **2 MB/s**    | **4 MB/s**    | **10 MB/s**   | **20 mb/s**    | **40 mb/s**    | **100 mb/s**   | **200 mb/s**    |
| Przepustowość wychodząca | 2 MB/s   | 4 MB/s   | 10 MB/s  | 20 mb/s   | 40 mb/s   | 100 mb/s  | 200 mb/s   |


|     Emisja             | Jednostka 1 | Jednostka 2 | Jednostka 5  | Jednostka10 | Jednostka 20 | Jednostka50  | Jednostka100 |
|---------------------------|-------|-------|--------|--------|--------|---------|---------|
| Połączenia               | 1000 | 2000 | 5000  | 10 000 | 20 000 | 50 000  | 100 000 |
| Przepustowość przychodząca  | 4 kb/s   | 4 kb/s   | 4 kb/s    | 4 kb/s    | 4 kb/s    | 4 kb/s     | 4 kb/s    |
| **Przepustowość wychodząca** | **4 MB/s**    | **8 MB/s**    | **20 mb/s**    | **40 mb/s**    | **80 mb/s**    | **200 mb/s**    | **400 mb/s**   |

*Przepustowość przychodząca* i *przepustowość wychodząca* to całkowity rozmiar wiadomości na sekundę.  Oto formuły dla nich:
```
  inboundBandwidth = inboundConnections * messageSize / sendInterval
  outboundBandwidth = outboundConnections * messageSize / sendInterval
```

- *połączenia przychodzące*: Liczba połączeń wysyłających wiadomość.

- *połączenia wychodzące*: Liczba połączeń odbierających wiadomość.

- *messageSize*: Rozmiar pojedynczej wiadomości (średnia wartość). Mała wiadomość, która jest mniejsza niż 1024 bajtów ma wpływ na wydajność, który jest podobny do 1024-bajtowy komunikat.

- *sendInterval*: Czas wysłania jednej wiadomości. Zazwyczaj jest to 1 sekunda na wiadomość, co oznacza wysyłanie jednej wiadomości co sekundę. Mniejszy interwał oznacza wysyłanie większej ilości wiadomości w określonym czasie. Na przykład 0,5 sekundy na wiadomość oznacza wysyłanie dwóch wiadomości co sekundę.

- *Połączenia:* zatwierdzony maksymalny próg dla usługi Azure SignalR dla każdej warstwy. Jeśli numer połączenia zostanie zwiększona dalej, będzie cierpieć z powodu ograniczania połączeń.

#### <a name="evaluation-for-complex-use-cases"></a>Ocena złożonych przypadków użycia

##### <a name="bigger-message-size-or-different-sending-rate"></a>Większy rozmiar wiadomości lub inna szybkość wysyłania

Rzeczywisty przypadek użycia jest bardziej skomplikowany. Może wysłać wiadomość większą niż 2048 bajtów lub szybkość wysyłania wiadomości nie jest jedną wiadomością na sekundę. Weźmy transmisję Unit100 jako przykład, aby dowiedzieć się, jak ocenić jego wydajność.

W poniższej tabeli przedstawiono rzeczywisty przypadek użycia **emisji**. Ale rozmiar wiadomości, liczba połączeń i szybkość wysyłania wiadomości różnią się od zakładanych w poprzedniej sekcji. Pytanie brzmi, w jaki sposób możemy wywniczyć dowolny z tych elementów (rozmiar wiadomości, liczba połączeń lub szybkość wysyłania wiadomości), jeśli znamy tylko dwa z nich.

| Emisja  | Rozmiar komunikatu | Wiadomości przychodzące na sekundę | Połączenia | Interwały wysyłania |
|---|---------------------|--------------------------|-------------|-------------------------|
| 1 | 20 KB                | 1                        | 100 000     | 5 sek.                      |
| 2 | 256 KB               | 1                        | 8000       | 5 sek.                      |

Poniższa formuła jest łatwa do wywnioskowania na podstawie poprzedniej formuły:

```
outboundConnections = outboundBandwidth * sendInterval / messageSize
```

W przypadku urządzenia Unit100 maksymalna przepustowość wychodząca wynosi 400 MB w stosunku do poprzedniej tabeli. W przypadku rozmiaru wiadomości o rozmiarze 20 KB maksymalna liczba \* połączeń wychodzących powinna wynosić 400 MB 5 / 20 KB = 100 000, co odpowiada wartości rzeczywistej.

##### <a name="mixed-use-cases"></a>Mieszane przypadki użycia

Rzeczywisty przypadek użycia zazwyczaj łączy cztery podstawowe przypadki użycia: **echo,** **emisja,** **wyślij do grupy**i wyślij do **połączenia.** Metodologia używana do oceny zdolności produkcyjnych polega na:

1. Podziel mieszane przypadki użycia na cztery podstawowe przypadki użycia.
1. Oblicz maksymalną przepustowość wiadomości przychodzącej i wychodzącej przy użyciu poprzednich formuł oddzielnie.
1. Zsumuj obliczenia przepustowości, aby uzyskać całkowitą maksymalną przepustowość przychodzącą/wychodzącą. 

Następnie wybierz odpowiednią warstwę z tabel maksymalnej przepustowości przychodzącej/wychodzącej.

> [!NOTE]
> W przypadku wysyłania wiadomości do setek lub tysięcy małych grup lub dla tysięcy klientów wysyłających wiadomość do siebie, koszt routingu stanie się dominujący. Weźmy pod uwagę ten wpływ.

W przypadku użycia wysyłania wiadomości do klientów upewnij się, że serwer aplikacji *nie* jest wąskim gardłem. Poniższa sekcja "Studium przypadku" zawiera wskazówki dotyczące liczby serwerów aplikacji, których potrzebujesz i liczby połączeń z serwerem, które należy skonfigurować.

## <a name="case-study"></a>Analiza przypadku

W poniższych sekcjach przedstawiono cztery typowe przypadki użycia transportu WebSocket: **echo,** **emisja,** **wysyłanie do grupy**i wysyłanie do **połączenia.** Dla każdego scenariusza sekcja zawiera listę bieżących przychodzących i wychodzących pojemności usługi Azure SignalR. Wyjaśnia również główne czynniki, które wpływają na wydajność.

W trybie domyślnym serwer aplikacji tworzy pięć połączeń serwera z usługą Azure SignalR Service. Serwer aplikacji domyślnie używa zestawu SDK usługi Azure SignalR. W następujących wynikach testu wydajności połączenia z serwerem są zwiększane do 15 (lub więcej w przypadku nadawania i wysyłania wiadomości do dużej grupy).

Różne przypadki użycia mają różne wymagania dotyczące serwerów aplikacji. **Emisja** wymaga niewielkiej liczby serwerów aplikacji. **Echo** lub **wyślij do połączenia** potrzebuje wielu serwerów aplikacji.

We wszystkich przypadkach użycia domyślny rozmiar wiadomości wynosi 2048 bajtów, a interwał wysyłania wiadomości wynosi 1 sekundę.

### <a name="default-mode"></a>Tryb domyślny

Klienci, serwery aplikacji sieci web i usługa Azure SignalR service są zaangażowane w trybie domyślnym. Każdy klient oznacza jedno połączenie.

#### <a name="echo"></a>Echo

Najpierw aplikacja internetowa łączy się z usługą Azure SignalR Service. Po drugie wielu klientów łączy się z aplikacją sieci web, która przekierowuje klientów do usługi Azure SignalR z tokenem dostępu i punktem końcowym. Następnie klienci ustanawiają połączenia WebSocket z usługą Azure SignalR Service.

Po wszystkich klientów ustanowić połączenia, zaczynają wysyłać wiadomość, która zawiera sygnaturę czasową do określonego koncentratora co sekundę. Centrum powtarza wiadomość z powrotem do oryginalnego klienta. Każdy klient oblicza opóźnienie po odebraniu komunikatu echa z powrotem.

Na poniższym diagramie od 5 do 8 (czerwony podświetlony ruch) znajduje się w pętli. Pętla jest uruchamiana przez domyślny czas trwania (5 minut) i pobiera statystykę wszystkich opóźnień wiadomości.

![Ruch dla przypadku użycia echa](./media/signalr-concept-performance/echo.png)

Zachowanie **echa** określa, że maksymalna przepustowość przychodząca jest równa maksymalnej przepustowości wychodzącej. Aby uzyskać szczegółowe informacje, zapoznaj się z poniższą tabelą.

|       Echo                        | Jednostka 1 | Jednostka 2 | Jednostka 5 | Jednostka10 | Jednostka 20 | Jednostka50 | Jednostka100 |
|-----------------------------------|-------|-------|-------|--------|--------|--------|---------|
| Połączenia                       | 1000 | 2000 | 5000 | 10 000 | 20 000 | 50 000 | 100 000 |
| Komunikaty przychodzące/wychodzące na sekundę | 1000 | 2000 | 5000 | 10 000 | 20 000 | 50 000 | 100 000 |
| Przepustowość przychodząca/wychodząca | 2 MB/s   | 4 MB/s   | 10 MB/s  | 20 mb/s   | 40 mb/s   | 100 mb/s  | 200 mb/s   |

W tym przypadku użycia każdy klient wywołuje koncentratora zdefiniowane na serwerze aplikacji. Koncentrator po prostu wywołuje metodę zdefiniowaną w oryginalnej stronie klienta. Ten koncentrator jest najładniejszym **koncentratorem echo.**

```
        public void Echo(IDictionary<string, object> data)
        {
            Clients.Client(Context.ConnectionId).SendAsync("RecordLatency", data);
        }
```

Nawet w przypadku tego prostego koncentratora presja ruchu na serwerze aplikacji jest widoczna wraz ze wzrostem obciążenia przychodzącego **echa.** Ta presja ruchu wymaga wielu serwerów aplikacji dla dużych warstw jednostek SKU. W poniższej tabeli wymieniono liczbę serwerów aplikacji dla każdej warstwy.


|    Echo          | Jednostka 1 | Jednostka 2 | Jednostka 5 | Jednostka10 | Jednostka 20 | Jednostka50 | Jednostka100 |
|------------------|-------|-------|-------|--------|--------|--------|---------|
| Połączenia      | 1000 | 2000 | 5000 | 10 000 | 20 000 | 50 000 | 100 000 |
| Liczba serwerów aplikacji | 2     | 2     | 2     | 3      | 3      | 10     | 20      |

> [!NOTE]
> Numer połączenia klienta, rozmiar wiadomości, szybkość wysyłania wiadomości, warstwa SKU i procesor/pamięć serwera aplikacji wpływają na ogólną wydajność **programu Echo**.

#### <a name="broadcast"></a>Emisja

W przypadku **emisji**, gdy aplikacja internetowa odbiera wiadomość, emituje do wszystkich klientów. Im więcej klientów jest do emisji, tym większy jest ruch wiadomości dla wszystkich klientów. Zapoznaj się z poniższym diagramem.

![Ruch dla przypadku użycia emisji](./media/signalr-concept-performance/broadcast.png)

Niewielka liczba klientów nadawanie. Przepustowość wiadomości przychodzącej jest mała, ale przepustowość wychodząca jest ogromna. Przepustowość komunikatu wychodzącego zwiększa się wraz ze wzrostem szybkości połączenia klienta lub emisji.

W poniższej tabeli podsumowano maksymalną liczbę połączeń klientów, liczbę komunikatów przychodzących/wychodzących oraz przepustowość.

|     Emisja             | Jednostka 1 | Jednostka 2 | Jednostka 5  | Jednostka10 | Jednostka 20 | Jednostka50  | Jednostka100 |
|---------------------------|-------|-------|--------|--------|--------|---------|---------|
| Połączenia               | 1000 | 2000 | 5000  | 10 000 | 20 000 | 50 000  | 100 000 |
| Wiadomości przychodzące na sekundę  | 2     | 2     | 2      | 2      | 2      | 2       | 2       |
| Wiadomości wychodzące na sekundę | 2000 | 4000 | 10 000 | 20 000 | 40 000 | 100 000 | 200,000 |
| Przepustowość przychodząca  | 4 kb/s   | 4 kb/s   | 4 kb/s    | 4 kb/s    | 4 kb/s    | 4 kb/s     | 4 kb/s     |
| Przepustowość wychodząca | 4 MB/s   | 8 MB/s   | 20 mb/s   | 40 mb/s   | 80 mb/s   | 200 mb/s   | 400 mb/s   |

Klientów nadawców, które publikują wiadomości są nie więcej niż cztery. Potrzebują mniej serwerów aplikacji w porównaniu z **echo,** ponieważ kwota wiadomości przychodzącej jest mała. Dwa serwery aplikacji wystarczą zarówno do umowy SLA, jak i do uwzględnienia wydajności. Należy jednak zwiększyć domyślne połączenia z serwerem, aby uniknąć braku równowagi, szczególnie w przypadku jednostek Unit50 i Unit100.

|   Emisja      | Jednostka 1 | Jednostka 2 | Jednostka 5 | Jednostka10 | Jednostka 20 | Jednostka50 | Jednostka100 |
|------------------|-------|-------|-------|--------|--------|--------|---------|
| Połączenia      | 1000 | 2000 | 5000 | 10 000 | 20 000 | 50 000 | 100 000 |
| Liczba serwerów aplikacji | 2     | 2     | 2     | 2      | 2      | 2      | 2       |

> [!NOTE]
> Zwiększ domyślne połączenia serwera z 5 do 40 na każdym serwerze aplikacji, aby uniknąć ewentualnych niesymetrykowych połączeń serwera z usługą Azure SignalR Service.
>
> Numer połączenia klienta, rozmiar wiadomości, szybkość wysyłania wiadomości i warstwa SKU wpływają na ogólną wydajność **emisji**.

#### <a name="send-to-group"></a>Wyślij do grupy

Przypadek użycia **wyślij do grupy** ma podobny wzorzec ruchu do **emisji**. Różnica polega na tym, że po tym, jak klienci nawiązują połączenia WebSocket z usługą Azure SignalR Service, muszą dołączyć do grup, zanim będą mogli wysłać wiadomość do określonej grupy. Na poniższym diagramie przedstawiono przepływ ruchu.

![Ruch w przypadku użycia typu "wyślij do grupy"](./media/signalr-concept-performance/sendtogroup.png)

Liczba członków grupy i grupy to dwa czynniki wpływające na wydajność. Aby uprościć analizę, definiujemy dwa rodzaje grup:

- **Mała grupa**: Każda grupa ma 10 połączeń. Numer grupy jest równy (maksymalna liczba połączeń) / 10. Na przykład dla Unit1, jeśli istnieje 1000 liczby połączeń, to mamy 1000 / 10 = 100 grup.

- **Duża grupa**: Numer grupy jest zawsze 10. Liczba członków grupy jest równa (maksymalna liczba połączeń) / 10. Na przykład dla Unit1, jeśli istnieje 1000 liczby połączeń, a następnie każda grupa ma 1000 / 10 = 100 członków.

**Wyślij do grupy** przynosi koszt routingu do usługi Azure SignalR, ponieważ musi znaleźć połączenia docelowe za pośrednictwem struktury danych rozproszonych. Wraz ze wzrostem liczby połączeń wysyłających koszty rosną.

##### <a name="small-group"></a>Mała grupa

Koszt routingu jest znaczny w przypadku wysyłania wiadomości do wielu małych grup. Obecnie implementacja usługi Azure SignalR trafia limit kosztów routingu w jednostce Unit50. Dodanie większej ilości procesora i pamięci nie pomaga, więc unit100 nie może poprawić się dalej zgodnie z projektem. Jeśli potrzebujesz większej przepustowości przychodzącej, skontaktuj się z działem obsługi klienta.

|   Wyślij do małej grupy     | Jednostka 1 | Jednostka 2 | Jednostka 5  | Jednostka10 | Jednostka 20 | Jednostka50 | Jednostka100 |
|---------------------------|-------|-------|--------|--------|--------|--------|---------|
| Połączenia               | 1000 | 2000 | 5000  | 10 000 | 20 000 | 50 000 | 100 000
| Liczba członków grupy        | 10    | 10    | 10     | 10     | 10     | 10     | 10 
| Liczba grup               | 100   | 200   | 500    | 1000  | 2000  | 5000  | 10 000 
| Wiadomości przychodzące na sekundę  | 200   | 400   | 1000  | 2500  | 4000  | 7,000  | 7,000   |
| Przepustowość przychodząca  | 400 kb/s  | 800 kb/s  | 2 MB/s     | 5 MB/s     | 8 MB/s     | 14 mb/s    | 14 mb/s     |
| Wiadomości wychodzące na sekundę | 2000 | 4000 | 10 000 | 25 000 | 40 000 | 70,000 | 70,000  |
| Przepustowość wychodząca | 4 MB/s    | 8 MB/s    | 20 mb/s    | 50 mb/s     | 80 mb/s    | 140 mb/s   | 140 mb/s    |

Wiele połączeń klientów wywołuje koncentrator, więc numer serwera aplikacji jest również krytyczny dla wydajności. W poniższej tabeli wymieniono sugerowane liczby serwerów aplikacji.

|  Wyślij do małej grupy   | Jednostka 1 | Jednostka 2 | Jednostka 5 | Jednostka10 | Jednostka 20 | Jednostka50 | Jednostka100 |
|------------------|-------|-------|-------|--------|--------|--------|---------|
| Połączenia      | 1000 | 2000 | 5000 | 10 000 | 20 000 | 50 000 | 100 000 |
| Liczba serwerów aplikacji | 2     | 2     | 2     | 3      | 3      | 10     | 20      |

> [!NOTE]
> Numer połączenia klienta, rozmiar wiadomości, szybkość wysyłania wiadomości, koszt routingu, warstwa jednostek SKU i procesora CPU/pamięci serwera aplikacji wpływają na ogólną wydajność **wysyłania do małej grupy**.

##### <a name="big-group"></a>Duża grupa

W przypadku **wysyłania do dużej grupy**przepustowość wychodząca staje się wąskim gardłem przed osiągnięciem limitu kosztów routingu. W poniższej tabeli wymieniono maksymalną przepustowość wychodzącą, która jest prawie taka sama jak w przypadku **emisji**.

|    Wyślij do dużej grupy      | Jednostka 1 | Jednostka 2 | Jednostka 5  | Jednostka10 | Jednostka 20 | Jednostka50  | Jednostka100 |
|---------------------------|-------|-------|--------|--------|--------|---------|---------|
| Połączenia               | 1000 | 2000 | 5000  | 10 000 | 20 000 | 50 000  | 100 000
| Liczba członków grupy        | 100   | 200   | 500    | 1000  | 2000  | 5000   | 10 000 
| Liczba grup               | 10    | 10    | 10     | 10     | 10     | 10      | 10
| Wiadomości przychodzące na sekundę  | 20    | 20    | 20     | 20     | 20     | 20      | 20      |
| Przepustowość przychodząca  | 80 kb/s   | 40 kb/s   | 40 kb/s    | 20 kb/s    | 40 kb/s    | 40 kb/s     | 40 kb/s     |
| Wiadomości wychodzące na sekundę | 2000 | 4000 | 10 000 | 20 000 | 40 000 | 100 000 | 200,000 |
| Przepustowość wychodząca | 8 MB/s    | 8 MB/s    | 20 mb/s    | 40 mb/s    | 80 mb/s    | 200 mb/s    | 400 mb/s    |

Liczba połączeń wysyłających wynosi nie więcej niż 40. Obciążenie serwera aplikacji jest niewielkie, więc sugerowana liczba aplikacji internetowych jest niewielka.

|  Wyślij do dużej grupy  | Jednostka 1 | Jednostka 2 | Jednostka 5 | Jednostka10 | Jednostka 20 | Jednostka50 | Jednostka100 |
|------------------|-------|-------|-------|--------|--------|--------|---------|
| Połączenia      | 1000 | 2000 | 5000 | 10 000 | 20 000 | 50 000 | 100 000 |
| Liczba serwerów aplikacji | 2     | 2     | 2     | 2      | 2      | 2      | 2       |

> [!NOTE]
> Zwiększ domyślne połączenia serwera z 5 do 40 na każdym serwerze aplikacji, aby uniknąć ewentualnych niesymetrykowych połączeń serwera z usługą Azure SignalR Service.
> 
> Numer połączenia klienta, rozmiar wiadomości, szybkość wysyłania wiadomości, koszt routingu i warstwa jednostek SKU wpływają na ogólną wydajność **wysyłania do grupy dużych**.

#### <a name="send-to-connection"></a>Wyślij do połączenia

W przypadku użycia **połączenia wyślij do połączenia,** gdy klienci ustanawiają połączenia z usługą Azure SignalR, każdy klient wywołuje specjalne centrum, aby uzyskać własny identyfikator połączenia. Wskaźnik wydajności zbiera wszystkie identyfikatory połączeń, tasuje je i ponownie przydziela je do wszystkich klientów jako miejsce docelowe wysyłania. Klienci zachować wysyłanie wiadomości do połączenia docelowego, dopóki nie zakończy się test wydajności.

![Ruch w przypadku użycia wiadomości e-mail do klienta](./media/signalr-concept-performance/sendtoclient.png)

Koszt routingu **wysyłania do połączenia** jest podobny do kosztu **wysłania do małej grupy**.

Wraz ze wzrostem liczby połączeń koszt routingu ogranicza ogólną wydajność. Unit50 osiągnął limit. W rezultacie Unit100 nie może się dalej poprawiać.

Poniższa tabela jest podsumowaniem statystycznym po wielu rundach uruchamiania testu porównawczego **wysyłania do połączenia.**

|   Wyślij do połączenia   | Jednostka 1 | Jednostka 2 | Jednostka 5 | Jednostka10 | Jednostka 20 | Jednostka50          | Jednostka100         |
|------------------------------------|-------|-------|-------|--------|--------|-----------------|-----------------|
| Połączenia                        | 1000 | 2000 | 5000 | 10 000 | 20 000 | 50 000          | 100 000         |
| Komunikaty przychodzące/wychodzące na sekundę | 1000 | 2000 | 5000 | 8000  | 9000  | 20 000 | 20 000 |
| Przepustowość przychodząca/wychodząca | 2 MB/s    | 4 MB/s    | 10 MB/s   | 16 mb/s    | 18 deputowanych    | 40 mb/s       | 40 mb/s       |

Ten przypadek użycia wymaga wysokiego obciążenia po stronie serwera aplikacji. Zobacz sugerowaną liczbę serwerów aplikacji w poniższej tabeli.

|  Wyślij do połączenia  | Jednostka 1 | Jednostka 2 | Jednostka 5 | Jednostka10 | Jednostka 20 | Jednostka50 | Jednostka100 |
|------------------|-------|-------|-------|--------|--------|--------|---------|
| Połączenia      | 1000 | 2000 | 5000 | 10 000 | 20 000 | 50 000 | 100 000 |
| Liczba serwerów aplikacji | 2     | 2     | 2     | 3      | 3      | 10     | 20      |

> [!NOTE]
> Numer połączenia klienta, rozmiar wiadomości, szybkość wysyłania wiadomości, koszt routingu, warstwa jednostek SKU i procesora/pamięci dla serwera aplikacji wpływają na ogólną wydajność **wysyłania do połączenia**.

#### <a name="aspnet-signalr-echo-broadcast-and-send-to-small-group"></a>ASP.NET echo SignalR, nadawanie i wysyłanie do małej grupy

Usługa Azure SignalR zapewnia taką samą wydajność dla ASP.NET SignalR. 

Test wydajności używa aplikacji Azure Web Apps z [standardowego planu usług S3](https://azure.microsoft.com/pricing/details/app-service/windows/) dla ASP.NET SignalR.

W poniższej tabeli przedstawiono sugerowaną liczbę aplikacji internetowych dla ASP.NET **echo**SignalR .

|   Echo           | Jednostka 1 | Jednostka 2 | Jednostka 5 | Jednostka10 | Jednostka 20 | Jednostka50 | Jednostka100 |
|------------------|-------|-------|-------|--------|--------|--------|---------|
| Połączenia      | 1000 | 2000 | 5000 | 10 000 | 20 000 | 50 000 | 100 000 |
| Liczba serwerów aplikacji | 2     | 2     | 4     | 4      | 8      | 32      | 40       |

W poniższej tabeli przedstawiono sugerowaną liczbę aplikacji internetowych dla ASP.NET **emisji**SignalR .

|  Emisja       | Jednostka 1 | Jednostka 2 | Jednostka 5 | Jednostka10 | Jednostka 20 | Jednostka50 | Jednostka100 |
|------------------|-------|-------|-------|--------|--------|--------|---------|
| Połączenia      | 1000 | 2000 | 5000 | 10 000 | 20 000 | 50 000 | 100 000 |
| Liczba serwerów aplikacji | 2     | 2     | 2     | 2      | 2      | 2      | 2       |

W poniższej tabeli przedstawiono sugerowaną liczbę aplikacji internetowych dla ASP.NET **SignalR wysyłania do małej grupy**.

|  Wyślij do małej grupy     | Jednostka 1 | Jednostka 2 | Jednostka 5 | Jednostka10 | Jednostka 20 | Jednostka50 | Jednostka100 |
|------------------|-------|-------|-------|--------|--------|--------|---------|
| Połączenia      | 1000 | 2000 | 5000 | 10 000 | 20 000 | 50 000 | 100 000 |
| Liczba serwerów aplikacji | 2     | 2     | 4     | 4      | 8      | 32      | 40       |

### <a name="serverless-mode"></a>Tryb bezserwerowy

Klienci i usługa Azure SignalR są zaangażowane w tryb bezserwerowy. Każdy klient oznacza jedno połączenie. Klient wysyła wiadomości za pośrednictwem interfejsu API REST do innego klienta lub emituje wiadomości do wszystkich.

Wysyłanie komunikatów o wysokiej gęstości za pośrednictwem interfejsu API REST nie jest tak wydajne, jak przy użyciu WebSocket. Wymaga to tworzenia nowego połączenia HTTP za każdym razem, a to dodatkowy koszt w trybie bezserwerowym.

#### <a name="broadcast-through-rest-api"></a>Emisja za pośrednictwem interfejsu API REST
Wszyscy klienci ustanawiają połączenia WebSocket za pomocą usługi Azure SignalR. Następnie niektórzy klienci rozpocząć nadawanie za pośrednictwem interfejsu API REST. Wiadomość wysyłająca (przychodzące) jest za pośrednictwem protokołu HTTP Post, który nie jest wydajny w porównaniu z WebSocket.

|   Emisja za pośrednictwem interfejsu API REST     | Jednostka 1 | Jednostka 2 | Jednostka 5  | Jednostka10 | Jednostka 20 | Jednostka50  | Jednostka100 |
|---------------------------|-------|-------|--------|--------|--------|---------|---------|
| Połączenia               | 1000 | 2000 | 5000  | 10 000 | 20 000 | 50 000  | 100 000 |
| Wiadomości przychodzące na sekundę  | 2     | 2     | 2      | 2      | 2      | 2       | 2       |
| Wiadomości wychodzące na sekundę | 2000 | 4000 | 10 000 | 20 000 | 40 000 | 100 000 | 200,000 |
| Przepustowość przychodząca  | 4 kb/s    | 4 kb/s    | 4 kb/s     | 4 kb/s     | 4 kb/s     | 4 kb/s      | 4 kb/s      |
| Przepustowość wychodząca | 4 MB/s    | 8 MB/s    | 20 mb/s    | 40 mb/s    | 80 mb/s    | 200 mb/s    | 400 mb/s    |

#### <a name="send-to-user-through-rest-api"></a>Wyślij do użytkownika za pośrednictwem interfejsu API REST
Benchmark przypisuje nazwy użytkowników do wszystkich klientów przed rozpoczęciem łączenia się z usługą Azure SignalR Service. Po nawiązaniu przez klientów połączeń WebSocket z usługą Azure SignalR, zaczynają wysyłać wiadomości do innych osób za pośrednictwem protokołu HTTP Post.

|   Wyślij do użytkownika za pośrednictwem interfejsu API REST | Jednostka 1 | Jednostka 2 | Jednostka 5  | Jednostka10 | Jednostka 20 | Jednostka50  | Jednostka100 |
|---------------------------|-------|-------|--------|--------|--------|---------|---------|
| Połączenia               | 1000 | 2000 | 5000  | 10 000 | 20 000 | 50 000  | 100 000 |
| Wiadomości przychodzące na sekundę  | 300   | 600   | 900    | 1,300  | 2000  | 10 000  | 18 000  |
| Wiadomości wychodzące na sekundę | 300   | 600   | 900    | 1,300  | 2000  | 10 000  | 18 000 |
| Przepustowość przychodząca  | 600 kb/s  | 1,2 mln m/s  | 1,8 mln mb/s   | 2,6 mln mb/s   | 4 MB/s     | 10 MB/s     | 36 mb/s    |
| Przepustowość wychodząca | 600 kb/s  | 1,2 mln m/s  | 1,8 mln mb/s   | 2,6 mln mb/s   | 4 MB/s     | 10 MB/s     | 36 mb/s    |

## <a name="performance-test-environments"></a>Środowiska testowe wydajności

Dla wszystkich przypadków użycia wymienionych wcześniej firma Microsoft przeprowadziła testy wydajności w środowisku platformy Azure. Co najwyżej użyliśmy 50 maszyn wirtualnych klienta i 20 maszyn wirtualnych serwera aplikacji. Oto niektóre szczegóły:

- Rozmiar maszyny wirtualnej klienta: StandardDS2V2 (2 procesory wirtualne, pamięć 7G)

- Rozmiar maszyny wirtualnej serwera aplikacji: StandardF4sV2 (4 procesory wirtualne, pamięć 8G)

- Połączenia serwera SDK usługi Azure SignalR: 15

## <a name="performance-tools"></a>Narzędzia wydajności

Narzędzia wydajności usługi Azure SignalR można znaleźć w [usłudze GitHub.](https://github.com/Azure/azure-signalr-bench/)

## <a name="next-steps"></a>Następne kroki

W tym artykule masz omówienie wydajności usługi Azure SignalR w typowych scenariuszach przypadków użycia.

Aby uzyskać szczegółowe informacje na temat wewnętrznych usług i skalowania dla niej, przeczytaj następujące przewodniki:

* [Wewnętrzne elementy usługi Azure SignalR Service](signalr-concept-internals.md)
* [Skalowanie usługi azure signalr](signalr-howto-scale-multi-instances.md)
