---
title: Inspekcja pakietów przy użyciu usługi Azure Network Watcher | Dokumentacja firmy Microsoft
description: W tym artykule opisano, jak Usługa Network Watcher umożliwia głębokiej inspekcji pakietów zebrane z maszyny Wirtualnej
services: network-watcher
documentationcenter: na
author: KumudD
manager: twooley
editor: ''
ms.assetid: 7b907d00-9c35-40f5-a61e-beb7b782276f
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: kumud
ms.openlocfilehash: 1c30e679b250c2d6c6b03a1fe5d70dbd06acf052
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60535716"
---
# <a name="packet-inspection-with-azure-network-watcher"></a>Inspekcja pakietów przy użyciu usługi Azure Network Watcher

Przy użyciu funkcji przechwytywania pakietów usługi Network Watcher, można zainicjować i zarządzanie sesjami przechwytywania na maszynach wirtualnych platformy Azure z poziomu portalu, programu PowerShell, interfejsu wiersza polecenia i programowo przy użyciu zestawu SDK i interfejsu API REST. Przechwytywanie pakietów umożliwia scenariuszy, które wymagają pakietu danych na poziomie poprzez dostarczanie informacji w formacie gotowy do użycia. Korzystanie z bezpłatnych narzędzi, aby sprawdzić dane, można zbadać wiadomości wysyłane do i z maszyn wirtualnych i uzyskiwanie szczegółowych informacji o ruchu sieciowym. Do niektórych zastosowań przykład danych przechwytywania pakietów obejmują: badanie problemów z siecią lub aplikacji, wykrywanie prób nieautoryzowanego dostępu, jak i nieuprawnione użycie sieci lub utrzymania zgodności z przepisami. W tym artykule przedstawiono, jak można otworzyć pliku przechwytywania pakietów, udostępniane przez usługi Network Watcher za pomocą narzędzia typu open source. Firma Microsoft udostępni również przykłady pokazujące sposób obliczania czas oczekiwania na połączenie, identyfikacji nietypowe ruchu i zbadać statystyk sieciowych.

## <a name="before-you-begin"></a>Przed rozpoczęciem

W tym artykule omówiono kilka wstępnie skonfigurowanych scenariuszy na przechwytywanie pakietów, która została wcześniej uruchomiona. Te scenariusze pokazują możliwości, które są dostępne, sprawdzając przechwytywania pakietów. W tym scenariuszu [WireShark](https://www.wireshark.org/) do kontroli przechwytywania pakietów.

W tym scenariuszu przyjęto założenie, że już uruchomione przechwytywania pakietów na maszynie wirtualnej. Aby dowiedzieć się, jak utworzyć odwiedziny przechwytywania pakietów [przechwytywać pakiety zarządzania za pomocą portalu](network-watcher-packet-capture-manage-portal.md) lub z użyciem usług REST, odwiedzając [Zarządzanie Przechwytywanie pakietów przy użyciu interfejsu API REST](network-watcher-packet-capture-manage-rest.md).

## <a name="scenario"></a>Scenariusz

W tym scenariuszu możesz:

* Przejrzyj przechwytywania pakietów

## <a name="calculate-network-latency"></a>Oblicz opóźnienie sieci

W tym scenariuszu przedstawiono sposób wyświetlania początkowej czasu obiegu (RTT) występujące między dwa punkty końcowe konwersacji Transmission Control Protocol (TCP).

Po nawiązaniu połączenia protokołu TCP, pierwsze trzy pakiety wysyłane przez połączenie wykonaj wzorzec, nazywaną trójstopniowego. Analizując pierwsze dwa pakiety przesyłane w tym uzgadnianie początkowego żądania z klienta i odpowiedź z serwera, możemy obliczyć opóźnienie podczas tego połączenia. Ten czas oczekiwania jest określany jako czas obiegu (RTT). Aby uzyskać więcej informacji na temat protokołu TCP i trójstopniowego zapoznaj się z następujących zasobów. https://support.microsoft.com/en-us/help/172983/explanation-of-the-three-way-handshake-via-tcp-ip

### <a name="step-1"></a>Krok 1

Launch WireShark

### <a name="step-2"></a>Krok 2

Obciążenia **.cap** plik z sieci przechwytywania pakietów. Ten plik można znaleźć w obiekcie blob został zapisany w naszym lokalnie na maszynie wirtualnej, w zależności od sposobu skonfigurowania.

### <a name="step-3"></a>Krok 3

Aby wyświetlić początkowy czas obiegu (RTT) konwersacje TCP, firma Microsoft będzie tylko można spojrzenie na pierwsze dwa pakiety związane z uzgadniania protokołu TCP. Użyjemy dwóch pierwszych pakietów trójstopniowego, które [SYN], [SYN i ACK] pakietów. Są one nazywane dla flag ustawionych w nagłówku protokołu TCP. W tym scenariuszu nie posłuży ostatnim pakiecie w uzgadnianie pakietu [ACK]. Pakiet [SYN] jest wysyłany przez klienta. Po odebraniu serwer wysyła pakiet [potwierdzenia] jako potwierdzenie odbieranie SYN od klienta. Korzystanie z faktu, że odpowiedź serwera wymaga niewielkim zapasem, możemy obliczyć RTT przez odjęcie ilości czasu [SYN, potwierdzenia] pakiet został odebrany przez klienta do czasu [SYN] pakiet został wysłany przez klienta.

Za pomocą programu WireShark ta wartość jest obliczana dla nas.

Aby łatwiej przeglądać pierwsze dwa pakiety w trójstopniowego TCP, firma Microsoft będzie korzystać z możliwości filtrowania, dostarczone przez program WireShark.

Aby zastosować filtr w WireShark, rozwiń Segment "Transmission Control Protocol" pakiet [SYN] w swojej przechwytywania i zbadać flag ustawionych w nagłówku protokołu TCP.

Ponieważ czekamy, aby odfiltrować wszystkie [SYN] i [SYN potwierdzenia] pakietów w obszarze flagi upewnij się, Syn bit jest ustawiona na 1, a następnie kliknij prawym przyciskiem bitu Syn -> Zastosuj jako filtr -> wybrany.

![Rysunek 7][7]

### <a name="step-4"></a>Krok 4

Teraz, gdy zostały przefiltrowane okna, aby było widać tylko pakiety z bitem [SYN], możesz łatwo wybrać rozmowy, które interesują Cię do wyświetlania RTT początkowej. Prosty sposób, aby wyświetlić RTT WireShark po prostu kliknij listę rozwijaną, oznaczone jako "SEQ/potwierdzenia" analizy. Następnie zobaczysz RTT wyświetlane. W tym przypadku RTT był 0.0022114 sekund, czyli 2.211 ms.

![Rysunek 8][8]

## <a name="unwanted-protocols"></a>Niechciane protokołów

Może mieć wiele aplikacji uruchomionych na wystąpieniu maszyny wirtualnej wdrożonej na platformie Azure. Wiele z tych aplikacji komunikują się za pośrednictwem sieci, może być bez jego jawnej zgody. Przy użyciu przechwytywania pakietów do przechowywania komunikacji sieciowej, firma Microsoft można zbadać jak rozmawiają w sieci i odszukaj wszelkie problemy w aplikacji.

W tym przykładzie analizujemy poprzedniego uruchomienia przechwytywania pakietów niechciane protokołów, które mogą wskazywać na nieautoryzowany komunikacji z aplikacji uruchomionej na komputerze.

### <a name="step-1"></a>Krok 1

Przy użyciu tego samego przechwytywania w poprzednim scenariuszu kliknij **statystyki** > **protokołu hierarchii**

![Protokół hierarchii menu][2]

Zostanie wyświetlone okno hierarchii protokołu. Ten widok zawiera listę wszystkich protokołów, które były używane podczas sesji przechwytywania i liczba pakietów wysłanych i odebranych przy użyciu protokołów. Ten widok jest przydatny do znajdowania niechciane ruch sieciowy na maszynach wirtualnych lub w sieci.

![Hierarchia protokołu otwarte][3]

Jak widać na poniższym zrzucie ekranu, było ruchu przy użyciu protokołu BitTorrent, który jest używany do udostępniania plików równorzędnych. Administrator usługi nie powinny być widoczne BitTorrent ruch w przypadku tego konkretnego maszyn wirtualnych. Teraz należy pamiętać o ten ruch, możesz usunąć oprogramowanie równorzędnych zainstalowanego na tej maszynie wirtualnej, lub blokować ruch przy użyciu sieciowych grup zabezpieczeń lub zapory. Ponadto może zdecydować się na uruchamianie przechwytywania pakietów zgodnie z harmonogramem, co pozwoli na przejrzenie protokół korzystać na maszynach wirtualnych regularnie. Na przykład dotyczące automatyzacji zadań sieci na platformie azure, odwiedź stronę [monitorowania zasobów sieciowych przy użyciu usługi azure automation](network-watcher-monitor-with-azure-automation.md)

## <a name="finding-top-destinations-and-ports"></a>Najpopularniejsze miejsca docelowe Znajdowanie i portów

Opis typów ruchu, punktów końcowych i porty przekazywane za pośrednictwem jest ważne podczas monitorowania i rozwiązywania problemów aplikacji i zasobów w sieci. Korzystanie z pliku przechwytywania pakietów z powyższych możemy szybko Poznaj Najpopularniejsze miejsca docelowe, które maszyny Wirtualnej komunikuje się z i porty wykorzystywane.

### <a name="step-1"></a>Krok 1

Przy użyciu tego samego przechwytywania w poprzednim scenariuszu kliknij **statystyki** > **statystyki protokołu IPv4** > **miejsc docelowych i portów**

![Okno przechwytywania pakietów][4]

### <a name="step-2"></a>Krok 2

Gdy spojrzymy za pośrednictwem wyników, który wyróżnia linię wystąpiło wiele połączeń na porcie 111. Został najczęściej używanych portu 3389, czyli pulpitu zdalnego, a pozostałe są dynamiczne porty RPC.

Ten ruch może oznaczać, że nic nie, ale jest port, który został użyty dla wielu połączeń i jest nieznany do administratora.

![Rysunek 5][5]

### <a name="step-3"></a>Krok 3

Teraz, gdy Ustaliliśmy poza miejscem portu można odfiltrować naszych przechwytywania oparty na porcie.

Filtr, w tym scenariuszu będzie następująca:

```
tcp.port == 111
```

Tekst filtru z powyższych możemy wprowadzić w polu tekstowym filtru i naciśnij klawisz enter.

![Rysunek 6.][6]

Na liście wyników można zauważyć, że cały ruch pochodzi z lokalnej maszyny wirtualnej w tej samej podsieci. Jeśli nadal nie wiemy, dlaczego występuje ten ruch, firma Microsoft dodatkowo sprawdź pakietów, aby ustalić, dlaczego robi tych wywołań na porcie 111. Dzięki tym informacjom możemy podejmij odpowiednie działanie.

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się więcej o innych funkcji diagnostycznych usługi Network Watcher, odwiedzając [sieci platformy Azure, monitorowanie — Przegląd](network-watcher-monitoring-overview.md)

[1]: ./media/network-watcher-deep-packet-inspection/figure1.png
[2]: ./media/network-watcher-deep-packet-inspection/figure2.png
[3]: ./media/network-watcher-deep-packet-inspection/figure3.png
[4]: ./media/network-watcher-deep-packet-inspection/figure4.png
[5]: ./media/network-watcher-deep-packet-inspection/figure5.png
[6]: ./media/network-watcher-deep-packet-inspection/figure6.png
[7]: ./media/network-watcher-deep-packet-inspection/figure7.png
[8]: ./media/network-watcher-deep-packet-inspection/figure8.png













