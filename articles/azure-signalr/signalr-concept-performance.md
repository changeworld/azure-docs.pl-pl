---
title: Wskazówki dotyczące wydajności dla usługi Azure SignalR Service
description: Omówienie usługi Azure SignalR Service wydajności.
author: sffamily
ms.service: signalr
ms.topic: conceptual
ms.date: 04/08/2019
ms.author: zhshang
ms.openlocfilehash: 53139dd253c491ea6578fd0b9cbada4e7b331c7d
ms.sourcegitcommit: 1a19a5845ae5d9f5752b4c905a43bf959a60eb9d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/11/2019
ms.locfileid: "59502041"
---
# <a name="performance-guide-for-azure-signalr-service"></a>Wskazówki dotyczące wydajności dla usługi Azure SignalR Service

Jedną z kluczowych korzyści dotyczące korzystania z usługi Azure SignalR Service to łatwe do skalowania aplikacji SignalR. W scenariuszu na dużą skalę wydajność, staje się ważnym czynnikiem. W tym przewodniku, firma Microsoft wprowadzi czynników, które mają wpływ na wydajność aplikacji SignalR, a w ramach scenariuszy przypadków użycia innego, co to jest typowe wydajności? Na koniec również udostępnimy środowiska i narzędzia używane do generowania raportów wydajności.

## <a name="terms-definition"></a>Definicja warunki

*ASRS*: Usługa Azure SignalR Service

*Liczba przychodzących*: wiadomości przychodzące do usługi Azure SignalR Service

*Wychodzące*: wychodzącej wiadomości z usługi Azure SignalR Service

*Przepustowość*: całkowity rozmiar wszystkich komunikatów w ciągu 1 sekundy

*Domyślny tryb*: ASRS oczekuje, że serwer aplikacji, aby nawiązać połączenie z nią przed zaakceptowaniem połączeń klienta. Jest to domyślny tryb pracy podczas tworzenia ASRS.

*Tryb bez użycia serwera*: ASRS akceptuje tylko połączenia klienta. Brak połączenia z serwerem jest dozwolone.

## <a name="overview"></a>Omówienie

ASRS definiuje siedem warstwy standardowa o pojemności maksymalnej wydajności i w przewodniku zamierza odpowiedzieć na następujące pytania:

-   Co to jest typowe ASRS wydajność dla poszczególnych warstw?

-   ASRS spełnia moich wymagań przepływność komunikatów, na przykład wysyłanie 100 000 komunikatów na sekundę?

-   Dla mojego konkretnego scenariusza, która warstwa jest odpowiedni dla mnie? Lub w jaki sposób można wybrać odpowiednie warstwę?

-   Jakiego rodzaju aplikacji serwera (rozmiar maszyny Wirtualnej) jest odpowiedni dla mnie i ile z nich jest wdrożyć?

Odpowiedzi na pytania, ten przewodnik wydajności najpierw zapewnia ogólne informacje na temat czynników, które mają wpływ na wydajność, a następnie przedstawia maksymalną liczbę komunikatów przychodzących i wychodzących dla każdej warstwy dla typowe przypadki użycia: **echo**, **emisji**, **wysyłania do grupy**, i **połączeń wysyłania** (przejmować równorzędnych).

Nie jest możliwe dla tego dokumentu uwzględnić wszystkie scenariusze (i przypadek użycia różnych, inny komunikat rozmiar lub wzorzec wysyłania wiadomości itp.). Zapewnia jednak niektóre metody oceny, aby ułatwić użytkownikom około ocenić ich wymaganiem wiadomości przychodzących lub wychodzących, a następnie znajdź odpowiednie warstw, sprawdzając w tabeli wydajności.

## <a name="performance-insight"></a>Szczegółowe informacje o wydajności

W tej sekcji opisano metodologii oceny wydajności, a następnie wyświetla listę wszystkich czynników, które mają wpływ na wydajność. Na koniec zapewnia metody do oceniania wymagań dotyczących wydajności.

### <a name="methodology"></a>Metodologia

**Przepływność** i **opóźnienie** dwie typowe kwestie sprawdzania wydajności. ASRS inną warstwę SKU ma różne przepływności ograniczania zasad. Ten dokument **maksymalna dozwolona wartość przepływności (przepustowość ruchu przychodzącego i wychodzącego)** jako wartość maksymalną osiągnięta przepływność, gdy 99% wiadomości opóźnienie mniej niż 1 sekundę.

Opóźnienie to przedział czasu za pośrednictwem połączenia wysyłania komunikatu do odbierania komunikatów odpowiedzi z ASRS. Weźmy **echo** na przykład każde połączenie klienta dodaje sygnaturę czasową w komunikacie. Serwer aplikacji Centrum wysyła oryginalnej wiadomości z powrotem do klienta. Dlatego opóźnienia propagacji łatwo jest obliczana przez każde połączenie klienta. Sygnatura czasowa jest dołączony do każdej wiadomości w **emisji**, **wysyłania do grupy**, i **połączeń wysyłania**.

Aby symulować tysiące klientów równoczesnych połączeń, wiele maszyn wirtualnych są tworzone w wirtualnej sieci prywatnej, na platformie Azure. Łączenie wszystkich tych maszyn wirtualnych w tym samym wystąpieniu ASRS.

W programie ASRS domyślny tryb aplikacji serwerowych maszyn wirtualnych są wdrożone w tym samym wirtualnej sieci prywatnej jako klienta maszyn wirtualnych.

Wszystkich maszyny wirtualne klienta i serwera aplikacji znajdujących się maszyny wirtualne są wdrażane w tej samej sieci o tym samym regionie, aby uniknąć krzyżowe opóźnienie regionu.

### <a name="performance-factors"></a>Czynnikami wydajnościowymi

Teoretycznie ASRS pojemność jest ograniczona przez zasobów obliczeniowych: Procesor CPU, pamięci i sieci. Na przykład więcej połączeń ASRS, więcej pamięci ASRS używane. Dla większych ruchu wiadomości na przykład, każdy komunikat jest większa niż 2048 bajtów, wymaga ASRS poświęcić więcej cykli procesora CPU do przetwarzania oraz. W tym samym czasie przepustowość sieci platformy Azure również nakłada limit maksymalny ruchu.

Typ transportu [WebSocket](https://en.wikipedia.org/wiki/WebSocket), [serwera wysyłane-wydarzeń](https://en.wikipedia.org/wiki/Server-sent_events), lub [długiego sondowania](https://en.wikipedia.org/wiki/Push_technology), jest inny współczynnik znacząco wpływa na wydajność. Protokół WebSocket jest protokół komunikacji dwukierunkowej i pełnego dupleksu za pośrednictwem jednego połączenia TCP. Jednak serwera — wysyłane — zdarzenie jest protokół jednokierunkową do wypychania komunikatów z serwera do klienta. Długiego sondowania wymaga od klientów do okresowego wysyłania informacji z serwera za pomocą żądania HTTP. Dla tego samego interfejsu API w ramach tego samego warunku WebSocket ma najwyższą wydajność serwera wysyłane-zdarzeń jest wolniejszy i długiego sondowania jest niższa. ASRS zaleca WebSocket domyślnie.

Ponadto koszt routingu komunikatów ogranicza wydajność. ASRS odgrywa rolę jako router wiadomość, która kieruje wiadomości z zestawu serwerów lub klientów do innych klientów i serwerów. Inny scenariusz lub interfejsu API wymaga różnych zasad routingu. Aby uzyskać **echo**, klient wysyła komunikat do samego siebie i routingu obiektem docelowym jest także sam. Ten wzorzec ma najniższą routingu kosztów. Ale w przypadku **emisji**, **wysyłania do grupy**, **wysyłania do połączenia**, ASRS trzeba wyszukać połączeń docelowym za pośrednictwem struktury danych rozproszonych wewnętrznych, który zajmuje więcej czasu Procesora, pamięci i nawet przepustowości sieci. W rezultacie, wydajność jest niższa niż **echo**.

W domyślnym trybie serwera aplikacji może również stać się wąskim gardłem w niektórych scenariuszach, ponieważ do wywołania koncentratora, w tym czasie utrzymuje połączenia na żywo z każdego komputera klienckiego za pomocą sygnały pulsu — zestaw SDK usługi Azure SignalR.

W trybie bez użycia serwera klient wysyła komunikat przez post protokołu HTTP, który nie jest wydajne niż protokołu WebSocket.

Innym czynnikiem jest protokół: JSON i [MessagePack](https://msgpack.org/index.html). MessagePack jest mniejszy rozmiar i dostarczane szybciej niż JSON. Intuicyjne MessagePack będą korzystać z wydajności, ale ASRS wydajności nie jest poufnych z protokołami, ponieważ nie dekoduje ładunku komunikatu podczas przekazywania wiadomości z klientów na serwerach lub na odwrót.

Podsumowując następujące czynniki mają wpływ na zdolność ruchu przychodzącego i wychodzącego:

-   Warstwa jednostki SKU (Procesora/pamięci)

-   Liczba połączeń

-   Rozmiar komunikatu

-   wskaźnik wysyłania wiadomości

-   Typ transportu (WebSocket/serwerze-wysyłane — zdarzenia/długiego sondowania)

-   Scenariusz przypadków (routingu koszt) użycia

-   połączenia server i usługę aplikacji (w trybie serwera)


### <a name="find-a-proper-sku"></a>Znajdź odpowiednie jednostki SKU

Ocenianie pojemności dla ruchu przychodzącego/wychodzącego lub sposób znajdowania, która jest odpowiednia dla specyficznego przypadku użycia?

Przyjęto założenie, serwera aplikacji jest wystarczająca i nie jest wąskim gardłem. Następnie możemy sprawdzić maksymalnej przepustowości dla ruchu przychodzącego i wychodzącego dla każdej warstwy.

#### <a name="quick-evaluation"></a>Szybką ocenę

Teraz uprościć oceny najpierw, zakładając, że niektóre ustawienia domyślne: WebSocket jest używany, rozmiar komunikatu wynosi 2048 bajtów, co 1 s Trwa wysyłanie komunikatu i jest w domyślnym trybie.

Każdą warstwę ma swoją własną maksymalnej przepustowości dla ruchu przychodzącego i wychodzącego przepustowości. Środowisko użytkownika Smooth nie jest gwarantowane, gdy ruch przychodzący lub wychodzący przekracza limit.

**Echo** zapewnia maksymalnej przepustowości dla ruchu przychodzącego, ponieważ ma ona routingu najniższy koszt. **Emisja** definiuje przepustowości maksymalnej wiadomości wychodzących.

Czy **nie** przekracza wyróżnione wartości w dwóch poniższych tabelach.

|       Echo                        | Unit1 | Unit2 | Unit5 | Unit10 | Unit20 | Unit50 | Unit100 |
|-----------------------------------|-------|-------|-------|--------|--------|--------|---------|
| Połączenia                       | 1000 | 2000 | 5000 | 10 000 | 20,000 | 50,000 | 100 000 |
| **Przepustowości dla ruchu przychodzącego (bajtów/s)** | **2M**    | **4M**    | **10 MLN**   | **20 MLN**    | **40M**    | **100 MLN**   | **200M**    |
| Przepustowość ruchu wychodzącego (bajtów/s) | 2M    | 4M    | 10 MLN   | 20 MLN    | 40M    | 100 MLN   | 200M    |


|     Emisja             | Unit1 | Unit2 | Unit5  | Unit10 | Unit20 | Unit50  | Unit100 |
|---------------------------|-------|-------|--------|--------|--------|---------|---------|
| Połączenia               | 1000 | 2000 | 5000  | 10 000 | 20,000 | 50,000  | 100 000 |
| Przepustowości dla ruchu przychodzącego (bajtów/s)  | 4K    | 4K    | 4K     | 4K     | 4K     | 4K      | 4K     |
| **Przepustowość ruchu wychodzącego (bajtów/s)** | **4M**    | **8M**    | **20 MLN**    | **40M**    | **80 MLN**    | **200M**    | **400M**   |

Przepustowości dla ruchu przychodzącego i przepustowość wychodzącą formuły:
```
  inboundBandwidth = inboundConnections * messageSize / sendInterval
  outboundBandwidth = outboundConnections * messageSize / sendInterval
```

*inboundConnections*: liczba połączeń, wysyłanie komunikatu

*outboundConnections*: liczba połączeń odbieranie wiadomości

*messageSize*: rozmiar jednego komunikatu (wartość średnia). Dla małych komunikatu, którego rozmiar jest mniejszy niż 1024 bajty ma negatywny wpływ na wydajność podobne jako wiadomości 1024 bajtów.

*sendInterval*: podczas wysyłania jeden komunikat, zwykle jest 1 sekundę na komunikat, co oznacza wysyłanie jeden komunikat co sekundę. Mniejsze sendInterval oznacza, że wysyła więcej wiadomości w danym przedziale czasu. Na przykład 0,5 sekundy na komunikat oznacza, że wysyłanie komunikatów co sekundę.

*Połączenia* jest ASRS zatwierdzone maksymalną wartość progową dla każdej warstwy. Jeśli dodatkowo zwiększa się liczba połączeń, będzie odczuwają ograniczania przepustowości połączenia.

*Liczba przychodzących przepustowości* i *przepustowości wychodzącej* rozmiar łączna liczba komunikatów na sekundę. W tym miejscu am "oznacza, że megabajt dla uproszczenia.

#### <a name="evaluation-for-complex-use-cases"></a>Ocena dla przypadków użycia złożonych

##### <a name="bigger-message-size-or-different-sending-rate"></a>Większy rozmiar komunikatu lub inną szybkość wysyłania

Przypadek użycia w rzeczywistości jest bardziej skomplikowany. Może też wysyłać wiadomości większych niż 2048 bajtów lub szybkość wysyłania wiadomości jest nie jeden komunikat na sekundę. Na przykład można znaleźć ocenianie jego wydajność, Przyjrzyjmy się emisja unit100 firmy.

W poniższej tabeli przedstawiono rzeczywiste przypadek **emisji**, ale rozmiar komunikatu, liczba połączeń i częstotliwość wysyłania wiadomości różnią się od co firma Microsoft zakłada, że w poprzedniej sekcji. Pytanie jest o tym, jak firma Microsoft dowolnego z tych elementów (rozmiar komunikatu, liczba połączeń lub szybkość wysyłania wiadomości) wywnioskować Jeśli wiemy tylko 2 z nich.

| Emisja  | Rozmiar komunikatu (bajty) | Dla ruchu przychodzącego (komunikatów/s) | Połączenia | Wyślij interwały (drugie) |
|---|---------------------|--------------------------|-------------|-------------------------|
| 1 | 20 K                 | 1                        | 100 000     | 5                       |
| 2 | 256 K                | 1                        | 8000       | 5                       |

Poniższa formuła jest łatwe, można go wywnioskować na podstawie istniejącej poprzednie formuły:

```
outboundConnections = outboundBandwidth * sendInterval / messageSize
```

Dla unit100, wiemy maksymalna przepustowość ruchu wychodzącego jest 400M z poprzedniej tabeli, a następnie rozmiar 20 K komunikatu o maksymalnej liczbie połączeń wychodzących powinna być 400M \* 5 / 20 K = 100 000, który pasuje do wartości rzeczywistych.

##### <a name="mixed-use-cases"></a>Zastosowań mieszanych

Przypadek użycia rzeczywistych ze sobą zwykle napisana cztery podstawowego zastosowań: **echo**, **emisji**, **wysyłania do grupy**, lub **wysyłać połączenia**. Metody używane do oceny wydajności jest podzielona przypadki użycia mieszane cztery podstawowego przypadkach **obliczenia przepustowości maksymalnej wiadomości przychodzących i wychodzących** oddzielnie przy użyciu formuły powyżej, a ich w celu uzyskania łączną sumę Maksymalna przepustowość ruchu przychodzącego/wychodzącego. Następnie wybierz odpowiednie warstwy z tabel maksymalnej przepustowości dla ruchu przychodzącego/wychodzącego.

W tym samym czasie wysyłania komunikatu do setek lub tysięcy mniejszym grupom lub tysięcy klientów, wysyłając do siebie nawzajem, koszt routingu stają się dominującego. Można uwzględnić wpływ. Szczegółowe informacje znajdują się w poniższych sekcjach "Case study".

W przypadku użycia wysyłania komunikatu do klientów, upewnij się, to serwer aplikacji **nie** wąskie gardło. Sekcja "Case study", zapewnia wytycznymi, o ile serwery aplikacji są potrzebne, i należy je skonfigurować liczbę połączeń serwera.

## <a name="case-study"></a>Analiza przypadku

Poniższe sekcje przechodzą przez cztery typowe przypadki użycia dla transportu WebSocket: **echo**, **emisji**, **wysyłania do grupy**, i **wysyłania do połączenia** . W każdym scenariuszu, wyświetla listę bieżącej ASRS dla ruchu przychodzącego i wychodzącego pojemności w międzyczasie wyjaśniający, co to jest główne czynniki na wydajność.

W domyślnym trybie, serwera aplikacji, za pomocą usługi Azure SignalR Service SDK domyślnie tworzy pięć połączenia z serwerem za pomocą ASRS. Wynik poniżej, serwer, który połączeń zwiększa się do 15 (lub więcej do emisji lub wysyłania komunikatu do dużych grup) testu w czasie wykonywania.

Różnych przypadków użycia mają różne wymagania dotyczące serwerów aplikacji sieci. **Emisja** wymaga niewielkiej liczby serwerów aplikacji. **Echo** lub **połączeń wysyłania** wymaga wielu serwerów aplikacji.

We wszystkich przypadkach użycia, domyślny rozmiar komunikatu wynosi 2048 bajtów i interwał wysyłania komunikatów wynosi 1 s.

## <a name="default-mode"></a>Tryb domyślny

Klientów, serwerów aplikacji sieci web i ASRS biorących udział w tym trybie. Każdy klient oznacza jednego połączenia.

### <a name="echo"></a>Echo

Po pierwsze aplikacje sieci web nawiązać ASRS. Po drugie, wiele klienci łączą się z aplikacji sieci web, które przekierowywania klientów do ASRS przy użyciu tokenu dostępu i punktu końcowego. Następnie klienci ustanawiania połączeń protokołu WebSocket z ASRS.

Wszyscy klienci nawiązywać połączenia, mogą uruchamiany wysyłania komunikatu, który zawiera sygnaturę czasową do określonego koncentratora co sekundę. Centrum echa komunikat, przywracając jego oryginalny klient. Każdy klient oblicza opóźnienia, po odebraniu wiadomości echo ponownie.

Kroki od 5\~8 (ruch wyróżniony czerwonym) znajdują się w pętli, która działa dla domyślny czas trwania (5 minut) i uzyskać statystyki wszystkich opóźnienie wiadomości.
Przewodnik dotyczący wydajności przedstawia maksymalną liczbę połączeń klienta.

![Echo](./media/signalr-concept-performance/echo.png)

**Echo**firmy zachowanie ustali, że maksymalnej przepustowości dla ruchu przychodzącego jest równa maksymalnej przepustowości wychodzącej. Zobacz poniższą tabelę.

|       Echo                        | Unit1 | Unit2 | Unit5 | Unit10 | Unit20 | Unit50 | Unit100 |
|-----------------------------------|-------|-------|-------|--------|--------|--------|---------|
| Połączenia                       | 1000 | 2000 | 5000 | 10 000 | 20,000 | 50,000 | 100 000 |
| Inbound/Outbound (komunikatów/s) | 1000 | 2000 | 5000 | 10 000 | 20,000 | 50,000 | 100 000 |
| Przepustowość ruchu przychodzącego/wychodzącego (bajtów/s) | 2M    | 4M    | 10 MLN   | 20 MLN    | 40M    | 100 MLN   | 200M    |

W tym przypadku użycia każdego komputera klienckiego wywołuje koncentratora zdefiniowanego na serwerze aplikacji. Koncentrator, po prostu wywołuje metody zdefiniowanej w oryginalnym po stronie klienta. To Centrum jest najbardziej światła zważoną Centrum dla **echo**.

```
        public void Echo(IDictionary<string, object> data)
        {
            Clients.Client(Context.ConnectionId).SendAsync("RecordLatency", data);
        }
```

Nawet w przypadku tego prostego koncentratora wykorzystanie ruchu na serwerze aplikacji jest również widoczne jako **echo** zwiększa komunikatu przychodzącego. W związku z tym wymaga wielu serwerów aplikacji, w przypadku dużych jednostek SKU. W poniższej tabeli wymieniono liczby serwerów aplikacji, dla każdej warstwy.


|    Echo          | Unit1 | Unit2 | Unit5 | Unit10 | Unit20 | Unit50 | Unit100 |
|------------------|-------|-------|-------|--------|--------|--------|---------|
| Połączenia      | 1000 | 2000 | 5000 | 10 000 | 20,000 | 50,000 | 100 000 |
| Liczba serwerów aplikacji | 2     | 2     | 2     | 3      | 3      | 10     | 20      |

> [!NOTE]
>
> Klient połączenia liczbę, rozmiar komunikatu, komunikat wysyłania szybkości, warstwa jednostki SKU i serwera aplikacji Procesora/pamięci mają wpływ na ogólną wydajność **echo**.

### <a name="broadcast"></a>Emisja

Aby uzyskać **emisji**, gdy aplikacja internetowa odbierze komunikat, emituje do wszystkich klientów. Większej liczby klientów do emisji więcej ruchu wiadomość do wszystkich klientów. Zobacz poniższy diagram.

![Emisja](./media/signalr-concept-performance/broadcast.png)

Charakterystyka emisji jest istnieje niewielka liczba klientów emisji, co oznacza, że przepustowości dla ruchu przychodzącego komunikatu jest mały, ale przepustowość ruchu wychodzącego jest bardzo duży. Przepustowość ruchu wychodzącego wiadomości zwiększa się jako połączenie klienta lub zwiększenie współczynnika emisji.

Maksymalna liczba połączeń klientów, liczba komunikatów dla ruchu przychodzącego/wychodzącego i przepustowość są podsumowane w poniższej tabeli.

|     Emisja             | Unit1 | Unit2 | Unit5  | Unit10 | Unit20 | Unit50  | Unit100 |
|---------------------------|-------|-------|--------|--------|--------|---------|---------|
| Połączenia               | 1000 | 2000 | 5000  | 10 000 | 20,000 | 50,000  | 100 000 |
| Dla ruchu przychodzącego (komunikatów/s)  | 2     | 2     | 2      | 2      | 2      | 2       | 2       |
| Wychodzące (komunikatów/s) | 2000 | 4,000 | 10 000 | 20,000 | 40,000 | 100 000 | 200,000 |
| Przepustowości dla ruchu przychodzącego (bajtów/s)  | 4K    | 4K    | 4K     | 4K     | 4K     | 4K      | 4K      |
| Przepustowość ruchu wychodzącego (bajtów/s) | 4M    | 8M    | 20 MLN    | 40M    | 80 MLN    | 200M    | 400M    |

Klientów emisji, umieszczać komunikaty są nie więcej niż 4, więc wymaga mniejszej liczby serwerów aplikacji, w porównaniu z **echo** od ilości ruchu przychodzącego komunikatu jest mała. Dwa serwery aplikacji są wystarczająco wziąć pod uwagę zarówno umowy SLA, jak i wydajności. Jednak domyślnie połączenia serwera, należy zwiększyć w celu uniknięcia problemu niezrównoważone, szczególnie w przypadku Unit50 i Unit100.

|   Emisja      | Unit1 | Unit2 | Unit5 | Unit10 | Unit20 | Unit50 | Unit100 |
|------------------|-------|-------|-------|--------|--------|--------|---------|
| Połączenia      | 1000 | 2000 | 5000 | 10 000 | 20,000 | 50,000 | 100 000 |
| Liczba serwerów aplikacji | 2     | 2     | 2     | 2      | 2      | 2      | 2       |

> [!NOTE]
>
> Zwiększ domyślne połączenia serwera z zakresu od 5 do 40 na każdym serwerze aplikacji, aby uniknąć możliwych serwera niezrównoważone połączenia ASRS.
>
> Liczba połączeń klienta, rozmiar komunikatu, szybkość wysyłania wiadomości i warstwa jednostki SKU mieć wpływ na ogólną wydajność dla **emisji**

### <a name="send-to-group"></a>Wyślij do grupy

**Wyślij do grupy** ma podobny wzorzec ruchu z tą różnicą, że po klientów ustanawiania połączeń protokołu WebSocket przy użyciu ASRS, dołączenie do grupy przed wysłaniem wiadomości do określonej grupy. Poniższy diagram przedstawia przepływ ruchu.

![Wyślij do grupy](./media/signalr-concept-performance/sendtogroup.png)

Element członkowski grupy i liczba grup są dwóch czynników mających wpływ na wydajność. Aby uprościć analizy, możemy zdefiniować dwa rodzaje grup: małe grupy i big Data.

- `small group`: 10 połączeń w każdej grupie. Numer grupy jest równa (liczba połączeń max) / 10. Na przykład dla 1 jednostki, jeśli istnieją liczby połączeń 1000, mamy jeszcze 1000 / 10 = 100 grupy.

- `Big group`: Numer grupy zawsze wynosi 10. Liczba elementów członkowskich grupy jest równy (liczba połączeń max) / 10. Na przykład dla 1 jednostki, w przypadku liczby połączeń 1000, następnie każda grupa ma 1000 / 10 = 100 elementów członkowskich.

**Wyślij do grupy** oferuje ASRS koszt routingu, ponieważ ma ona można znaleźć połączenia docelowej przy użyciu struktury danych rozproszonych. Zwiększania połączeń wysyłania również zwiększa koszty.

#### <a name="small-group"></a>Małą grupę

Routing koszt jest znaczący do wysyłania wiadomości na wiele małych grup. Obecnie implementacji ASRS osiąga limit koszt routingu w unit50. Dodawanie więcej czasu Procesora i pamięci nie pomocy, więc nie może poprawić unit100 dalsze zgodnie z projektem. Jeśli oczekiwanym przepustowości więcej ruchu przychodzącego do dostosowywania się z pomocą techniczną.

|   Wyślij do małej grupy     | Unit1 | Unit2 | Unit5  | Unit10 | Unit20 | Unit50 | Unit100 |
|---------------------------|-------|-------|--------|--------|--------|--------|---------|
| Połączenia               | 1000 | 2000 | 5000  | 10 000 | 20,000 | 50,000 | 100 000
| Liczba elementów członkowskich grupy        | 10    | 10    | 10     | 10     | 10     | 10     | 10 
| Liczba grup               | 100   | 200   | 500    | 1000  | 2000  | 5000  | 10 000 
| Dla ruchu przychodzącego (komunikatów/s)  | 200   | 400   | 1000  | 2,500  | 4,000  | 7,000  | 7,000   |
| Przepustowości dla ruchu przychodzącego (bajtów/s)  | 400 K  | 800 K  | 2M     | 5 MIN     | 8M     | 14 MLN    | 14 MLN     |
| Wychodzące (komunikatów/s) | 2000 | 4,000 | 10 000 | 25,000 | 40,000 | 70,000 | 70,000  |
| Przepustowość ruchu wychodzącego (bajtów/s) | 4M    | 8M    | 20 MLN    | 50 MLN     | 80 MLN    | 140M   | 140M    |

Istnieje wiele połączeń klienta wywołania koncentratora, w związku z tym, numer serwera aplikacji są także krytyczne znaczenie dla wydajności. Liczba serwerów sugerowanych aplikacji znajduje się w poniższej tabeli.

|  Wyślij do małej grupy   | Unit1 | Unit2 | Unit5 | Unit10 | Unit20 | Unit50 | Unit100 |
|------------------|-------|-------|-------|--------|--------|--------|---------|
| Połączenia      | 1000 | 2000 | 5000 | 10 000 | 20,000 | 50,000 | 100 000 |
| Liczba serwerów aplikacji | 2     | 2     | 2     | 3      | 3      | 10     | 20      |

> [!NOTE]
>
> Liczba połączeń klienta, rozmiar komunikatu, szybkość wysyłania wiadomości, koszt routingu, warstwa jednostki SKU i serwera aplikacji Procesora/pamięci mieć wpływ na ogólną wydajność **wysyłania do małych grupy**.

#### <a name="big-group"></a>Grupa big Data

Dla **send-big grupa**, przepustowość wychodzącą staje się wąskie gardło, zanim osiągnięcia routingu koszt limit. W poniższej tabeli przedstawiono maksymalną przepustowość ruchu wychodzącego jest prawie taki sam jak **emisji**.

|    Wyślij do dużych grup      | Unit1 | Unit2 | Unit5  | Unit10 | Unit20 | Unit50  | Unit100 |
|---------------------------|-------|-------|--------|--------|--------|---------|---------|
| Połączenia               | 1000 | 2000 | 5000  | 10 000 | 20,000 | 50,000  | 100 000
| Liczba elementów członkowskich grupy        | 100   | 200   | 500    | 1000  | 2000  | 5000   | 10 000 
| Liczba grup               | 10    | 10    | 10     | 10     | 10     | 10      | 10
| Dla ruchu przychodzącego (komunikatów/s)  | 20    | 20    | 20     | 20     | 20     | 20      | 20      |
| Przepustowości dla ruchu przychodzącego (bajtów/s)  | 80 K   | 40 TYS.   | 40 TYS.    | 20 K    | 40 TYS.    | 40 TYS.     | 40 TYS.     |
| Wychodzące (komunikatów/s) | 2000 | 4,000 | 10 000 | 20,000 | 40,000 | 100 000 | 200,000 |
| Przepustowość ruchu wychodzącego (bajtów/s) | 8M    | 8M    | 20 MLN    | 40M    | 80 MLN    | 200M    | 400M    |

Wysyłanie liczba połączeń jest nie więcej niż 40, obciążenie serwera aplikacji jest mały, dlatego sugerowane sieci web aplikacji jest również mało.

|  Wyślij do dużych grup  | Unit1 | Unit2 | Unit5 | Unit10 | Unit20 | Unit50 | Unit100 |
|------------------|-------|-------|-------|--------|--------|--------|---------|
| Połączenia      | 1000 | 2000 | 5000 | 10 000 | 20,000 | 50,000 | 100 000 |
| Liczba serwerów aplikacji | 2     | 2     | 2     | 2      | 2      | 2      | 2       |

> [!NOTE]
>
> Zwiększ domyślne połączenia serwera z zakresu od 5 do 40 na każdym serwerze aplikacji, aby uniknąć możliwych serwera niezrównoważone połączenia ASRS.
> 
> Liczba połączeń klienta, rozmiar komunikatu, szybkość wysyłania wiadomości, koszt routingu i warstwy SKU mieć wpływ na ogólną wydajność **send-big grupa**.

### <a name="send-to-connection"></a>Wyślij do połączenia

W tym przypadku użycia po klienci nawiązania połączenia z ASRS, każdy klient wywołuje specjalne Centrum można pobrać własne identyfikator połączenia. Testów porównawczych wydajności odpowiada zbierać wszystkie identyfikatory połączeń, mieszania je i ponownie Przypisz ich do wszystkich klientów jako obiekt docelowy wysyłania. Klienci nadal wysyłania komunikatu do połączenie docelowe, dopóki nie zakończy się test wydajności.

![Wyślij do klienta](./media/signalr-concept-performance/sendtoclient.png)

Koszt marszruty **połączeń wysyłania** przypomina jako **wysyłania do małych grupy**.

W miarę zwiększania liczby połączeń ogólną wydajność jest ograniczona przez koszt routingu. 50 jednostek osiągnęła limit. W rezultacie 100 jednostek nie można usprawnić posługiwanie.

Poniższa tabela dotyczy statystyki podsumowujące po wielu rund uruchomiona **połączeń wysyłania** testu porównawczego

|   Wyślij do połączenia   | Unit1 | Unit2 | Unit5 | Unit10 | Unit20 | Unit50          | Unit100         |
|------------------------------------|-------|-------|-------|--------|--------|-----------------|-----------------|
| Połączenia                        | 1000 | 2000 | 5000 | 10 000 | 20,000 | 50,000          | 100 000         |
| Dla ruchu przychodzącego / wychodzącego (komunikatów/s) | 1000 | 2000 | 5000 | 8000  | 9,000  | 20,000 | 20,000 |
| Przepustowości dla ruchu przychodzącego / wychodzącego (bajtów/s) | 2M    | 4M    | 10 MLN   | 16M    | 18 MIN    | 40M       | 40M       |

Ten przypadek użycia wymaga dużym obciążeniem po stronie serwera aplikacji. Zobacz Serwer aplikacji sugerowane count w tabeli poniżej.

|  Wyślij do połączenia  | Unit1 | Unit2 | Unit5 | Unit10 | Unit20 | Unit50 | Unit100 |
|------------------|-------|-------|-------|--------|--------|--------|---------|
| Połączenia      | 1000 | 2000 | 5000 | 10 000 | 20,000 | 50,000 | 100 000 |
| Liczba serwerów aplikacji | 2     | 2     | 2     | 3      | 3      | 10     | 20      |

> [!NOTE]
>
> Liczba połączeń klienta, rozmiar komunikatu, szybkość wysyłania wiadomości, koszt routingu, warstwa jednostki SKU i serwera aplikacji Procesora/pamięci mieć wpływ na ogólną wydajność **połączeń wysyłania**.

### <a name="aspnet-signalr-echobroadcastsend-to-connection"></a>ASP.NET SignalR echo/emisji/Wyślij połączeń

ASRS zawiera samą pojemność wydajności biblioteki SignalR platformy ASP.NET. W tej sekcji przedstawiono liczba sugerowane web app for ASP.NET SignalR **echo**, **emisji**, i **wysyłania do małych grupy**.

Test wydajności używa aplikacji sieci Web platformy Azure z [standardowa S3 planu usługi](https://azure.microsoft.com/pricing/details/app-service/windows/) dla biblioteki SignalR platformy ASP.NET.

- `echo`

|   Echo           | Unit1 | Unit2 | Unit5 | Unit10 | Unit20 | Unit50 | Unit100 |
|------------------|-------|-------|-------|--------|--------|--------|---------|
| Połączenia      | 1000 | 2000 | 5000 | 10 000 | 20,000 | 50,000 | 100 000 |
| Liczba serwerów aplikacji | 2     | 2     | 4     | 4      | 8      | 32      | 40       |

- `broadcast`

|  Emisja       | Unit1 | Unit2 | Unit5 | Unit10 | Unit20 | Unit50 | Unit100 |
|------------------|-------|-------|-------|--------|--------|--------|---------|
| Połączenia      | 1000 | 2000 | 5000 | 10 000 | 20,000 | 50,000 | 100 000 |
| Liczba serwerów aplikacji | 2     | 2     | 2     | 2      | 2      | 2      | 2       |

- `Send-to-small-group`

|  Wyślij do małej grupy     | Unit1 | Unit2 | Unit5 | Unit10 | Unit20 | Unit50 | Unit100 |
|------------------|-------|-------|-------|--------|--------|--------|---------|
| Połączenia      | 1000 | 2000 | 5000 | 10 000 | 20,000 | 50,000 | 100 000 |
| Liczba serwerów aplikacji | 2     | 2     | 4     | 4      | 8      | 32      | 40       |

## <a name="serverless-mode"></a>Tryb bez użycia serwera

Klienci i ASRS biorących udział w tym trybie. Każdy klient oznacza jednego połączenia. Klient wysyła komunikaty za pośrednictwem interfejsu API REST do innego klienta lub odpowiedniej do emisji komunikatów dla wszystkich.

Wysyłanie komunikatów o wysokiej gęstości za pośrednictwem interfejsu API REST jest wydajne niż WebSocket, ponieważ wymaga, aby utworzyć nowe połączenie HTTP każdym - dodatkowych opłat w trybie bez użycia serwera.

### <a name="broadcast-through-rest-api"></a>Emisja za pośrednictwem interfejsu API REST
Wszyscy klienci ustanawiania połączeń protokołu WebSocket z ASRS. Niektórzy klienci zacznij emisji za pośrednictwem interfejsu API REST. Wysyłanie (ruch przychodzący) komunikatów są wspomaga Post protokołu HTTP, który nie jest efektywne w porównaniu z protokołu WebSocket.

|   Emisja za pośrednictwem interfejsu API REST     | Unit1 | Unit2 | Unit5  | Unit10 | Unit20 | Unit50  | Unit100 |
|---------------------------|-------|-------|--------|--------|--------|---------|---------|
| Połączenia               | 1000 | 2000 | 5000  | 10 000 | 20,000 | 50,000  | 100 000 |
| Dla ruchu przychodzącego (komunikatów/s)  | 2     | 2     | 2      | 2      | 2      | 2       | 2       |
| Wychodzące (komunikatów/s) | 2000 | 4,000 | 10 000 | 20,000 | 40,000 | 100 000 | 200,000 |
| Przepustowości dla ruchu przychodzącego (bajtów/s)  | 4K    | 4K    | 4K     | 4K     | 4K     | 4K      | 4K      |
| Przepustowość ruchu wychodzącego (bajtów/s) | 4M    | 8M    | 20 MLN    | 40M    | 80 MLN    | 200M    | 400M    |

### <a name="send-to-user-through-rest-api"></a>Wysłać do użytkownika za pośrednictwem interfejsu API REST
Testu porównawczego przypisuje nazwy użytkownika do wszystkich klientów, przed rozpoczęciem nawiązywania połączenia z ASRS. Po ustaleniu połączeń protokołu WebSocket przy użyciu ASRS klientów uruchamiania wysyłania wiadomości do innych użytkowników za pomocą żądania HTTP Post.

|   Wysłać do użytkownika za pośrednictwem interfejsu API REST | Unit1 | Unit2 | Unit5  | Unit10 | Unit20 | Unit50  | Unit100 |
|---------------------------|-------|-------|--------|--------|--------|---------|---------|
| Połączenia               | 1000 | 2000 | 5000  | 10 000 | 20,000 | 50,000  | 100 000 |
| Dla ruchu przychodzącego (komunikatów/s)  | 300   | 600   | 900    | 1,300  | 2000  | 10 000  | 18,000  |
| Wychodzące (komunikatów/s) | 300   | 600   | 900    | 1,300  | 2000  | 10 000  | 18,000 |
| Przepustowości dla ruchu przychodzącego (bajtów/s)  | 600 K  | 1.2 M  | 1.8 M   | 2.6 M   | 4M     | 10 MLN     | 36M    |
| Przepustowość ruchu wychodzącego (bajtów/s) | 600 K  | 1.2 M  | 1.8 M   | 2.6 M   | 4M     | 10 MLN     | 36M    |

## <a name="performance-test-environments"></a>Wydajność środowiska testowe

Test wydajności dla wszystkich przypadków użycia wymienionych powyżej zostały przeprowadzone w środowisku platformy Azure. W większości 50 klienta maszyn wirtualnych i 20 serwera aplikacji maszyn wirtualnych są używane.

Rozmiar maszyny Wirtualnej klienta: StandardDS2V2 (2 procesory vCPU, pamięci 7G)

Serwer aplikacji rozmiar maszyny Wirtualnej: StandardF4sV2 (4 vCPU, pamięci 8 K)

Azure SignalR SDK połączeń serwera: 15

## <a name="performance-tools"></a>Narzędzia wydajności

https://github.com/Azure/azure-signalr-bench/tree/master/SignalRServiceBenchmarkPlugin

## <a name="next-steps"></a>Kolejne kroki

W tym artykule uzyskasz Przegląd wydajności usługi SignalR w typowym zastosowaniem przypadków.

Aby uzyskać więcej informacji o podstawy usługi SignalR i skalowania dla usługi SignalR, przewodniku.

* [Elementy wewnętrzne usługi Azure SignalR Service](signalr-concept-internals.md)
* [Usługi Azure SignalR Service skalowania](signalr-howto-scale-multi-instances.md)