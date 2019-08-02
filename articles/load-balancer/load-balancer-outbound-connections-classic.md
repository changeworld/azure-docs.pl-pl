---
title: Połączenia wychodzące na platformie Azure (klasyczne)
titlesuffix: Azure Load Balancer
description: W tym artykule wyjaśniono, jak platforma Azure umożliwia usługom w chmurze komunikowanie się z publicznymi usługami internetowymi.
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.custom: seodec18
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/13/2018
ms.author: allensu
ms.openlocfilehash: 10af3b4838aae1565bac1d996997c117a74cedbc
ms.sourcegitcommit: 9a699d7408023d3736961745c753ca3cec708f23
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/16/2019
ms.locfileid: "68274662"
---
# <a name="outbound-connections-classic"></a>Połączenia wychodzące (klasyczne)

Platforma Azure zapewnia łączność wychodzącą dla wdrożeń klientów przy użyciu kilku różnych mechanizmów. W tym artykule opisano scenariusze, w których są one stosowane, jak działają i jak nimi zarządzać.

>[!NOTE]
>W tym artykule omówiono tylko wdrożenia klasyczne.  Przejrzyj [połączenia](load-balancer-outbound-connections.md) wychodzące dla wszystkich scenariuszy wdrażania Menedżer zasobów na platformie Azure.

Wdrożenie na platformie Azure może komunikować się z punktami końcowymi poza platformą Azure w publicznej przestrzeni adresów IP. Gdy wystąpienie inicjuje przepływ wychodzący do miejsca docelowego w publicznej przestrzeni adresów IP, platforma Azure dynamicznie mapuje prywatny adres IP na publiczny adres IP. Po utworzeniu tego mapowania ruch powrotny dla tego wychodzącego przepływu może również dotrzeć do prywatnego adresu IP, z którego pochodzi przepływ.

Na platformie Azure do wykonania tej funkcji jest stosowany translator adresów sieciowych (Resources). Gdy wiele prywatnych adresów IP jest rozliczanych za pojedynczym publicznym adresem IP, platforma Azure używa [translatora adresów portu (Binding)](#pat) do zamaskowanego prywatnych adresów IP. Porty tymczasowe są używane na potrzeby usługi i są [wstępnie przydzieloną](#preallocatedports) na podstawie rozmiaru puli.

Istnieje wiele [scenariuszy wychodzących](#scenarios). Te scenariusze można połączyć zgodnie z wymaganiami. Uważnie przejrzyj je, aby zrozumieć możliwości, ograniczenia i wzorce, które mają zastosowanie do modelu wdrożenia i scenariusza aplikacji. Zapoznaj się ze wskazówkami dotyczącymi [zarządzania tymi scenariuszami](#snatexhaust).

## <a name="scenarios"></a>Omówienie scenariusza

Platforma Azure oferuje trzy różne metody do osiągnięcia klasycznych wdrożeń łączności z ruchem wychodzącym.  Nie wszystkie klasyczne wdrożenia mają dostępne wszystkie trzy scenariusze:

| Scenariusz | Metoda | Protokoły IP | Opis | Rola proces roboczy sieci Web | IaaS | 
| --- | --- | --- | --- | --- | --- |
| [1. Maszyna wirtualna z publicznym adresem IP na poziomie wystąpienia](#ilpip) | Reportcy adresów sieciowych, nie użyto zamaskowanego portu | TCP, UDP, ICMP, ESP | Platforma Azure używa maszyny wirtualnej z przypisaną publicznym adresem IP. Dla tego wystąpienia są dostępne wszystkie porty tymczasowe. | Nie | Tak |
| [2. publiczny punkt końcowy o zrównoważonym obciążeniu](#publiclbendpoint) | Reportcy adresów sieciowych z zamaskowanem portów do publicznego punktu końcowego | TCP, UDP | Platforma Azure udostępnia publiczny publiczny adres IP z wieloma prywatnymi punktami końcowymi. Platforma Azure używa tymczasowych portów publicznego punktu końcowego w celu uzyskania dostępu. | Yes | Tak |
| [3. Autonomiczna maszyna wirtualna](#defaultsnat) | Reportcy adresów sieciowych z zamaskowanem portów | TCP, UDP | Platforma Azure automatycznie wyznacza publiczny adres IP dla protokołu reportowego, udostępnia ten publiczny adres IP w całym wdrożeniu i używa tymczasowych portów publicznego adresu IP dla usługi. Jest to scenariusz rezerwowy dla powyższych scenariuszy. Nie zalecamy jej, jeśli potrzebujesz widoczności i kontroli. | Yes | Yes |

Jest to podzbiór funkcji połączenia wychodzącego dostępna dla wdrożeń Menedżer zasobów na platformie Azure.  

Różne wdrożenia klasyczne mają różne funkcje:

| Wdrożenie klasyczne | Dostępna funkcja | 
| --- | --- |
| Maszyna wirtualna | scenariusz [1](#ilpip), [2](#publiclbendpoint)lub [3](#defaultsnat) |
| Rola proces roboczy sieci Web | scenariusz [2](#publiclbendpoint), [3](#defaultsnat) | 

[Strategie zaradcze](#snatexhaust) mają także takie same różnice.

Algorytm używany do prealokowania portów tymczasowych dla usługi w przypadku wdrożeń klasycznych jest taki sam jak w przypadku wdrożeń zasobów Azure Resource Manager.

### <a name="ilpip"></a>Scenariusz 1: Maszyna wirtualna z publicznym adresem IP na poziomie wystąpienia

W tym scenariuszu maszyna wirtualna ma przypisany publiczny adres IP na poziomie wystąpienia (ILPIP). Jeśli chodzi o połączenia wychodzące, nie ma znaczenia, czy maszyna wirtualna ma zrównoważony punkt końcowy. Ten scenariusz ma pierwszeństwo przed innymi. Gdy ILPIP jest używany, maszyna wirtualna używa ILPIP dla wszystkich przepływów wychodzących.  

Publiczny adres IP przypisany do maszyny wirtualnej jest relacją 1:1 (a nie 1: wiele) i zaimplementowaną jako bezstanowe urządzenie NAT 1:1.  Nie jest używane zamaskowane portów, a maszyna wirtualna ma dostęp do wszystkich tymczasowych portów do użycia.

Jeśli aplikacja inicjuje wiele przepływów wychodzących i wyczerpanie portów wiązania adresów sieciowych, należy rozważyć przypisanie [ILPIP do ograniczania ograniczeń dotyczących translatora adresów sieciowych](#assignilpip). Zapoznaj się z [zarządzaniem wyczerpaniem spalin](#snatexhaust) w całości.

### <a name="publiclbendpoint"></a>Scenariusz 2: Publiczny punkt końcowy o zrównoważonym obciążeniu

W tym scenariuszu rola maszyny wirtualnej lub procesu roboczego sieci Web jest skojarzona z publicznym adresem IP za pomocą punktu końcowego ze zrównoważonym obciążeniem. Do maszyny wirtualnej nie jest przypisany publiczny adres IP. 

Gdy maszyna wirtualna ze zrównoważonym obciążeniem tworzy przepływ wychodzący, platforma Azure tłumaczy prywatny adres IP przepływu wychodzącego na publiczny adres IP publicznego punktu końcowego ze zrównoważonym obciążeniem. Aby wykonać tę funkcję, platforma Azure używa tego programu. Platforma Azure używa [](#pat) również usługi, aby zamaskowane wiele prywatnych adresów IP za publicznym adresem IP. 

Tymczasowe porty frontonu publicznego adresu IP modułu równoważenia obciążenia są używane do rozróżniania poszczególnych przepływów pochodzących z maszyny wirtualnej. W przypadku tworzenia przepływów ruchu wychodzącego w ramach strumienia danych dynamicznie [przydzielono wstępnie przydzieloną porty](#preallocatedports) tymczasowe. W tym kontekście porty, które są używane do przystawcy adresów sieciowych, są nazywane portami.

Porty przydziałów adresów sieciowych są wstępnie przydzielonymi, zgodnie z opisem w sekcji [Omówienie adresów i](#snat) elementów. Są to skończone zasoby, które mogą być wyczerpane. Ważne jest, aby zrozumieć, jak są [używane](#pat). Aby dowiedzieć się, jak projektować w tym zużyciu i w razie potrzeby rozwiązać problem, przejrzyj [Zarządzanie wyczerpaniem](#snatexhaust)zasobów.

Gdy istnieje [wiele publicznych punktów końcowych](load-balancer-multivip.md) ze zrównoważonym obciążeniem, każdy z tych publicznych adresów IP jest kandydatem dla przepływów wychodzących, a jeden jest wybierany losowo.  

### <a name="defaultsnat"></a>Scenariusz 3: Brak skojarzonego publicznego adresu IP

W tym scenariuszu rola maszyny wirtualnej lub procesu roboczego sieci Web nie jest częścią publicznego punktu końcowego ze zrównoważonym obciążeniem.  A w przypadku maszyny wirtualnej nie ma przypisanego adresu ILPIP. Gdy maszyna wirtualna tworzy przepływ wychodzący, platforma Azure tłumaczy prywatny adres IP przepływu wychodzącego na publiczny źródłowy adres IP. Publiczny adres IP używany dla tego przepływu wychodzącego nie jest konfigurowalny i nie jest uwzględniany w limicie limitu zasobów publicznego adresu IP subskrypcji.  Platforma Azure automatycznie przydziela ten adres.

Aby można było wykonać tę funkcję, Platforma[](#pat)Azure używa podszywającego się na portach. Ten scenariusz jest podobny do scenariusza 2, z tą różnicą, że nie ma kontroli nad używanym adresem IP. Jest to scenariusz rezerwowy dla sytuacji, gdy scenariusze 1 i 2 nie istnieją. Nie zalecamy tego scenariusza, jeśli chcesz kontrolować adres wychodzący. Jeśli połączenia wychodzące są krytyczną częścią aplikacji, należy wybrać inny scenariusz.

Porty przydziałów adresów sieciowych są wstępnie przydzielonymi, zgodnie z opisem w sekcji [Omówienie adresów i](#snat) elementów.  Liczba ról maszyn wirtualnych lub procesów roboczych sieci Web współużytkujących publiczny adres IP określa liczbę wstępnie alokowanych portów tymczasowych.   Ważne jest, aby zrozumieć, jak są [używane](#pat). Aby dowiedzieć się, jak projektować w tym zużyciu i w razie potrzeby rozwiązać problem, przejrzyj [Zarządzanie wyczerpaniem](#snatexhaust)zasobów.

## <a name="snat"></a>Informacje o porozumieniu i niepomyślnym

### <a name="pat"></a>Przymaskowany port do podszywającego się

Gdy wdrożenie wykonuje połączenie wychodzące, każde źródło połączenia wychodzącego jest ponownie zapisywane. Źródło jest ponownie zapisywane z prywatnego obszaru adresów IP na publiczny adres IP skojarzony z wdrożeniem (na podstawie scenariuszy opisanych powyżej). W publicznej przestrzeni adresowej IP, 5-krotka przepływu (źródłowy adres IP, port źródłowy, protokół transportu IP, docelowy adres IP, port docelowy) musi być unikatowa.  

Porty tymczasowe (porty IP) są używane do osiągnięcia tego po zapisaniu prywatnego źródłowego adresu IP, ponieważ wiele przepływów pochodzi z jednego publicznego adresu IP. 

Jeden port strumienia adresów sieciowych jest zużywany na przepływ do jednego docelowego adresu IP, portu i protokołu. W przypadku wielu przepływów na ten sam docelowy adres IP, port i protokół każdy przepływ wykorzystuje jeden port. Gwarantuje to, że przepływy są unikatowe, gdy pochodzą z tego samego publicznego adresu IP i przejdź do tego samego docelowego adresu IP, portu i protokołu. 

Wiele przepływów, każdy do innego docelowego adresu IP, portu i protokołu, współużytkuje jeden port. Docelowy adres IP, port i protokół sprawiają, że przepływy są unikatowe bez konieczności stosowania dodatkowych portów źródłowych do rozróżnienia przepływów w publicznej przestrzeni adresów IP.

W przypadku wyczerpania zasobów portów współdziałania przepływy wychodzące kończą się niepowodzeniem, dopóki istniejące przepływy nie zwolnią portów. Load Balancer przejmowanie portów protokołu równorzędnego, gdy przepływ zostanie zamknięty i korzysta z [4-minutowego limitu czasu bezczynności](#idletimeout) w przypadku odzyskiwania portów ze STRUMIENIAMI adresów sieciowych z bezczynnych przepływów.

Aby uzyskać wzorce ograniczające warunki, które zwykle prowadzą do wyczerpania portów, przejrzyj sekcję [Zarządzanie](#snatexhaust) reportem adresów sieciowych.

### <a name="preallocatedports"></a>Wstępne przydzielanie portów tymczasowych dla podszywającego się portu

Platforma Azure używa algorytmu do określenia liczby wstępnie przyalokowanych portów przydziałów adresów sieciowych, które są dostępne na podstawie rozmiaru puli zaplecza przy użyciu[](#pat)podzestawu adresów sieciowych z zamaskowanem portem. Porty IP to tymczasowe porty dostępne dla określonego publicznego adresu IP.

Platforma Azure wstępnie przydziela porty do adresów IP, gdy wystąpienie jest wdrożone na podstawie liczby wystąpień roli maszyny wirtualnej lub procesu roboczego sieci Web, które korzystają z danego publicznego adresu.  Gdy przepływy wychodzące [](#pat) są tworzone, za pomocą tego portu są dynamicznie zużywane (do wstępnie przydzielonych limitów) i zwalniają te porty, gdy następuje zamknięcie przepływu lub przekroczenie limitu czasu bezczynności.

W poniższej tabeli przedstawiono alokacje wstępne portów adresów sieciowych dla warstw rozmiaru puli zaplecza:

| Wystąpienia | Wstępnie przydzielony porty dla każdego wystąpienia |
| --- | --- |
| 1-50 | 1,024 |
| 51-100 | 512 |
| 101-200 | 256 |
| 201-400 | 128 |

Należy pamiętać, że liczba dostępnych portów nie jest tłumaczona bezpośrednio na liczbę przepływów. Można ponownie użyć pojedynczego portu źródłowego dla wielu unikatowych miejsc docelowych. Porty są używane tylko wtedy, gdy konieczne jest przeprowadzenie unikatowych przepływów. Aby zapoznać się z zaleceniami dotyczącymi projektowania i rozwiązywania problemów, zapoznaj się z sekcją [jak zarządzać tym zasobem exhaustible](#snatexhaust) i sekcją opisującą. [](#pat)

Zmiana rozmiaru wdrożenia może mieć wpływ na niektóre z określonych przepływów. Jeśli rozmiar puli zaplecza zwiększy się i przejdzie do następnej warstwy, połowa wstępnie przydzielonego portu ze współdzielonym zestawem danych jest odzyskiwana podczas przejścia do następnej większej warstwy puli zaplecza. Przepływy skojarzone z odwiązanym portem wiązania adresów sieciowych przekroczą limit czasu i muszą zostać ponownie nawiązane. Jeśli zostanie podjęta próba nowego przepływu, przepływ zakończy się pomyślnie, o ile dostępne są wstępnie przydzieloną porty.

Jeśli rozmiar wdrożenia zmniejsza się i przechodzi do niższej warstwy, zwiększa się liczba dostępnych portów. W takim przypadku nie ma to wpływ na istniejące przyłączone porty i ich przepływy.

Jeśli usługa w chmurze jest ponownie wdrażana lub zmieniana, infrastruktura może tymczasowo zgłosić pulę zaplecza tak, aby była większa od wartości rzeczywistej, a na platformie Azure zostanie wydzielona mniejsza liczba portów podrzędnego ruchu źródłowego na wystąpienie.  Może to spowodować tymczasowe zwiększenie prawdopodobieństwa wyczerpania portów przez translatora adresów sieciowych. Ostatecznie rozmiar puli zostanie zmieniony na rozmiar rzeczywisty, a platforma Azure automatycznie zwiększy wstępnie przydzieloną liczbę portów podzestawów adresów sieciowych do oczekiwanej liczby zgodnie z powyższą tabelą.  To zachowanie jest zaprojektowane i nie można go konfigurować.

Alokacje portów przydziałów adresów IP są zależne od protokołu NNTP (TCP i UDP są obsługiwane oddzielnie) i są publikowane w następujących warunkach:

### <a name="tcp-snat-port-release"></a>Wydanie portu źródłowego protokołu TCP

- Jeśli zarówno serwer, jak i klient wysyłają polecenie FIN/ACK, port z podsiecią zostanie wydaną po 240 sekundach.
- Jeśli jest widoczny parametr RST, port elementu poddanego zostanie wystawiony po 15 sekundach.
- Osiągnięto limit czasu bezczynności

### <a name="udp-snat-port-release"></a>Wydanie portu przychodzący protokołu UDP

- Osiągnięto limit czasu bezczynności

## <a name="problemsolving"></a>Rozwiązywanie problemów 

Ta sekcja ma pomóc w eliminowaniu wyczerpania i innych scenariuszy, które mogą wystąpić w przypadku połączeń wychodzących na platformie Azure.

### <a name="snatexhaust"></a>Zarządzanie wyczerpaniem portów (z)
[Porty](#preallocatedports) tymczasowe używane do [](#pat) nałożenia są exhaustible zasobów, zgodnie z opisem w artykule [Brak skojarzonego publicznego adresu IP](#defaultsnat) i [publicznego punktu końcowego](#publiclbendpoint)ze zrównoważonym obciążeniem.

Jeśli wiesz, że masz wiele wychodzących połączeń TCP lub UDP z tym samym docelowym adresem IP i portem, możesz zauważyć, że połączenia wychodzące kończą się niepowodzeniem lub są zalecane przez pomoc techniczną w przypadku wyczerpania portów przydzielonej liczby [portów tymczasowych](#preallocatedports) używane przezbinding) masz kilka ogólnych opcji zaradczych. [](#pat) Zapoznaj się z tymi opcjami i zdecyduj, co jest dostępne i najlepsze dla Twojego scenariusza. Istnieje możliwość, że co najmniej jedna może pomóc w zarządzaniu tym scenariuszem.

Jeśli masz problemy z zrozumieniem zachowania połączenia wychodzącego, możesz użyć statystyk stosu IP (netstat). Może być również pomocne w obserwowanie zachowań połączenia za pomocą przechwytywania pakietów.

#### <a name="connectionreuse"></a>Modyfikowanie aplikacji w celu ponownego użycia połączeń 
Można zmniejszyć zapotrzebowanie na porty tymczasowe, które są używane w przypadku usługi reportcy, przez ponowną obsługę połączeń w aplikacji. Jest to szczególnie prawdziwe w przypadku protokołów takich jak HTTP/1.1, w przypadku których użycie połączenia jest domyślne. Inne protokoły używające protokołu HTTP jako transportu (na przykład REST) mogą korzystać z tej usługi. 

Ponowne użycie jest zawsze lepsze niż pojedyncze, niepodzielne połączenia TCP dla każdego żądania. Wielokrotne użycie powoduje skuteczniejsze, bardzo wydajne transakcje TCP.

#### <a name="connection pooling"></a>Modyfikowanie aplikacji tak, aby używała puli połączeń
Można użyć schematu puli połączeń w aplikacji, w którym żądania są dystrybuowane wewnętrznie w ramach ustalonego zestawu połączeń (z każdym z nich, gdzie to możliwe). Ten schemat ogranicza liczbę używanych portów tymczasowych i tworzy bardziej przewidywalne środowisko. Ten schemat może również zwiększyć przepływność żądań przez umożliwienie wielu równoczesnych operacji, gdy jedno połączenie blokuje odpowiedzi na odpowiedź operacji.  

Pule połączeń mogą już istnieć w ramach platformy, która jest używana do tworzenia aplikacji lub ustawień konfiguracji aplikacji. Pulę połączeń można połączyć z wielokrotnym użyciem połączenia. Wiele żądań korzysta z ustalonej, przewidywalnej liczby portów na ten sam docelowy adres IP i port. Żądania korzystają również z efektywnego wykorzystania transakcji TCP redukujących opóźnienia i wykorzystanie zasobów. Transakcje UDP mogą również być korzystne, ponieważ zarządzanie liczbą przepływów UDP może mieć na celu uniknięcie przekroczenia warunków i zarządzanie wykorzystaniem portów przystawek.

#### <a name="retry logic"></a>Modyfikowanie aplikacji tak, aby była używana mniej agresywna logika ponawiania
Gdy [wstępnie alokowany porty](#preallocatedports) tymczasowe są [](#pat) używane na potrzeby odbierania lub występują błędy aplikacji, agresywne lub bezproblemowe wymuszanie ponawiania prób bez pozostałego działania lub logiki wycofywania. Możesz zmniejszyć zapotrzebowanie na tymczasowe porty przy użyciu mniej agresywnej logiki ponawiania. 

Porty tymczasowe mają 4-minutowy limit czasu bezczynności (nieregulowane). Jeśli ponowna próba jest zbyt agresywna, wyczerpanie nie ma możliwości samodzielnego wyczyszczenia. W związku z tym rozważać, jak i jak często — transakcje ponawiania prób aplikacji są istotną częścią projektu.

#### <a name="assignilpip"></a>Przypisz publiczny adres IP na poziomie wystąpienia do każdej maszyny wirtualnej
Przypisanie ILPIP zmienia swój scenariusz na [publiczny adres IP na poziomie wystąpienia na maszynę wirtualną](#ilpip). Wszystkie tymczasowe porty publicznego adresu IP, które są używane dla każdej maszyny wirtualnej, są dostępne dla maszyny wirtualnej. (W przeciwieństwie do scenariuszy, w których porty tymczasowe publicznego adresu IP są udostępniane wszystkim maszynom wirtualnym skojarzonym z odpowiednim wdrożeniem). Istnieją kompromisy, które należy wziąć pod uwagę, takie jak potencjalny wpływ listy dozwolonych dużej liczby pojedynczych adresów IP.

>[!NOTE] 
>Ta opcja nie jest dostępna dla ról procesów roboczych sieci Web.

### <a name="idletimeout"></a>Aby zresetować limit czasu bezczynności dla ruchu wychodzącego, użyj utrzymywania aktywności

Połączenia wychodzące mają 4-minutowy limit czasu bezczynności. Ten limit czasu nie jest regulowany. Można jednak użyć transportu (na przykład aktywności protokołu TCP) lub nieaktywności warstwy aplikacji, aby odświeżyć bezczynny przepływ i zresetować ten limit czasu bezczynności w razie potrzeby.  Należy skontaktować się z dostawcą każdego spakowanego oprogramowania, które jest obsługiwane lub jak je włączyć.  Zwykle tylko jedna strona musi generować utrzymywanie aktywności w celu zresetowania limitu czasu bezczynności. 

## <a name="discoveroutbound"></a>Odnajdywanie publicznego adresu IP używanego przez maszynę wirtualną
Istnieje wiele sposobów określenia publicznego źródłowego adresu IP połączenia wychodzącego. OpenDNS zapewnia usługę, która może wyświetlać publiczny adres IP maszyny wirtualnej. 

Za pomocą polecenia nslookup można wysłać zapytanie DNS dla nazwy myip.opendns.com do OpenDNS rozpoznawania nazw. Usługa zwraca źródłowy adres IP, który został użyty do wysłania zapytania. Po uruchomieniu następującego zapytania z maszyny wirtualnej odpowiedź jest publicznym adresem IP używanym dla tej maszyny wirtualnej:

    nslookup myip.opendns.com resolver1.opendns.com


## <a name="next-steps"></a>Kolejne kroki

- Dowiedz się więcej o [Load Balancer](load-balancer-overview.md) używanych w Menedżer zasobów wdrożeniach.
- Tryb informacji o scenariuszach [połączeń wychodzących](load-balancer-outbound-connections.md) dostępnych w ramach wdrożeń Menedżer zasobów.
