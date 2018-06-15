---
title: Inspekcję pakietów z obserwatora sieciowego Azure | Dokumentacja firmy Microsoft
description: W tym artykule opisano sposób użycia obserwatora sieciowego przeprowadzać inspekcję pakietów zebrane z maszyny Wirtualnej
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: ''
ms.assetid: 7b907d00-9c35-40f5-a61e-beb7b782276f
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: jdial
ms.openlocfilehash: 1ad6ca4abe73336ce9ce3539fdaf2a9d7dd23fa6
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/11/2017
ms.locfileid: "23863972"
---
# <a name="packet-inspection-with-azure-network-watcher"></a>Inspekcję pakietów z obserwatora sieciowego Azure

Za pomocą funkcji przechwytywania pakietów obserwatora sieciowego, można zainicjować i zarządzanie sesjami przechwytywania na maszynach wirtualnych platformy Azure z portalu, programu PowerShell, interfejsu wiersza polecenia i programowo przy użyciu zestawu SDK i interfejsu API REST. Przechwytywania pakietów umożliwia scenariusze adresów, które wymagają danych na poziomie pakietów przez przekazywanie informacji w formacie łatwo można używać. Wykorzystanie bezpłatnych narzędzi, aby sprawdzić dane, możesz sprawdzić łączności wysyłane do i z maszyn wirtualnych i uzyskać wgląd w ruchu sieciowego. Niektóre przykładowe zastosowania danych przechwytywania pakietów obejmują: do badania problemów dotyczących sieci lub aplikacji, wykrywanie prób nieprawidłowego użycia i nieautoryzowanego dostępu sieciowego lub utrzymania zgodności z przepisami. W tym artykule, zostanie przedstawiony sposób otwierania pliku przechwytywania pakietów dostarczonego przez obserwatora sieciowego przy użyciu narzędzia popularnych typu open source. Firma Microsoft udostępni również przykładami przedstawiający sposób obliczania opóźnienie połączenia, identyfikowanie nietypowe ruchu i sprawdź statystyki sieci.

## <a name="before-you-begin"></a>Przed rozpoczęciem

W tym artykule przechodzi przez niektóre scenariusze wstępnie skonfigurowane na przechwytywania pakietów, która została wcześniej uruchomiona. Te scenariusze przedstawiono możliwości, które są dostępne, przeglądając przechwytywania pakietów. W tym scenariuszu [WireShark](https://www.wireshark.org/) do zbadania przechwytywania pakietów.

W tym scenariuszu przyjęto założenie, że był już uruchamiany przechwytywania pakietów na maszynie wirtualnej. Aby dowiedzieć się, jak utworzyć odwiedziny przechwytywania pakietów [Przechwytywanie pakietów Zarządzaj w portalu](network-watcher-packet-capture-manage-portal.md) lub REST odwiedzając [z interfejsu API REST zarządzania Przechwytywanie pakietów](network-watcher-packet-capture-manage-rest.md).

## <a name="scenario"></a>Scenariusz

W tym scenariuszu należy:

* Przejrzyj przechwytywania pakietów

## <a name="calculate-network-latency"></a>Oblicz opóźnienia sieci

W tym scenariuszu zostanie przedstawiony sposób wyświetlania początkowy czas obiegu (RTT) między dwoma punktami końcowymi konwersacji Transmission Control Protocol (TCP).

Po nawiązaniu połączenia TCP, pierwsze trzy pakiety wysyłane przez połączenie wykonaj zwanymi powszechnie trójstopniowego wzorca. Sprawdzając pierwsze dwa pakiety przesyłane w tym uzgadnianie początkowe żądanie od klienta i odpowiedzi z serwera, firma Microsoft obliczenia opóźnienia podczas tego połączenia. Czas ten jest określany jako czas obiegu (RTT). Aby uzyskać więcej informacji na temat protokołu TCP i trójstopniowego odwoływać się do następujących zasobów. https://support.microsoft.com/en-US/Help/172983/Explanation-of-the-three-way-Handshake-VIA-TCP-IP

### <a name="step-1"></a>Krok 1

Uruchamianie programu WireShark

### <a name="step-2"></a>Krok 2

Obciążenia **CAP** plik z sieci przechwytywania pakietów. Ten plik znajduje się w obiekcie blob został zapisany w naszym lokalnie na maszynie wirtualnej, w zależności od tego, jak został skonfigurowany.

### <a name="step-3"></a>Krok 3

Aby wyświetlić początkowy czas obiegu (RTT) konwersacji TCP, firma Microsoft będzie tylko patrzeć pierwsze dwa pakiety objętego uzgadnianie protokołu TCP. Użyjemy dwóch pierwszych pakietów trójstopniowego, które są [SYN], [SYN, potwierdzenia] pakietów. Są one nazwane dla flag w nagłówku protokołu TCP. W tym scenariuszu nie użyjemy ostatnim pakiecie w uzgadnianie pakietu [ACK]. Pakiet [SYN] jest wysyłany przez klienta. Po odebraniu serwer wysyła pakiet [potwierdzenia] jako potwierdzenia otrzymania SYN od klienta. Wykorzystanie fakt, że odpowiedzi serwera wymaga nadmiernego obciążenia, możemy obliczyć RTT przez odjęcie ilości czasu [SYN, potwierdzenia] pakiet został odebrany przez klienta w czasie [SYN] pakiet został wysłany przez klienta.

Za pomocą programu WireShark ta wartość jest obliczana firmie Microsoft.

Aby łatwiej przeglądać pierwsze dwa pakiety w trójstopniowego TCP, firma Microsoft będzie wykorzystywać oferowana przez WireShark możliwość filtrowania.

Aby zastosować filtr w WireShark, rozwiń segmentu "Transmission Control Protocol" [SYN] pakietu w Twojej przechwytywania i zbadać flagi w nagłówku protokołu TCP.

Ponieważ czekamy filtrowanie wszystkich [SYN] i [SYN potwierdzenia] pakiety, w obszarze flagi cofirm Syn bit jest ustawiony na wartość 1, a następnie kliknij prawym przyciskiem Syn bit myszy -> Zastosuj jako filtr -> wybrane.

![Rysunek 7][7]

### <a name="step-4"></a>Krok 4

Teraz, gdy okno, aby zobaczyć tylko pakiety z bitowego zestawu [SYN] zostały przefiltrowane, można łatwo wybrać konwersacji, które planuje się wyświetlić RTT początkowej. Prosty sposób, aby wyświetlić RTT w WireShark po prostu kliknij listę rozwijaną oznaczone jako "SEQ/potwierdzenia" analizy. Zostanie wtedy wyświetlone RTT wyświetlane. W takim przypadku RTT został 0.0022114 sekund lub 2.211 ms.

![rysunek 8][8]

## <a name="unwanted-protocols"></a>Protokoły niechciane

Może mieć wiele aplikacji uruchomionych w wystąpieniu maszyny wirtualnej wdrożonej na platformie Azure. Wiele z tych aplikacji komunikują się za pośrednictwem sieci, może bez Twojej zgody jawnej. Za pomocą przechwytywania pakietów do przechowywania komunikacji sieciowej możemy Sprawdź jak mówimy w sieci i Wyszukaj problemy w aplikacji.

W tym przykładzie firma Microsoft analizuje poprzedniego uruchomienia przechwytywania pakietów dla niechciane protokołów, które mogą wskazywać nieautoryzowanego komunikacji z aplikacji na komputerze.

### <a name="step-1"></a>Krok 1

Przy użyciu tego samego przechwytywania w poprzednim scenariuszu kliknij **statystyki** > **protokołu hierarchii**

![Protokół hierarchii menu][2]

Zostanie wyświetlone okno hierarchii protokołu. Ten widok zawiera listę wszystkich protokołów, które były używane podczas sesji przechwytywania i liczbę pakietów wysłanych i odebranych za pomocą protokołów. Ten widok może być przydatne do znajdowania niechciane ruchu sieciowego na maszynach wirtualnych lub w sieci.

![Hierarchia elementów protokołu otwarty][3]

Jak widać w następujących Przechwytywanie ekranu wystąpił ruchu przy użyciu protokołu BitTorrent, który jest używany do udostępniania plików równorzędnych. Jako administrator nie powinny być widoczne BitTorrent ruchu w przypadku tego konkretnego maszyn wirtualnych. Teraz należy pamiętać o tego rodzaju ruch, możesz można usunąć równorzędnych oprogramowanie zainstalowane na tej maszynie wirtualnej lub blokowania ruchu przy użyciu grup zabezpieczeń sieci i zapory. Ponadto może zdecydować się na uruchamiania przechwytywania pakietów zgodnie z harmonogramem, więc możesz przejrzeć Użyj protokołu regularnie na maszynach wirtualnych. Na przykład dotyczące automatyzacji zadań sieci na platformie azure, odwiedź stronę [monitorowania zasobów sieciowych przy użyciu usługi Automatyzacja azure](network-watcher-monitor-with-azure-automation.md)

## <a name="finding-top-destinations-and-ports"></a>Znajdowanie Najpopularniejsze miejsca docelowe i porty

Opis typów ruchu, punkty końcowe i przekazywane za pośrednictwem portów jest ważne podczas monitorowania i rozwiązywania problemów z aplikacji i zasobów w sieci. Przy użyciu pliku przechwytywania pakietów z powyższych, firma Microsoft szybko i można znaleźć górny miejsc docelowych, które naszym wirtualna komunikuje się z portów jej użycia.

### <a name="step-1"></a>Krok 1

Przy użyciu tego samego przechwytywania w poprzednim scenariuszu kliknij **statystyki** > **statystyki IPv4** > **miejsc docelowych i porty**

![Okno przechwytywania pakietów][4]

### <a name="step-2"></a>Krok 2

Jak możemy Przejrzyj wyniki linii będą się wystąpiły wiele połączeń na porcie 111. Został najczęściej używanych portu 3389, czyli pulpitu zdalnego, a pozostałe są dynamiczne porty RPC.

Podczas tego ruchu może oznaczać nic, jest port, który był używany dla wielu połączeń i jest nieznany administratora.

![Rysunek 5][5]

### <a name="step-3"></a>Krok 3

Teraz, Ustaliliśmy jest za mało miejsca portu firma Microsoft można filtrować naszych przechwytywania na podstawie portu.

Filtr w tym scenariuszu należy:

```
tcp.port == 111
```

Firma Microsoft wprowadź tekst filtr z powyższych w polu tekstowym filtru i naciśnij klawisz enter.

![Rysunek 6.][6]

Spośród wyników możemy można zauważyć, że cały ruch jest pochodzi z lokalnej maszyny wirtualnej w tej samej podsieci. Jeśli nadal nie Rozumiemy Dlaczego występuje ten ruch, możemy dalsze inspekcję pakietów w celu ustalenia, dlaczego jest wprowadzenie tych połączeń na porcie 111. Dzięki tym informacjom możemy podejmij odpowiednie działanie.

## <a name="next-steps"></a>Następne kroki

Więcej informacji na temat innych funkcji diagnostycznych obserwatora sieciowego odwiedzając [omówienie monitorowania sieci platformy Azure](network-watcher-monitoring-overview.md)

[1]: ./media/network-watcher-deep-packet-inspection/figure1.png
[2]: ./media/network-watcher-deep-packet-inspection/figure2.png
[3]: ./media/network-watcher-deep-packet-inspection/figure3.png
[4]: ./media/network-watcher-deep-packet-inspection/figure4.png
[5]: ./media/network-watcher-deep-packet-inspection/figure5.png
[6]: ./media/network-watcher-deep-packet-inspection/figure6.png
[7]: ./media/network-watcher-deep-packet-inspection/figure7.png
[8]: ./media/network-watcher-deep-packet-inspection/figure8.png













