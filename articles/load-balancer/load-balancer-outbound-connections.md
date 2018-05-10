---
title: Połączeń wychodzących na platformie Azure | Dokumentacja firmy Microsoft
description: W tym artykule opisano, jak Azure umożliwia maszyny wirtualne do komunikowania się z publicznej usługi internetowe.
services: load-balancer
documentationcenter: na
author: KumudD
manager: jeconnoc
editor: ''
ms.assetid: 5f666f2a-3a63-405a-abcd-b2e34d40e001
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/08/2018
ms.author: kumud
ms.openlocfilehash: 5cff443ac3bbd89a2245e7adb21458ecc62fd494
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/10/2018
---
# <a name="outbound-connections-in-azure"></a>Połączeń wychodzących na platformie Azure

Platforma Azure udostępnia łączność wychodząca wdrożeń klienta za pomocą kilku różnych mechanizmów. W tym artykule opisano scenariusze są, gdy mają one zastosowanie, jak działają i jak nimi zarządzać.

>[!NOTE] 
>W tym artykule opisano tylko wdrożenia usługi Resource Manager. Przegląd [połączenia wychodzące (klasyczne)](load-balancer-outbound-connections-classic.md) dla wszystkich scenariuszy wdrożenia klasycznego na platformie Azure.

Wdrażanie na platformie Azure mogą komunikować się z punktami końcowymi poza Azure w ramach publicznej przestrzeni adresów IP. Gdy wystąpienie inicjuje przepływu wychodzącego do miejsca docelowego w publicznej przestrzeni adresów IP, Azure dynamicznie mapuje prywatnego adresu IP do publicznego adresu IP. Po utworzeniu tego mapowania zwracany ruchu dla tego ruchu wychodzącego przepływu zdalnych zapewnia także łączność prywatnego adresu IP pochodzenie przepływu.

Azure używa translatora adresów sieciowych źródła (SNAT) do tej funkcji. W przypadku wielu prywatnych adresów IP są maskaradę za jeden publiczny adres IP, korzysta z usługi Azure [portu translacji adresów (PAWEŁ)](#pat) do zamaskowane pod prywatnych adresów IP. Porty efemeryczne są używane do PAWEŁ i są [wstępnie przydzielonych](#preallocatedports) na podstawie rozmiaru puli.

Dostępnych jest wiele [scenariusze wychodzących](#scenarios). W razie potrzeby można połączyć tych scenariuszy. Przeglądać je uważnie, aby zrozumieć możliwości, ograniczenia i wzorce, ponieważ mają one zastosowanie do modelu wdrażania oraz scenariusz aplikacji. Przejrzyj wskazówki dotyczące [Zarządzanie tych scenariuszy](#snatexhaust).

>[!IMPORTANT] 
>Standardowa usługa równoważenia obciążenia wprowadzono nowe możliwości i różne zachowania do łączność wychodząca.   Na przykład [Scenariusz 3](#defaultsnat) nie istnieje, gdy występuje wewnętrzny standardowy moduł równoważenia obciążenia i wykonania różnych kroków należy podjąć.   Dokładnie Sprawdź ten cały dokument, aby zapoznać się z ogólną pojęcia i różnice między jednostki SKU.

## <a name="scenarios"></a>Omówienie scenariusza

Moduł równoważenia obciążenia Azure i powiązane zasoby są jawnie zdefiniowane podczas korzystania z [usługi Azure Resource Manager](#arm).  Platforma Azure udostępnia obecnie trzech różnych metod na osiągnięcie łączność wychodząca dla zasobów usługi Azure Resource Manager. 

| Scenariusz | Metoda | Protokoły IP | Opis |
| --- | --- | --- | --- |
| [1. Maszyna wirtualna o wystąpieniu poziomu publicznego adresu IP (z lub bez modułu równoważenia obciążenia)](#ilpip) | SNAT, port maskaradę nieużywane. | TCP, UDP, ICMP, ESP | Azure używa publicznego adresu IP, przypisane do konfiguracji IP karty sieciowej dla wystąpienia. Wystąpienie ma wszystkie dostępne porty efemeryczne. |
| [2. Publiczny moduł równoważenia obciążenia skojarzone z maszyny Wirtualnej (nie wystąpienia poziomu publicznego adresu IP w wystąpieniu)](#lb) | SNAT z portu maskaradę (PAT) przy użyciu frontends modułu równoważenia obciążenia | TCP, UDP |Publiczny adres IP publicznego frontends modułu równoważenia obciążenia Azure udostępnia wiele prywatnych adresów IP. Platforma Azure korzysta porty efemeryczne frontends do PAWEŁ. |
| [3. Maszyna wirtualna autonomiczne (Brak usługi równoważenia obciążenia, żaden adres publiczny adres IP na poziomie wystąpienia)](#defaultsnat) | SNAT z portu maskaradę (PAT) | TCP, UDP | Azure automatycznie wyznacza publicznego adresu IP dla SNAT współużytkuje ten publiczny adres IP z wielu prywatnych adresów IP zestawu dostępności i używa porty efemeryczne ten publiczny adres IP. Jest to rezerwowy scenariusz dla powyższych scenariuszy. Firma Microsoft nie zaleca, aby uzyskać wgląd i większą kontrolę. |

Jeśli nie chcesz maszyny Wirtualnej do komunikowania się z punktami końcowymi poza Azure w ramach publicznej przestrzeni adresów IP, można użyć grup zabezpieczeń sieci (NSG) w celu zablokowania dostępu zgodnie z potrzebami. Sekcja [uniemożliwia nawiązanie połączenia wychodzącego](#preventoutbound) omówiono bardziej szczegółowo grup NSG. Wskazówki dotyczące projektowania, wdrażania i zarządzania nimi sieci wirtualnej bez żadnych wychodzący dostęp wykracza poza zakres tego artykułu.

### <a name="ilpip"></a>Scenariusz 1: Maszyna wirtualna adres publiczny adres IP na poziomie wystąpienia

W tym scenariuszu maszyna wirtualna ma wystąpienie poziomu publicznego adresu IP (ILPIP) przypisane do niej. Jeśli chodzi o połączeń wychodzących, nie ma znaczenia, czy maszyna wirtualna jest równoważone, czy nie. Ten scenariusz ma pierwszeństwo przed jej innym osobom. W przypadku ILPIP maszyna wirtualna używa ILPIP dla wszystkich przepływów wychodzących.  

Publiczny IP przypisane do maszyny Wirtualnej jest 1:1 relacji (zamiast 1: duży zakres) oraz implementowany jako bezstanowych translatora adresów sieciowych. 1:1  Port zamaskowana (PAWEŁ) nie jest używany, a maszyna wirtualna ma wszystkie porty efemeryczne dostępne do użycia.

Aplikacja inicjuje wiele przepływów wychodzących i występują wyczerpania portu SNAT, warto rozważyć przypisywanie [ILPIP złagodzić ograniczenia SNAT](#assignilpip). Przegląd [wyczerpania Zarządzanie SNAT](#snatexhaust) w całości.

### <a name="lb"></a>Scenariusz 2: VM równoważeniem obciążenia bez wystąpienia poziomu publicznego adresu IP

W tym scenariuszu maszyna wirtualna jest częścią publicznego puli zaplecza modułu równoważenia obciążenia. Maszyna wirtualna nie ma publicznego adresu IP przypisane do niej. Zasób usługi równoważenia obciążenia musi być skonfigurowany z reguły modułu równoważenia obciążenia, aby utworzyć łącze między publicznego frontonu IP z puli zaplecza.

Jeśli nie wykonasz tę konfigurację reguły, zachowanie jest opisany w scenariuszu dla [autonomiczny maszynę Wirtualną za pomocą wystąpienia poziomu publiczny adres IP nie](#defaultsnat). Nie jest konieczne, aby reguła mogła mieć odbiornik roboczych w puli zaplecza sondy kondycji powiodło się.

Gdy maszyna wirtualna z równoważeniem obciążenia tworzy przepływu wychodzącego, Azure tłumaczy prywatnej źródłowy adres IP przepływu wychodzącego do publicznego adresu IP publiczny moduł równoważenia obciążenia serwera sieci Web. Platforma Azure korzysta SNAT tej funkcji. Używa również Azure [PAT](#pat) do zamaskowane pod wiele prywatnych adresów IP za pomocą publicznego adresu IP. 

Porty efemeryczne publicznego frontonu adresu IP usługi równoważenia obciążenia są używane do rozróżniania poszczególnych przepływów pochodzi przez maszynę Wirtualną. Dynamicznie używa SNAT [wstępnie przydzielonych portów tymczasowych](#preallocatedports) utworzenia przepływów wychodzących. W tym kontekście tymczasowych portów używanych do SNAT są nazywane SNAT portów.

Są wstępnie przydzielonych portów SNAT, zgodnie z opisem w [SNAT zrozumienia i PAWEŁ](#snat) sekcji. Są one ograniczone zasób, który może być wyczerpany. Ważne jest, aby zrozumieć, jak są one [używane](#pat). Aby zrozumieć sposób projektowania dla tego zużycia i ograniczyć odpowiednio do potrzeb, przejrzyj [wyczerpania Zarządzanie SNAT](#snatexhaust).

Gdy [wielu adresów IP (publicznymi) są skojarzone z podstawowe usługi równoważenia obciążenia](load-balancer-multivip-overview.md), wszystkie te publiczne adresy IP są adresy [kandydatem do przepływów wychodzących](#multivipsnat), i wybrać jedną.  

Aby monitorować kondycję połączenia wychodzące z podstawowe usługi równoważenia obciążenia, można użyć [analizy dzienników dla usługi równoważenia obciążenia](load-balancer-monitor-log.md) i [alertów dzienniki zdarzeń](load-balancer-monitor-log.md#alert-event-log) do monitorowania SNAT portu wyczerpania komunikatów.

### <a name="defaultsnat"></a>Scenariusz 3: Autonomiczny VM bez wystąpienia poziomu publicznego adresu IP

W tym scenariuszu maszyna wirtualna nie jest częścią publicznego puli usługi równoważenia obciążenia (i nie jest częścią puli wewnętrznej standardowy moduł równoważenia obciążenia) i nie ma przypisanego adresu ILPIP. Gdy maszyna wirtualna tworzy przepływu wychodzącego, Azure tłumaczy prywatnej źródłowy adres IP przepływu wychodzącego do źródłowego publicznego adresu IP. Publiczny adres IP używany dla tego przepływu ruchu wychodzącego nie można skonfigurować, nie będą uwzględniane względem subskrypcji publicznego adresu IP limit zasobów.

>[!IMPORTANT] 
>Ten scenariusz ma zastosowanie również w przypadku __tylko__ wewnętrzny podstawowe usługi równoważenia obciążenia jest dołączony. Scenariusz 3 jest __nie jest dostępny__ po wewnętrzny standardowy moduł równoważenia obciążenia jest dołączony do maszyny Wirtualnej.  Należy jawnie utworzyć [scenariusz 1](#ilpip) lub [Scenariusz 2](#lb) oprócz przy użyciu wewnętrzny standardowy moduł równoważenia obciążenia.

Platforma Azure korzysta SNAT z portu maskaradę ([PAT](#pat)) do tej funkcji. Ten scenariusz jest podobny do [Scenariusz 2](#lb), z wyjątkiem jest nie kontroluje adres IP używany. Jest to rezerwowy scenariusz dla gdy nie istnieją scenariusze 1 i 2. Jeśli chcesz, aby kontrolować wychodzący adres nie zaleca tego scenariusza. W przypadku połączeń wychodzących krytyczną częścią aplikacji została wybrana opcja inny scenariusz.

Są wstępnie przydzielonych portów SNAT, zgodnie z opisem w [SNAT zrozumienia i PAWEŁ](#snat) sekcji.  Liczbę maszyn wirtualnych do udostępniania zestawu dostępności Określa, które wstępnego przydzielania warstwy ma zastosowanie.  Autonomicznej maszyny Wirtualnej bez zestawu dostępności to skutecznie Pula 1 na potrzeby określania wstępne przydzielanie (porty SNAT 1024). Porty SNAT są ograniczone zasób, który może być wyczerpany. Ważne jest, aby zrozumieć, jak są one [używane](#pat). Aby zrozumieć sposób projektowania dla tego zużycia i ograniczyć odpowiednio do potrzeb, przejrzyj [wyczerpania Zarządzanie SNAT](#snatexhaust).

### <a name="combinations"></a>Łączna scenariuszy z wieloma,

Możesz łączyć scenariuszy opisanych w poprzednich sekcjach, aby osiągnąć określony wynik. Występują wiele scenariuszy, w kolejności zastosowanie: [scenariusz 1](#ilpip) ma pierwszeństwo przed [Scenariusz 2](#lb) i [3](#defaultsnat). [Scenariusz 2](#lb) zastępuje [Scenariusz 3](#defaultsnat).

Przykładem jest wdrożenie usługi Azure Resource Manager, gdzie aplikacja opiera się głównie na połączenia wychodzące do ograniczonej liczby miejsc docelowych, ale także odebrał przepływów przychodzących za pośrednictwem frontonu modułu równoważenia obciążenia. W takim przypadku można łączyć scenariusze 1 i 2 do zwolnienia. Dodatkowe wzorce, można przejrzeć [wyczerpania Zarządzanie SNAT](#snatexhaust).

### <a name="multife"></a> Frontends wiele przepływów wychodzących

#### <a name="load-balancer-standard"></a>Usługa Load Balancer w warstwie Standardowa

Standardowe usługi równoważenia obciążenia używa wszystkich kandydatów przepływów wychodzących jednocześnie godzinę [wiele frontends IP (publicznymi)](load-balancer-multivip-overview.md) jest obecny. Każdy frontonu mnoży liczbę dostępnych portów SNAT przydzielony wstępnie włączenie reguły równoważenia obciążenia dla połączeń wychodzących.

Można pominąć adresu IP frontonu używanych dla połączenia wychodzące z nowej opcji reguły równoważenia obciążenia:

```json    
      "loadBalancingRules": [
        {
          "disableOutboundSnat": false
        }
      ]
```

Zazwyczaj domyślnie ta opcja _false_ i wskazuje, czy ta reguła programy SNAT wychodzącego skojarzone maszyn wirtualnych w puli zaplecza reguły równoważenia obciążenia.  Można to zmienić na _true_ zapobiegające moduł równoważenia obciążenia dla połączeń wychodzących dla maszyny Wirtualnej przy użyciu adresu IP frontonu skojarzonego elementu w puli zaplecza tej reguły równoważenia obciążenia.  I nadal można wyznaczyć określonego adresu IP do przepływów wychodzących, zgodnie z opisem w [wielu łączyć scenariusze](#combinations) również.

#### <a name="load-balancer-basic"></a>Basic modułu równoważenia obciążenia

Podstawowe usługi równoważenia obciążenia wybierze pojedynczego serwera sieci Web do zastosowania w przypadku przepływów wychodzących po [wiele frontends IP (publicznymi)](load-balancer-multivip-overview.md) nadają się do przepływów wychodzących. To pole wyboru nie można skonfigurować, należy rozważyć być losowy algorytm zaznaczenia. Określony adres IP dla przepływów wychodzących można wyznaczyć, zgodnie z opisem w [wielu łączyć scenariusze](#combinations).

### <a name="az"></a> Dostępność strefy

Korzystając z [standardowy moduł równoważenia obciążenia z stref dostępności](load-balancer-standard-availability-zones.md), frontends strefowo nadmiarowy zapewniają nadmiarowe strefy połączeń wychodzących SNAT rekompensaty dla użytkownika i programowania w języku SNAT błąd stref.  W przypadku używania zonal frontends połączeń wychodzących SNAT udostępniać obecność strefy, które należą do.

## <a name="snat"></a>Opis SNAT i Marta

### <a name="pat"></a>Port zamaskowana SNAT (PAWEŁ)

Gdy zasób publiczny moduł równoważenia obciążenia jest powiązana z wystąpień maszyn wirtualnych, każde źródło połączenia wychodzącego jest napisany od nowa. Źródło jest napisany od nowa z wirtualnej sieci prywatnej przestrzeń adresową do publicznego adresu IP usługi równoważenia obciążenia serwera sieci Web. W publicznej przestrzeni adresów IP 5-elementowej przepływu (źródłowy adres IP, port źródłowy, protokół transportu IP, docelowy adres IP, port docelowy) muszą być unikatowe.  SNAT zamaskowana dla portu można przy użyciu protokołów TCP lub UDP IP.

Tymczasowych porty (SNAT) są używane do osiągnięcia tego celu po ponowne zapisywanie prywatnej źródłowy adres IP, ponieważ wiele przepływów pochodzi z jednego publicznego adresu IP. 

Jeden port SNAT jest używany na przepływ pojedynczego adresu IP, portu i protokołu. Wiele przepływów do docelowego adresu IP, portu i protokołu zajmują jednego portu SNAT każdego przepływu. Dzięki temu strumienie są unikatowe, gdy pochodziły z tej samej publiczny adres IP i przejdź do tego samego adresu IP, portu i protokołu. 

Wiele przepływów, każdy z nich do innego adresu IP, port i protokół, współużytkować jeden port SNAT. Docelowy adres IP, port oraz protokół zapewnić przepływów unikatowy bez konieczności stosowania dodatkowych źródła porty, aby odróżnić przepływów w publicznej przestrzeni adresów IP.

Wyczerpania zasobów portu SNAT przepływów wychodzących zakończyć się niepowodzeniem aż do istniejących przepływów zwolnienia SNAT portów. Moduł równoważenia obciążenia zwraca SNAT portów, gdy przepływ zamyka i używa [limit czasu bezczynności 4-minutowy](#idletimeout) dla odzyskiwania porty SNAT z bezczynności przepływów.

Wzorce ograniczyć warunki, które często doprowadzić do wyczerpania portu SNAT, można przejrzeć [Zarządzanie SNAT](#snatexhaust) sekcji.

### <a name="preallocatedports"></a>Wstępne przydzielanie portów efemerycznych dla portu maskaradę SNAT (PAWEŁ)

Azure używa algorytmu, aby określić liczbę SNAT wstępnie przydzielonych portów dostępnych na podstawie rozmiaru puli wewnętrznej bazy danych przy użyciu portu zamaskowana SNAT ([PAT](#pat)). Porty SNAT są porty efemeryczne, które są dostępne dla określonego adresu źródłowego publicznego adresu IP.

Taką samą liczbę portów SNAT są odpowiednio wstępnie przydzielonych dla protokołów UDP i TCP i niezależnie zużytych według adresu IP, protokół transportu. 

>[!IMPORTANT]
>Standardowe SNAT SKU programowania na protokół transportu IP ani pochodzi z reguły równoważenia obciążenia.  Jeśli tylko istnieje reguła równoważenia obciążenia TCP, SNAT jest dostępna tylko dla protokołu TCP. Jeśli masz tylko TCP reguły równoważenia obciążenia i wymagają SNAT wychodzący protokołu UDP dla, należy utworzyć regułę z tego samego serwera sieci Web do tej samej puli wewnętrznej bazy danych z równoważeniem obciążenia UDP.  To spowoduje wyzwolenie SNAT programowania dla protokołu UDP.  Sonda reguły lub kondycji pracy nie jest wymagane.  Podstawowy SKU SNAT zawsze programów SNAT zarówno protokołu transportowego IP, niezależnie od protokołu transportu określone w regułę równoważenia obciążenia.

Azure preallocates SNAT portów z konfiguracją protokołu IP karty sieciowej z każdej maszyny Wirtualnej. Po dodaniu konfiguracji IP do puli, porty SNAT są wstępnie przydzielonych dla tej konfiguracji IP na podstawie rozmiaru puli wewnętrznej bazy danych. Po utworzeniu przepływów wychodzących [PAT](#pat) dynamicznie zużywa (w granicach przydzielonych wstępnie) i zwalnia te porty, po zamknięciu przepływ lub [limitów czasu bezczynności](#ideltimeout) stanie.

W poniższej tabeli przedstawiono preallocations portu SNAT warstw rozmiary puli wewnętrznej bazy danych:

| Rozmiar puli (wystąpień maszyny Wirtualnej) | Przydzielony wstępnie SNAT portów w każdej konfiguracji adresu IP|
| --- | --- |
| 1-50 | 1,024 |
| 51-100 | 512 |
| 101-200 | 256 |
| 201-400 | 128 |
| 401-800 | 64 |
| 1000 801 | 32 |

>[!NOTE]
> Gdy przy użyciu standardowego modułu równoważenia obciążenia z [wiele frontends](load-balancer-multivip-overview.md), [każdego adresu IP frontonu mnoży liczbę dostępnych portów SNAT](#multivipsnat) w poprzedniej tabeli. Na przykład pulę zaplecza 50 maszyny Wirtualnej z 2 reguł równoważenia obciążenia, każdy z adresów IP frontonu oddzielne użyje 2048 (2 x 1024) SNAT portów w każdej konfiguracji adresu IP. Zobacz szczegóły dotyczące [frontends wiele](#multife).

Należy pamiętać, że liczba dostępnych portów SNAT nie przekłada się bezpośrednio do liczby przepływów. Jeden port SNAT mogą być ponownie używane dla wielu celów unikatowy. Porty są używane tylko wtedy, gdy jest to niezbędne do zapewnienia przepływów unikatowy. Aby uzyskać wskazówki dotyczące projektowania i środki zaradcze, można znaleźć w sekcji o [jak zarządzać zasobem wyczerpującymi](#snatexhaust) i sekcji opisano [PAT](#pat).

Zmiana rozmiaru puli wewnętrznej bazy danych może mieć wpływ na niektóre przepływów ustalonych. Jeśli rozmiar puli wewnętrznej bazy danych zwiększa się i przechodzi do następnej warstwy, połowy Twojej przydzielony wstępnie porty SNAT odzyskane podczas przejścia do następnej warstwy większych puli wewnętrznej bazy danych. Przepływów, które są skojarzone z portem SNAT regeneracji limit czasu i należy ustanowić. Próbie nowy przepływ przepływ powiedzie się natychmiast tak długo, jak są dostępne porty przydzielony wstępnie.

Jeśli zmniejsza rozmiar puli wewnętrznej bazy danych i zwiększyć liczbę dostępnych portów SNAT przejścia do dolnej warstwy. W takim przypadku istniejące przydzielonych SNAT portów i nie ma wpływu na ich odpowiednich przepływów.

SNAT porty są protokół transportu IP określonego (TCP i UDP są przechowywane osobno) i są wydawane w następujących warunkach:

### <a name="tcp-snat-port-release"></a>Wersja port TCP SNAT

- Jeśli zarówno klient/serwer wysyła potwierdzenia/w wynikach wyszukiwania, SNAT port zostaną wydane 240 sekund.
- Jeśli występuje RST, SNAT port zostaną wydane po 15 sekund.
- Osiągnięto limit czasu bezczynności

### <a name="udp-snat-port-release"></a>Wersja portu UDP SNAT

- Osiągnięto limit czasu bezczynności

## <a name="problemsolving"></a> Rozwiązywanie problemów 

Ta sekcja jest przeznaczona do zmniejszenia wyczerpania SNAT i innych scenariuszy, które mogą wystąpić w przypadku połączeń wychodzących na platformie Azure.

### <a name="snatexhaust"></a> Zarządzanie wyczerpania portu SNAT (PAWEŁ)
[Porty efemeryczne](#preallocatedports) używany do [PAT](#pat) są zużywalnymi zasobami, zgodnie z opisem w [autonomicznej maszyny Wirtualnej, bez adres publiczny adres IP na poziomie wystąpienia](#defaultsnat) i [równoważeniem obciążenia maszyny Wirtualnej bez Wystąpienie poziomu publicznego adresu IP](#lb).

Jeśli wiadomo, że jest inicjowanie liczbę wychodzących połączeń TCP lub UDP do tego samego adresu IP i portu i obserwować niepowodzeniem połączenia wychodzące lub zalecana przy pomocy technicznej jest bardzo wyczerpuje porty SNAT (wstępnie przydzielonych [tymczasowych porty](#preallocatedports) używane przez [PAT](#pat)), masz kilka opcji ogólnych środki zaradcze. Przejrzyj te opcje i zdecyduj, jaki jest dostępny i najlepsze w przypadku danego scenariusza. Istnieje możliwość, że co najmniej jeden ułatwiają zarządzanie tego scenariusza.

Jeśli problemy z zrozumienie zachowania połączeń wychodzących można użyć adresu IP stosu statystyk (netstat). Lub może być przydatne do przestrzegania zachowania połączeń przy użyciu przechwytywania pakietów. Możesz wykonać te przechwytywania pakietów w system operacyjny gościa wystąpienia, lub użyj [obserwatora sieciowego dla przechwytywania pakietów](../network-watcher/network-watcher-packet-capture-manage-portal.md).

#### <a name="connectionreuse"></a>Modyfikowanie aplikacji do ponownego użycia połączenia 
Można zmniejszyć zapotrzebowanie na porty efemeryczne, używanych do SNAT przez ponowne użycie połączenia w aplikacji. Jest to szczególnie istotne dla protokołów, takich jak HTTP/1.1, gdzie ponownemu połączenia jest ustawieniem domyślnym. I innych protokołów, które zgodnie z ich transportu (na przykład REST) mogą korzystać z kolei za pomocą protokołu HTTP. 

Ponowne użycie zawsze jest lepszym rozwiązaniem niż poszczególnych, atomic połączeń TCP dla każdego żądania. Ponowne użycie powoduje więcej wydajności, bardzo wydajny transakcji protokołu TCP.

#### <a name="connection pooling"></a>Modyfikowanie aplikacji do korzystania z puli połączeń
Można wdrożyć połączenia buforowanie schemat w aplikacji, które żądania są dystrybuowane wewnętrznie przez ustalony zbiór połączeń (każdego ponowne użycie w miarę możliwości). Ten schemat ogranicza liczbę portów tymczasowych w użyciu i tworzy środowisku bardziej przewidywalne. Ten schemat także może zwiększyć przepływność żądań zezwalając wiele równoczesnych operacji, gdy blokuje pojedynczego połączenia w przypadku odpowiedzi operacji.  

Pula połączeń może już istnieć w ramach używanego do tworzenia aplikacji lub ustawienia konfiguracji dla aplikacji. Można połączyć z połączenia ponownemu puli połączeń. Wiele żądań mogły używać stały, przewidywalnych liczba portów do tego samego adresu IP i portu. Żądania również korzystać z efektywne wykorzystanie transakcji TCP zmniejsza opóźnienia i użycia zasobów. Transakcje protokołu UDP mogą również skorzystać, ponieważ zarządzanie liczba przepływów UDP mogą z kolei uniknąć warunków spalin i zarządzanie SNAT użycie portu.

#### <a name="retry logic"></a>Modyfikowanie aplikacji na używanie mniej agresywne Logika ponawiania
Gdy [wstępnie przydzielonych portów tymczasowych](#preallocatedports) używany do [PAT](#pat) są wyczerpane lub aplikacji występują błędy, życie aktywnego lub atak metodą prób bez zanikania i wycofywania logiki spowodować wyczerpanie wystąpienia lub utrwalić. Można zmniejszyć zapotrzebowanie na porty efemeryczne przy użyciu mniej agresywne logiki ponawiania próby. 

Porty efemeryczne mieć 4-minutowy limit czasu bezczynności (nie można dostosować). Jeśli ponowne próby są zbyt agresywnie, wyczerpanie nie ma możliwość wyczyszczone samodzielnie. W związku z tym biorąc pod uwagę, jak--i jak często--aplikacji ponowi próbę transakcji jest krytyczną częścią projektu.

#### <a name="assignilpip"></a>Przypisz wystąpienie poziomu publicznego adresu IP do każdej maszyny Wirtualnej
Przypisywanie ILPIP zmiany Twojego scenariusza [wystąpienia poziomu publicznego adresu IP do maszyny Wirtualnej](#ilpip). Wszystkie porty efemeryczne publicznego adresu IP, które są używane dla każdej maszyny Wirtualnej są dostępne dla maszyny Wirtualnej. (W przeciwieństwie do scenariuszy gdzie porty efemeryczne publicznego adresu IP są udostępniane wszystkich skojarzonych z pulą wewnętrznej bazy danych w odpowiednich maszynach wirtualnych). Brak możliwości wziąć pod uwagę, takie jak dodatkowych kosztów publicznych adresów IP i potencjalnego wpływu listę dozwolonych podobnej dużą liczbę pojedyncze adresy IP.

>[!NOTE] 
>Ta opcja nie jest dostępna dla roli proces roboczy sieci web.

#### <a name="multifesnat"></a>Użyj wielu frontends

Podczas korzystania z publicznej standardowy moduł równoważenia obciążenia, Przypisz [wielu adresów IP frontonu dla połączeń wychodzących](#multife) i [należy pomnożyć liczbę dostępnych portów SNAT](#preallocatedports).  Musisz utworzyć konfiguracji IP frontonu, reguły i puli zaplecza, aby wyzwolić programowania w języku SNAT do publicznego adresu IP z serwera sieci Web.  Reguła nie musi działać i badania kondycji nie powiodło się.  Jeśli używasz wielu frontends dla ruchu przychodzącego oraz (, a nie tylko dla ruchu wychodzącego), należy użyć sondy kondycji niestandardowe również zagwarantowanie niezawodności.

>[!NOTE]
>W większości przypadków wyczerpania SNAT portów jest znak zły projektu.  Upewnij się, że zrozumienie, dlaczego są wyczerpujące porty przed użyciem więcej frontends można dodać SNAT portów.  Może być maskowania problemu, co może prowadzić do awarii później.

#### <a name="scaleout"></a>Skalowanie w poziomie

[Wstępnie przydzielonych portów](#preallocatedports) są przypisywane na podstawie rozmiaru puli wewnętrznej bazy danych i grupowanych do warstwy, aby zminimalizować zakłócenia, gdy niektóre z portów przydzielić, aby zmieścił się w następnej warstwy rozmiar większy puli wewnętrznej bazy danych.  Masz opcję, aby zwiększyć intensywność SNAT użycie portu dla danego serwera sieci Web przez skalowanie pulę zaplecza do maksymalnego rozmiaru w danej warstwie.  Wymaga to dla aplikacji, aby efektywnie skalować w poziomie.

Na przykład 2 maszyn wirtualnych w puli zaplecza musi 1024 SNAT portów dostępnych dla konfiguracji adresów IP, co łącznie 2048 SNAT portów dla wdrożenia.  Gdyby wdrożenia można zwiększyć do 50 wirtualnych maszyny, nawet wtedy, gdy liczba wstępnie przydzielonych portów pozostaje stała na maszynę wirtualną, łącznie z 51,200 (50 x 1024) SNAT porty mogą posłużyć wdrożenia.  Jeśli chcesz skalowania wdrożenia Sprawdź liczbę [wstępnie przydzielonych portów](#preallocatedports) na warstwę, aby upewnić się, że kształt Twojej skalowania w poziomie do maksymalnej dla odpowiednich warstwy.  W przykładzie poprzedzających, jeżeli wybrano skalować w poziomie do 51 zamiast 50 wystąpień użytkownika czy postępu do następnej warstwy i na końcu się z mniej SNAT portów dla maszyny Wirtualnej oraz jak Suma.

Z drugiej strony skalowania w poziomie do następnego większych wewnętrznej bazy danych puli rozmiar warstwy potencjalnie wychodzących połączeń Jeśli przydzielonych portów ma odbiorczego.  Jeśli nie ma to miejsce, należy kształtu wdrożenia do rozmiaru warstwy.  Lub aplikacji mogą wykrywać i ponów próbę wykonania niezbędne.  TCP keepalives może pomóc w Wykryj, kiedy SNAT już porty funkcji z powodu przydzielany.

### <a name="idletimeout"></a>Użyj keepalives, aby zresetować wychodzącego limit czasu bezczynności

Połączenia wychodzące mieć 4-minutowy limit czasu bezczynności. Tego limitu czasu nie jest zmieniane. Jednak aby odświeżyć przepływu bezczynności i zresetować ten limit czasu bezczynności, w razie potrzeby mogą używać transportu (na przykład keepalives TCP) lub keepalives warstwy aplikacji.  

Przy użyciu TCP keepalives, wystarczy je włączyć na jednej stronie połączenia. Na przykład wystarczy włączyć je po stronie serwera tylko do resetowania czasomierza bezczynności przepływu i nie jest konieczne po obu stronach inicjowane keepalives TCP.  Istnieją podobne pojęcia warstwy aplikacji, w tym konfiguracji klient serwer bazy danych.  Sprawdź po stronie serwera, na jakie opcje istnieje dla keepalives określonych aplikacji.

## <a name="discoveroutbound"></a>Odnajdywanie publicznego adresu IP, który korzysta z maszyny Wirtualnej
Istnieje wiele sposobów, aby określić źródłowy publiczny adres IP połączeń wychodzących. OpenDNS zapewnia usługę, które mogą być prezentowane z publicznego adresu IP maszyny Wirtualnej. 

Za pomocą polecenia nslookup, możesz wysłać zapytanie DNS dla nazwy myip.opendns.com OpenDNS program rozpoznawania nazw. Usługa zwraca źródłowy adres IP użyty do wysłania zapytania. Po uruchomieniu następującego zapytania z maszyny Wirtualnej, odpowiedź jest publiczny adres IP używany dla tej maszyny Wirtualnej:

    nslookup myip.opendns.com resolver1.opendns.com

## <a name="preventoutbound"></a>Uniemożliwia nawiązanie połączenia wychodzącego
Czasami jest niepożądanych dla maszyny Wirtualnej, aby można było utworzyć przepływ wychodzący. Może być wymagane do zarządzania, które miejsc docelowych jest osiągalna z przepływów wychodzących lub które miejsc docelowych można rozpocząć przepływów przychodzących. W takim przypadku można użyć [sieciowej grupy zabezpieczeń](../virtual-network/virtual-networks-nsg.md) do zarządzania miejsc docelowych, które maszyna wirtualna może nawiązać połączenie. Umożliwia także grup NSG do zarządzania publicznego lokalizację docelową mogą inicjować przepływów przychodzących. 

Po zastosowaniu grupy NSG z maszyną wirtualną z równoważeniem obciążenia, należy zwrócić uwagę na [domyślne znaczniki](../virtual-network/virtual-networks-nsg.md#default-tags) i [domyślne zasady](../virtual-network/virtual-networks-nsg.md#default-rules). Należy się upewnić, czy maszyny Wirtualnej może odbierać żądań sondy kondycji z modułu równoważenia obciążenia Azure. 

Jeśli grupy NSG blokuje żądania sondy kondycji z AZURE_LOADBALANCER domyślny znacznik, sondy kondycji Twojej maszyny Wirtualnej nie powiedzie się i maszyny Wirtualnej jest oznaczony w dół. Moduł równoważenia obciążenia zatrzymuje wysyłanie nowych przepływów z tą maszyną Wirtualną.

## <a name="limitations"></a>Ograniczenia
- DisableOutboundSnat nie jest dostępny jako opcja podczas konfigurowania reguły w portalu równoważenia obciążenia.  Zamiast tego użyj narzędzia REST, szablonu lub klienta.
- Role pracownika w sieci Web bez sieci wirtualnej i innych usług platformy Microsoft mogą stać się niedostępne po tylko wewnętrzny standardowy moduł równoważenia obciążenia jest używany z powodu efekt uboczny z jak funkcja usług pre-VNet usług i innych platform. Należy nie polega na tego efektu ubocznego ponieważ odpowiednie usługi siebie lub podstawowa platformy mogą ulec zmianie bez uprzedzenia. Należy zawsze musi założenie, że należy utworzyć łączność wychodząca jawnie w razie potrzeby, używając wewnętrzny standardowy moduł równoważenia obciążenia tylko. [Domyślne SNAT](#defaultsnat) Scenariusz 3 opisane w tym artykule nie jest dostępna.

## <a name="next-steps"></a>Kolejne kroki

- Dowiedz się więcej o [modułu równoważenia obciążenia](load-balancer-overview.md).
- Dowiedz się więcej o [usłudze Load Balancer w warstwie Standardowa](load-balancer-standard-overview.md).
- Dowiedz się więcej o [sieciowej grupy zabezpieczeń](../virtual-network/virtual-networks-nsg.md).
- Dowiedz się więcej o niektóre inne kluczowe [możliwości w zakresie obsługi](../networking/networking-overview.md) na platformie Azure.
