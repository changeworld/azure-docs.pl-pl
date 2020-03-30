---
title: Inspekcja pakietów za pomocą usługi Azure Network Watcher | Dokumenty firmy Microsoft
description: W tym artykule opisano sposób używania obserwatora sieciowego do przeprowadzania inspekcji pakietów zebranych z maszyny Wirtualnej
services: network-watcher
documentationcenter: na
author: damendo
ms.assetid: 7b907d00-9c35-40f5-a61e-beb7b782276f
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: damendo
ms.openlocfilehash: 7d32043ca73e9cf810b3eab5e65cb4b42b599d18
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77152928"
---
# <a name="packet-inspection-with-azure-network-watcher"></a>Inspekcja pakietów za pomocą obserwatora sieci platformy Azure

Za pomocą funkcji przechwytywania pakietów network watcher, można zainicjować i zarządzać przechwytuje sesji na maszynach wirtualnych platformy Azure z portalu, PowerShell, CLI i programowo za pośrednictwem interfejsu API SDK i REST. Przechwytywanie pakietów umożliwia adresowanie scenariuszy, które wymagają danych na poziomie pakietu, dostarczając informacje w formacie łatwo użytecznym. Korzystając z dostępnych swobodnie narzędzi do sprawdzania danych, można sprawdzić komunikację wysyłaną do i z maszyn wirtualnych i uzyskać wgląd w ruch sieciowy. Przykładowe zastosowania danych przechwytywania pakietów obejmują: badanie problemów z siecią lub aplikacjami, wykrywanie niewłaściwego użycia sieci i prób włamania lub zachowanie zgodności z przepisami. W tym artykule pokazujemy, jak otworzyć plik przechwytywania pakietów dostarczony przez Network Watcher za pomocą popularnego narzędzia open source. Przedstawimy również przykłady pokazujące sposób obliczania opóźnienia połączenia, identyfikowania nieprawidłowego ruchu i analizowania statystyk sieciowych.

## <a name="before-you-begin"></a>Przed rozpoczęciem

W tym artykule oprzeszono niektóre wstępnie skonfigurowane scenariusze przechwytywania pakietów, który został uruchomiony wcześniej. Te scenariusze ilustrują możliwości, do których można uzyskać dostęp, przeglądając przechwytywanie pakietów. W tym scenariuszu używa [WireShark](https://www.wireshark.org/) do sprawdzenia przechwytywania pakietów.

W tym scenariuszu założono, że już uruchomiono przechwytywanie pakietów na maszynie wirtualnej. Aby dowiedzieć się, jak utworzyć przechwytywanie pakietów odwiedź [Zarządzanie przechwytywaniem pakietów za pomocą portalu](network-watcher-packet-capture-manage-portal.md) lub za pomocą REST, odwiedzając zarządzanie [przechwytywaniem pakietów za pomocą interfejsu API REST](network-watcher-packet-capture-manage-rest.md).

## <a name="scenario"></a>Scenariusz

W tym scenariuszu:

* Przeglądanie przechwytywania pakietów

## <a name="calculate-network-latency"></a>Obliczanie opóźnienia sieci

W tym scenariuszu pokażemy, jak wyświetlić początkową czas podróży w obie strony (RTT) konwersacji protokołu TCP (Transmission Control Protocol) występującej między dwoma punktami końcowymi.

Po nawiązaniu połączenia TCP pierwsze trzy pakiety wysyłane w połączeniu są zgodne ze wzorcem powszechnie nazywanym trójkierunkowym uzgadniania. Badając pierwsze dwa pakiety wysłane w tym uzgadnianiu, początkowe żądanie od klienta i odpowiedź z serwera, możemy obliczyć opóźnienie po ustanowieniu tego połączenia. To opóźnienie jest określane jako czas podróży w obie strony (RTT). Aby uzyskać więcej informacji na temat protokołu TCP i uzgadniania trójkierunkowego, zapoznaj się z następującym zasobem. [https://support.microsoft.com/en-us/help/172983/explanation-of-the-three-way-handshake-via-tcp-ip](https://support.microsoft.com/en-us/help/172983/explanation-of-the-three-way-handshake-via-tcp-ip)

### <a name="step-1"></a>Krok 1

Uruchom WireShark

### <a name="step-2"></a>Krok 2

Załaduj plik **.cap** z przechwytywania pakietów. Ten plik można znaleźć w obiekcie blob został zapisany w naszym lokalnie na maszynie wirtualnej, w zależności od sposobu skonfigurowania go.

### <a name="step-3"></a>Krok 3

Aby wyświetlić początkowy czas podróży w obie strony (RTT) w rozmowach TCP, będziemy patrzeć tylko na pierwsze dwa pakiety zaangażowane w uzgadnianie TCP. Będziemy używać pierwszych dwóch pakietów w trójdrożnym uścisku dłoni, które są pakietami [SYN], [SYN, ACK]. Są one nazwane dla flag ustawionych w nagłówku TCP. Ostatni pakiet w uzgadnianiu, pakiet [ACK], nie będzie używany w tym scenariuszu. Pakiet [SYN] jest wysyłany przez klienta. Po odebraniu serwer wysyła pakiet [ACK] jako potwierdzenie otrzymania SYN od klienta. Wykorzystując fakt, że odpowiedź serwera wymaga bardzo niewielkich nakładów, obliczamy RTT, odejmując czas odebrania przez klienta pakietu [SYN, ACK] do czasu wysłania przez klienta pakietu [SYN].

Za pomocą WireShark ta wartość jest obliczana dla nas.

Aby łatwiej wyświetlić pierwsze dwa pakiety w trójdrożnym uzgadnianiu TCP, wykorzystamy funkcję filtrowania zapewnianą przez WireShark.

Aby zastosować filtr w WireShark, rozwiń segment "Transmission Control Protocol" pakietu [SYN] w przechwytywaniu i sprawdź flagi ustawione w nagłówku TCP.

Ponieważ szukamy filtrowania wszystkich pakietów [SYN] i [SYN, ACK], pod flagami potwierdzają, że bit Syn jest ustawiony na 1, a następnie kliknij prawym przyciskiem myszy na bit Syn -> Zastosuj jako filtr -> Zaznaczone.

![rysunek 7][7]

### <a name="step-4"></a>Krok 4

Teraz, gdy filtrujesz okno, aby zobaczyć tylko pakiety z zestawem bitów [SYN], możesz łatwo wybrać rozmowy, które Cię interesują, aby wyświetlić początkowy RTT. Prosty sposób, aby wyświetlić RTT w WireShark po prostu kliknij rozwijaną oznaczoną "SEQ / ACK" analizy. Następnie pojawi się RTT. W tym przypadku RTT wynosił 0,0022114 sekundy lub 2,211 ms.

![rysunek 8][8]

## <a name="unwanted-protocols"></a>Niechciane protokoły

Możesz mieć wiele aplikacji uruchomionych w wystąpieniu maszyny wirtualnej, które zostały wdrożone na platformie Azure. Wiele z tych aplikacji komunikuje się za pośrednictwem sieci, być może bez wyraźnej zgody. Za pomocą przechwytywania pakietów do przechowywania komunikacji sieciowej, możemy zbadać, jak aplikacja mówi w sieci i szukać żadnych problemów.

W tym przykładzie przejrzymy poprzedni uruchomiony przechwytywanie pakietów dla niechcianych protokołów, które mogą wskazywać nieautoryzowaną komunikację z aplikacji uruchomionej na komputerze.

### <a name="step-1"></a>Krok 1

Używanie tego samego przechwytywania w poprzednim scenariuszu kliknij pozycję**Hierarchia protokołu** **statystyk** > 

![menu hierarchii protokołów][2]

Zostanie wyświetlene okno hierarchii protokołów. Ten widok zawiera listę wszystkich protokołów, które były używane podczas sesji przechwytywania oraz liczbę pakietów przesyłanych i odbieranych za pomocą protokołów. Ten widok może być przydatny do znajdowania niechcianego ruchu sieciowego na maszynach wirtualnych lub w sieci.

![Otwarto hierarchię protokołów][3]

Jak widać na poniższym przechwycie ekranu, był ruch za pomocą protokołu BitTorrent, który jest używany do udostępniania plików peer to peer. Jako administrator nie spodziewasz się zobaczyć ruch BitTorrent na tej konkretnej maszyn wirtualnych. Teraz, gdy znasz ten ruch, możesz usunąć oprogramowanie równorzędne zainstalowane na tej maszynie wirtualnej lub zablokować ruch przy użyciu sieciowych grup zabezpieczeń lub zapory. Ponadto można wybrać uruchamianie przechwytywania pakietów zgodnie z harmonogramem, dzięki czemu można regularnie przeglądać użycie protokołu na maszynach wirtualnych. Na przykład, jak zautomatyzować zadania sieciowe w witrynie Azure visit [Monitor zasobów sieciowych z automatyzacji platformy Azure](network-watcher-monitor-with-azure-automation.md)

## <a name="finding-top-destinations-and-ports"></a>Znajdowanie najlepszych miejsc docelowych i portów

Opis typów ruchu, punktów końcowych i portów komunikowanych za pośrednictwem jest ważne podczas monitorowania lub rozwiązywania problemów aplikacji i zasobów w sieci. Wykorzystując plik przechwytywania pakietów z góry, możemy szybko dowiedzieć się, z najlepszych miejsc docelowych, z których komunikuje się nasza maszyna wirtualna, oraz z portami, z których korzystamy.

### <a name="step-1"></a>Krok 1

Korzystanie z tego samego przechwytywania w poprzednim scenariuszu kliknij **opcję Statystyczne miejsca** > **docelowe i porty**  > **statystyki IPv4**

![okno przechwytywania pakietów][4]

### <a name="step-2"></a>Krok 2

Gdy przeglądamy wyniki linii wyróżnia, było wiele połączeń na porcie 111. Najczęściej używanym portem był 3389, który jest pulpitem zdalnym, a pozostałe to porty dynamiczne RPC.

Chociaż ten ruch może nic nie znaczyć, jest to port, który był używany dla wielu połączeń i jest nieznany administratorowi.

![rysunek 5][5]

### <a name="step-3"></a>Krok 3

Teraz, gdy ustaliliśmy port nie na miejscu, możemy filtrować nasze przechwytywanie w oparciu o port.

Filtr w tym scenariuszu będzie:

```
tcp.port == 111
```

Wprowadzamy tekst filtru z góry w pole tekstowe filtru i naciśnij enter.

![rysunek 6][6]

Na podstawie wyników widzimy cały ruch pochodzący z lokalnej maszyny wirtualnej w tej samej podsieci. Jeśli nadal nie rozumiemy, dlaczego ten ruch występuje, możemy dodatkowo sprawdzić pakiety, aby ustalić, dlaczego wywołuje te połączenia na porcie 111. Dzięki tym informacjom możemy podjąć odpowiednie działania.

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o innych funkcjach diagnostycznych funkcji Obserwatora sieci, odwiedzając [omówienie monitorowania sieci platformy Azure](network-watcher-monitoring-overview.md)

[1]: ./media/network-watcher-deep-packet-inspection/figure1.png
[2]: ./media/network-watcher-deep-packet-inspection/figure2.png
[3]: ./media/network-watcher-deep-packet-inspection/figure3.png
[4]: ./media/network-watcher-deep-packet-inspection/figure4.png
[5]: ./media/network-watcher-deep-packet-inspection/figure5.png
[6]: ./media/network-watcher-deep-packet-inspection/figure6.png
[7]: ./media/network-watcher-deep-packet-inspection/figure7.png
[8]: ./media/network-watcher-deep-packet-inspection/figure8.png













