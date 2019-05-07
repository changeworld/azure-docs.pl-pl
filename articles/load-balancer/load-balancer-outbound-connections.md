---
title: Połączenia wychodzące na platformie Azure
titlesuffix: Azure Load Balancer
description: W tym artykule wyjaśniono, jak platforma Azure zapewnia maszyny wirtualne do komunikowania się z publicznych usług internetowych.
services: load-balancer
documentationcenter: na
author: KumudD
ms.service: load-balancer
ms.custom: seodec18
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/02/2019
ms.author: kumud
ms.openlocfilehash: d5f52829f5895b30afd160cc8ded755332aca5c5
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/06/2019
ms.locfileid: "65190167"
---
# <a name="outbound-connections-in-azure"></a>Połączenia wychodzące na platformie Azure

Platforma Azure udostępnia połączenia ruchu wychodzącego dla wdrożenia klienta za pomocą kilku różnych mechanizmów. W tym artykule opisano scenariusze są, gdy są spełnione, jak działają i jak nimi zarządzać.

>[!NOTE] 
>W tym artykule opisano tylko w przypadku wdrożeń usługi Resource Manager. Przegląd [połączenia wychodzące (klasyczne)](load-balancer-outbound-connections-classic.md) we wszystkich scenariuszach wdrożenia klasycznego na platformie Azure.

Wdrożenie na platformie Azure mogą komunikować się z punktami końcowymi spoza platformy Azure w przestrzeń publicznych adresów IP. Gdy wystąpienie inicjuje przepływ ruchu wychodzącego do miejsca docelowego w przestrzeń publicznych adresów IP, Azure dynamicznie mapuje prywatny adres IP na publiczny adres IP. Po utworzeniu tego mapowania ruch powrotny dla tego ruchu wychodzącego przepływu pochodzącej może także łączyć prywatny adres IP skąd pochodzi przepływ.

Platforma Azure używa translatora adresów sieciowych źródła (SNAT), aby wykonać tę funkcję. W przypadku wielu prywatnych adresów IP są maskaradę za jeden publiczny adres IP, platforma Azure używa [portu translacji adresów (PAT)](#pat) poczynania prywatnych adresów IP. Porty efemeryczne są używane do osobisty token dostępu i są [wstępnie przydzielonych](#preallocatedports) na podstawie rozmiaru puli.

Dostępnych jest wiele [scenariusze ruchu wychodzącego](#scenarios). Można połączyć te scenariusze, zgodnie z potrzebami. Zapoznaj się z nimi, aby zrozumieć możliwości, ograniczenia i wzorce, ponieważ mają one zastosowanie do modelu wdrażania i scenariusz aplikacji. Przejrzyj wskazówki dotyczące [Zarządzanie te scenariusze](#snatexhaust).

>[!IMPORTANT] 
>Standardowy moduł równoważenia obciążenia i standardowego publicznego adresu IP wprowadzenia nowych możliwości i różne zachowania do łączności wychodzącej.  Nie są one takie same jak podstawowych wersji SKU.  Jeśli chcesz korzystać z łączności wychodzącej podczas pracy ze standardowej jednostki SKU, należy jawnie można zdefiniować ją przy użyciu standardowych publicznych adresów IP lub standardowego publicznego modułu równoważenia obciążenia.  Obejmuje to tworzenie łączności wychodzącej przy użyciu i wewnętrznego modułu równoważenia obciążenia standardowego.  Zaleca się, że zawsze używać reguł dla ruchu wychodzącego standardowego publicznego modułu równoważenia obciążenia.  [Scenariusz 3](#defaultsnat) nie jest dostępna za pomocą standardowej jednostki SKU.  To oznacza użycie wewnętrznego standardowego modułu równoważenia obciążenia, które należy wykonać kroki, aby utworzyć połączenia ruchu wychodzącego dla maszyn wirtualnych w puli zaplecza, jeśli łączność wychodząca jest pożądane.  W kontekście wychodzącym pojedynczą autonomiczną maszyną Wirtualną, wszystkie maszyny Wirtualnej w zestawie dostępności wszystkich wystąpień w VMSS zachowują się w grupie. Oznacza to, jeśli pojedyncza maszyna wirtualna w zestawie dostępności jest skojarzony z standardowej jednostki SKU, wszystkie wystąpienia maszyn wirtualnych w zestawie dostępności teraz zachowują się przez te same zasady tak, jakby są oni skojarzeni z standardowej jednostki SKU, nawet jeśli poszczególne wystąpienia nie jest bezpośrednio związane z nią.  Uważnie przeczytaj ten całego dokumentu, aby zapoznać się z pojęciami ogólną, przejrzyj [Balancer w warstwie standardowa](load-balancer-standard-overview.md) różnic między jednostkami SKU i przejrzyj [reguł dla ruchu wychodzącego](load-balancer-outbound-rules-overview.md).  Za pomocą reguł ruchu wychodzącego umożliwia bardziej szczegółową kontrolę nad wszystkimi aspektami łączności wychodzącej.

## <a name="scenarios"></a>Omówienie scenariusza

Usługa Azure Load Balancer i powiązane zasoby są jawnie zdefiniowane podczas korzystania z [usługi Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview).  System Azure oferuje obecnie trzech różnych metod na osiągnięcie wychodzącym dla zasobów usługi Azure Resource Manager. 

| Jednostki SKU | Scenariusz | Metoda | Protokoły IP | Opis |
| --- | --- | --- | --- | --- |
| Standardowa i podstawowa | [1. Maszyny Wirtualnej przy użyciu adresu publicznego adresu IP poziomu wystąpienia (z lub bez modułu równoważenia obciążenia)](#ilpip) | Nieużywany port maskaradę SNAT, | TCP, UDP, ICMP, ESP | Platforma Azure korzysta z publicznego adresu IP, przypisany do konfiguracji IP karty sieciowej. wystąpienie programu Wystąpienie ma wszystkie dostępne porty efemeryczne. Gdy użycie standardowego modułu równoważenia obciążenia, należy użyć [reguł dla ruchu wychodzącego](load-balancer-outbound-rules-overview.md) umożliwia jawne zdefiniowanie łączności wychodzącej |
| Standardowa i podstawowa | [2. Publiczny moduł równoważenia obciążenia skojarzone z maszyną Wirtualną (Brak adresu publicznym adresem IP na poziomie wystąpienia na wystąpieniu)](#lb) | SNAT przy użyciu portu maskaradę (PAT) przy użyciu frontonów modułu równoważenia obciążenia | TCP, UDP |Platforma Azure udostępnia publiczny adres IP publicznego frontonów modułu równoważenia obciążenia za pomocą wielu prywatnych adresów IP. Platforma Azure używa portów tymczasowych z frontonów osobisty token dostępu. |
| Brak lub podstawowe | [3. Autonomiczna maszyna wirtualna, (Brak usługi równoważenia obciążenia, żaden adres publiczny adres IP poziomu wystąpienia)](#defaultsnat) | SNAT przy użyciu portu maskaradę (PAT) | TCP, UDP | Azure automatycznie wyznacza publiczny adres IP dla SNAT, udostępnia ten publiczny adres IP za pomocą wielu prywatnych adresów IP zestawu dostępności i korzysta z portów tymczasowych ten publiczny adres IP. Ten scenariusz jest rezerwowych dla powyższych scenariuszy. Firma Microsoft nie zaleca, aby uzyskać lepszy wgląd i kontrolę. |

Jeśli nie chcesz, aby maszyny Wirtualnej w celu komunikowania się z punktami końcowymi spoza platformy Azure w publicznej przestrzeni adresów IP, można użyć grup zabezpieczeń sieci (NSG) w celu zablokowania dostępu, zgodnie z potrzebami. Sekcja [zapobieganie łączności wychodzącej](#preventoutbound) omówienie sieciowych grup zabezpieczeń w bardziej szczegółowo. Wskazówki dotyczące projektowania, wdrażania i zarządzania bez żadnych wychodzącego dostępu do sieci wirtualnej znajduje się poza zakres tego artykułu.

### <a name="ilpip"></a>Scenariusz 1: Maszyny Wirtualnej przy użyciu adresu publicznego adresu IP poziomu wystąpienia

W tym scenariuszu maszyna wirtualna ma wystąpienia poziom publicznego adresu IP (ILPIP) do niej przypisany. Jeśli chodzi o połączeniach wychodzących, nie ma znaczenia, czy maszyna wirtualna jest równoważone, czy nie. Ten scenariusz ma pierwszeństwo przed innymi. Gdy używany jest ILPIP, maszyna wirtualna używa ILPIP wszystkie przepływy ruchu wychodzącego.  

Publiczny adres IP przypisany do maszyny Wirtualnej jest to relacja 1:1 (zamiast 1: wiele) oraz implementowany jako bezstanowej translatorem adresów sieciowych 1:1  Nie jest używany port zamaskowany (PAT), a maszyna wirtualna ma wszystkie porty efemeryczne dostępne do użycia.

Jeśli wystąpią wyczerpanie portów SNAT aplikacji inicjuje wiele przepływów ruchu wychodzącego, należy wziąć pod uwagę przypisywanie [ILPIP złagodzić ograniczenia SNAT](#assignilpip). Przegląd [wyczerpania Zarządzanie SNAT](#snatexhaust) w całości.

### <a name="lb"></a>Scenariusz 2: Równoważenia obciążenia maszyn wirtualnych bez adresu publicznym adresem IP na poziomie wystąpienia

W tym scenariuszu maszyna wirtualna jest częścią publicznej puli zaplecza modułu równoważenia obciążenia. Maszyna wirtualna nie ma do niej przypisany publiczny adres IP. Zasób modułu równoważenia obciążenia musi być skonfigurowany przy użyciu reguły modułu równoważenia obciążenia, aby utworzyć łącze między publicznego adresu IP frontonu z pulą zaplecza.

Jeśli nie wykonasz tę konfigurację reguły, zachowanie jest opisany w scenariuszu dla [autonomicznej maszyny Wirtualnej, przy użyciu nie poziom publiczny adres IP wystąpienia](#defaultsnat). Nie jest konieczne dla tej reguły odbiornika roboczych w puli zaplecza dla sondy kondycji zakończyło się sukcesem.

W przypadku maszyn wirtualnych ze zrównoważonym obciążeniem tworzy przepływu wychodzącego, Azure tłumaczy prywatnej źródłowy adres IP przepływu wychodzącego do publicznego adresu IP frontonu modułu publicznej usługi Load Balancer. Platforma Azure używa SNAT, aby wykonać tę funkcję. Platforma Azure również używa [osobisty token dostępu](#pat) poczynania wielu prywatnych adresów IP za pomocą publicznego adresu IP. 

Efemeryczne porty frontonu adres IP publicznego modułu równoważenia obciążenia są używane w celu odróżnienia poszczególnych przepływy tworzone przez maszynę Wirtualną. Dynamicznie używa SNAT [wstępnie przydzielonych portów tymczasowych](#preallocatedports) utworzenia przepływy wychodzące. W tym kontekście portów tymczasowych używany do SNAT są nazywane SNAT portów.

Porty SNAT wstępnie są przydzielane zgodnie z opisem w [SNAT zrozumienie i osobisty token dostępu](#snat) sekcji. Są one ograniczone zasób, który może wyczerpać. Jest ważne zrozumieć, jak są one [używane](#pat). Aby dowiedzieć się, jak zaprojektować za to użycie i ograniczać zgodnie z potrzebami, zapoznaj się z [wyczerpania Zarządzanie SNAT](#snatexhaust).

Gdy [wiele publicznych adresów IP skojarzonych z podstawowego modułu równoważenia obciążenia](load-balancer-multivip-overview.md)te publiczne adresy IP są kandydatem do przepływy wychodzące i jeden losowo wybrany.  

Aby monitorować kondycję połączenia wychodzące z podstawowego modułu równoważenia obciążenia, możesz użyć [usługi Azure Monitor dzienniki dla modułu równoważenia obciążenia](load-balancer-monitor-log.md) i [alertów dzienników zdarzeń](load-balancer-monitor-log.md#alert-event-log) do monitorowania komunikatów wyczerpanie portów SNAT.

### <a name="defaultsnat"></a>Scenariusz 3: Autonomiczny maszyn wirtualnych, bez adresu publicznym adresem IP na poziomie wystąpienia

W tym scenariuszu maszyna wirtualna nie jest częścią puli publicznego modułu równoważenia obciążenia (i nie jest częścią puli wewnętrznej Balancer w warstwie standardowa) i nie ma przypisanego adresu ILPIP. Podczas tworzenia maszyny Wirtualnej przepływu wychodzącego, Azure tłumaczy prywatnej źródłowy adres IP przepływu wychodzącego do publicznych źródłowego adresu IP. Publiczny adres IP używany dla tego przepływu ruchu wychodzącego nie konfiguruje się i nie wliczają subskrypcji publicznego adresu IP limit zasobów. Ten publiczny adres IP nie należy do Ciebie i nie może być zastrzeżone. Jeśli ponowne wdrażanie zestawu skalowania maszyn wirtualnych lub zestawu dostępności lub maszyny wirtualnej, ten publiczny adres IP zostaną zwolnione i zażądano nowego publicznego adresu IP. Nie należy używać w tym scenariuszu do listy dozwolonych adresów IP. Zamiast tego należy użyć jednego z dwóch scenariuszy gdzie można jawnie deklarować wychodzącego scenariusza i publiczny adres IP, który ma być używany dla łączności wychodzącej.

>[!IMPORTANT] 
>Ten scenariusz ma zastosowanie również podczas __tylko__ wewnętrznego podstawowego modułu równoważenia obciążenia jest dołączony. Scenariusz 3 jest __nie jest dostępna__ podczas wewnętrznego standardowego modułu równoważenia obciążenia jest dołączony do maszyny Wirtualnej.  Należy jawnie utworzyć [scenariusz 1](#ilpip) lub [Scenariusz 2](#lb) oprócz używania wewnętrznego standardowego modułu równoważenia obciążenia.

Platforma Azure używa SNAT przy użyciu portu maskaradę ([osobisty token dostępu](#pat)) do wykonywania tej funkcji. Ten scenariusz jest podobny do [Scenariusz 2](#lb), z wyjątkiem ma nie kontroluje adres IP używany. Jest to rezerwowego scenariusz dla sytuacji scenariusze 1 i 2, nie istnieje. Jeśli chcesz mieć kontrolę nad adresów ruchu wychodzącego nie zaleca tego scenariusza. Połączenia wychodzące są kluczową częścią aplikacji, należy wybrać inny scenariusz.

Są wstępnie przydzielonych portów SNAT, zgodnie z opisem w [SNAT zrozumienie i osobisty token dostępu](#snat) sekcji.  Liczba maszyn wirtualnych, udostępnianie zestawu dostępności Określa, która wstępnego przydzielania warstwa ma zastosowanie.  Autonomiczną maszynę Wirtualną bez zestawu dostępności jest skutecznie puli 1 na potrzeby określania wstępne przydzielanie (1024 SNAT porty). Porty SNAT są ograniczone zasób, który może wyczerpać. Jest ważne zrozumieć, jak są one [używane](#pat). Aby dowiedzieć się, jak zaprojektować za to użycie i ograniczać zgodnie z potrzebami, zapoznaj się z [wyczerpania Zarządzanie SNAT](#snatexhaust).

### <a name="combinations"></a>Wiele scenariuszy połączone

Można połączyć scenariuszy opisanych w poprzedniej sekcji, aby osiągnąć konkretnego wyniku. Jeśli podano wiele scenariuszy dotyczy kolejności: [scenariusz 1](#ilpip) ma pierwszeństwo przed [Scenariusz 2](#lb) i [3](#defaultsnat). [Scenariusz 2](#lb) zastępuje [Scenariusz 3](#defaultsnat).

Przykładem jest wdrożenie usługi Azure Resource Manager, gdzie aplikacja opiera się głównie na połączenia wychodzące do ograniczonej liczby miejsc docelowych, ale także odbiera przepływy ruchu przychodzącego za pośrednictwem frontonu modułu równoważenia obciążenia. W takim przypadku można łączyć scenariusze 1 i 2 do zwolnienia. Dodatkowe wzorce, można przejrzeć [wyczerpania Zarządzanie SNAT](#snatexhaust).

### <a name="multife"></a> Wiele frontonów przepływy wychodzące

#### <a name="standard-load-balancer"></a>Usługa Load Balancer w warstwie Standardowa

Standardowy moduł równoważenia obciążenia używa wszystkich kandydatów przepływy wychodzące, w tym samym godzinę [wiele frontonów IP (publicznego)](load-balancer-multivip-overview.md) jest obecny. Każdym frontonem mnoży liczbę dostępnych portów SNAT przydzielony wstępnie, jeśli reguły równoważenia obciążenia jest włączony dla połączeń wychodzących.

Można wybrać pominąć adresu IP frontonu z używane dla połączeń wychodzących przy użyciu nowej opcji reguły równoważenia obciążenia:

```json    
      "loadBalancingRules": [
        {
          "disableOutboundSnat": false
        }
      ]
```

Zwykle `disableOutboundSnat` opcję wartość domyślna to _false_ i oznacza, że ta zasada programy SNAT wychodzących na skojarzonych maszynach wirtualnych w puli zaplecza reguły równoważenia obciążenia. `disableOutboundSnat` Można ją zmienić na _true_ aby zapobiec modułu równoważenia obciążenia przy użyciu adresu IP frontonu skojarzona dla połączeń wychodzących dla maszyn wirtualnych w puli zaplecza tej reguły równoważenia obciążenia.  I nadal można wyznaczyć określonego adresu IP dla ruchu wychodzącego przepływów, zgodnie z opisem w [wiele scenariuszy w połączeniu](#combinations) także.

#### <a name="load-balancer-basic"></a>Podstawowy moduł równoważenia obciążenia

Podstawowy moduł równoważenia obciążenia wybiera pojedynczego serwera sieci Web ma być używany dla przepływy wychodzące podczas [wiele frontonów IP (publicznego)](load-balancer-multivip-overview.md) nadają się do przepływy wychodzące. Zaznacz to pole wyboru nie jest konfigurowalne i należy wziąć pod uwagę algorytm wybór jako losowych. Możesz wyznaczyć określonego adresu IP dla ruchu wychodzącego przepływów, zgodnie z opisem w [wiele scenariuszy w połączeniu](#combinations).

### <a name="az"></a> Strefy dostępności

Korzystając z [Balancer w warstwie standardowa ze strefami dostępności występują](load-balancer-standard-availability-zones.md)strefowo nadmiarowe Frontony może zapewnić strefowo nadmiarowych połączeń SNAT wychodzących i programowania SNAT przeżyje błąd stref.  W przypadku używania strefowych frontonów połączeń SNAT wychodzących udostępniać LOS strefy, które należą do.

## <a name="snat"></a>Opis SNAT i osobisty token dostępu

### <a name="pat"></a>Port zamaskowany SNAT (PAT)

Gdy zasób publicznego modułu równoważenia obciążenia jest skojarzony z wystąpieniami maszyn wirtualnych, jest przepisany każde źródło połączeń wychodzących. Źródło jest przepisany z prywatnej przestrzeni adresowej IP sieci wirtualnej do frontonu publiczny adres IP modułu równoważenia obciążenia. W publicznej przestrzeni adresowej IP tuple 5 (źródłowy adres IP, port źródłowy, protokół transportowy IP, docelowy adres IP, port docelowy) przepływu musi być unikatowa.  Zamaskowany SNAT portu może służyć za pomocą protokołów TCP lub UDP IP.

Efemeryczne porty (SNAT) są używane do osiągnięcia tego celu po ponownego zapisywania adresów prywatnych źródłowy adres IP, ponieważ wiele przepływów pochodzi z jednego publicznego adresu IP. Algorytm SNAT zamaskowany portu inaczej przydziela SNAT porty protokołu UDP i TCP.

#### <a name="tcp"></a>Porty TCP usługi SNAT

Jednym SNAT port jest używany na przepływ, aby jeden docelowy adres IP, portu. W wielu przepływy TCP do tego samego adresu IP, portu i protokołu każdego przepływu TCP wykorzystuje pojedynczy port SNAT. Dzięki temu przepływy są unikatowe, gdy pochodziły z tego samego publicznego adresu IP i przejdź do tego samego adresu IP, portu i protokołu. 

Wiele przepływów, do innego adresu IP, portu i protokołu, udostępniać pojedynczy port SNAT. Docelowy adres IP, port i protokół unikatowość przepływy bez konieczności stosowania dodatkowych źródła portów w celu odróżnienia przepływów przestrzeń publicznych adresów IP.

#### <a name="udp"></a> Porty SNAT UDP

Porty UDP SNAT są zarządzane przez innego algorytmu niż porty TCP SNAT.  Moduł równoważenia obciążenia używa algorytmu znane jako "ograniczony do portu stożek translatora adresów Sieciowych" protokołu UDP.  Jednym SNAT port jest używany dla każdego przepływu, niezależnie od tego, docelowy adres IP, portu.

#### <a name="exhaustion"></a>Wyczerpanie

Wyczerpania zasobów portu SNAT, przepływy wychodzące zakończyć się niepowodzeniem aż do istniejących przepływów zwolnienia SNAT portów. Moduł równoważenia obciążenia odzyskuje porty SNAT, gdy przepływ zostanie zamknięty i używa [limitu czasu bezczynności 4-minutowej](#idletimeout) dla odzyskiwanie porty SNAT wychodzących z przepływów bezczynności.

Porty UDP SNAT wyczerpać zazwyczaj znacznie szybciej niż porty TCP SNAT z powodu różnic w algorytmie używanym. Możesz projektowania i skalowania należy przetestować mając na uwadze to różnicę.

Wzorce i ułatwiają eliminowanie warunki, które często prowadzą do wyczerpanie portów SNAT, można przejrzeć [Zarządzanie SNAT](#snatexhaust) sekcji.

### <a name="preallocatedports"></a>Wstępne portów efemerycznych przydzielanie dla portu maskaradę SNAT (PAT)

Używa platformy Azure, algorytm, aby określić liczbę użyć funkcji SNAT względem wstępnie przydzielonych portów dostępnych na podstawie rozmiaru puli zaplecza przy użyciu portu zamaskowany SNAT ([osobisty token dostępu](#pat)). SNAT porty są dostępne dla określonego publicznego źródłowego adresu IP portów tymczasowych.

Taką samą liczbę portów SNAT są odpowiednio wstępnie przydzielonych dla protokołów UDP i TCP i użycie niezależnie na protokół transportowy IP.  Jednak użycie portu SNAT różni się w zależności od tego, czy przepływ jest UDP lub TCP.

>[!IMPORTANT]
>Standardowa jednostka SKU użyć funkcji SNAT względem programowania jest na protokół transportowy IP i pochodzące z reguły równoważenia obciążenia.  Jeśli istnieje reguła równoważenia obciążenia protokołu TCP, tylko SNAT jest dostępna tylko dla protokołu TCP. Jeśli masz tylko TCP reguły równoważenia obciążenia i potrzebujesz SNAT wychodzących protokołu UDP, należy utworzyć UDP regułę równoważenia obciążenia z tego samego serwera sieci Web do tej samej puli zaplecza.  To spowoduje wyzwolenie SNAT programowania protokołu UDP.  Sonda reguły lub kondycji pracy nie jest wymagana.  Podstawowa jednostka SKU SNAT zawsze programów SNAT dla obu protokołu transportowego IP niezależnie od protokołu transportowego, określone w reguły równoważenia obciążenia.

Azure preallocates SNAT portów z konfiguracją adresów IP karty sieciowej, każdej maszyny wirtualnej. Po dodaniu konfiguracji adresu IP do puli porty SNAT są wstępnie przydzielonych dla tej konfiguracji adresu IP na podstawie rozmiaru puli zaplecza. Po utworzeniu przepływy wychodzące [osobisty token dostępu](#pat) dynamicznie zużywa (w granicach przydzielony wstępnie) i zwalnia te porty, gdy strumień zostanie zamknięty lub [przekroczeń limitu czasu bezczynności (%)](#idletimeout) się zdarzyć.

W poniższej tabeli przedstawiono preallocations portu SNAT dla warstw rozmiary pul zaplecza:

| Rozmiar puli (wystąpień maszyn wirtualnych) | Liczba przydzielonych wstępnie SNAT portów przypadająca na konfigurację adresu IP|
| --- | --- |
| 1-50 | 1,024 |
| 51-100 | 512 |
| 101-200 | 256 |
| 201-400 | 128 |
| 401-800 | 64 |
| 801-1,000 | 32 |

>[!NOTE]
> Gdy użycie standardowego modułu równoważenia obciążenia za pomocą [wiele frontonów](load-balancer-multivip-overview.md), każdy adres IP frontonu mnoży liczbę dostępnych portów SNAT w powyższej tabeli. Na przykład pula zaplecza 50 maszyn wirtualnych przy użyciu 2 reguły równoważenia obciążenia, każdy z adresu IP frontonu oddzielne użyje portów SNAT 2048 (2 x 1024) na konfigurację adresu IP. Zobacz szczegóły dotyczące [wiele frontonów](#multife).

Należy pamiętać, że liczba dostępnych portów SNAT nie przekłada się bezpośrednio do liczba przepływów. Pojedynczy port SNAT można ponownie wielu unikatowe miejsca docelowe. Porty są używane tylko wtedy, gdy jest to niezbędne do zapewnienia unikatowych przepływów. Aby uzyskać wskazówki dotyczące projektowania i ograniczania ryzyka, zapoznaj się z sekcją o [sposób zarządzać tym zasobem wyczerpującymi](#snatexhaust) i sekcji, która opisuje [osobisty token dostępu](#pat).

Zmiana rozmiaru puli zaplecza może mieć wpływ na niektóre ustanowionych przepływów. Jeśli rozmiar puli wewnętrznej bazy danych zwiększa się i przechodzi do następnej warstwy, połowa swoje przydzielony wstępnie portów SNAT są odzyskiwane podczas przejścia do następnej warstwie większych puli zaplecza. Przepływy, które są skojarzone z portem SNAT odzyskiwanego przekroczy limit czasu i trzeba je ponownie ustanowić. Jeśli nastąpi próba utworzenia nowego przepływu przepływ zakończy się pomyślnie natychmiast tak długo, jak przydzielony wstępnie porty są dostępne.

Jeśli zmniejsza rozmiar puli wewnętrznej bazy danych i zwiększa liczbę dostępnych portów SNAT przejścia do niższej warstwy. W tym przypadku SNAT istniejące przydzielonych portów i ich odpowiednich przepływów nie ulegają zmianom.

SNAT porty są określonego protokołu transportowego IP (TCP i UDP są obsługiwane osobno) i są wydawane w następujących warunkach:

### <a name="tcp-snat-port-release"></a>Wersja port TCP SNAT

- Jeśli albo serwerze/kliencie wysyła FINACK, SNAT port zostaną wydane po 240 sekundach.
- Jeśli występuje RST, SNAT port zostaną wydane po 15 sekundach.
- Jeśli zostanie osiągnięty limit czasu bezczynności portu jest zwalniana.

### <a name="udp-snat-port-release"></a>Wersja port UDP SNAT

- Jeśli zostanie osiągnięty limit czasu bezczynności portu jest zwalniana.

## <a name="problemsolving"></a> Rozwiązywanie problemów 

W tej sekcji ma na celu ułatwić uniknięcie wyczerpania SNAT i który może wystąpić w przypadku połączeń wychodzących na platformie Azure.

### <a name="snatexhaust"></a> Zarządzanie wyczerpanie portów SNAT (PAT)
[Porty efemeryczne](#preallocatedports) umożliwiający [osobisty token dostępu](#pat) są wyczerpującymi zasobów zgodnie z opisem w [autonomicznych maszyn wirtualnych, bez adresu publicznym adresem IP na poziomie wystąpienia](#defaultsnat) i [ze zrównoważonym obciążeniem maszynę Wirtualną bez Wystąpienie poziomu publiczny adres IP](#lb).

Jeśli wiesz, że one inicjowanie wielu TCP lub UDP na połączenia wychodzące przez ten sam docelowy adres IP i port, i obserwować niepowodzenie połączenia wychodzące lub doradza przez dział pomocy technicznej już wyczerpuje porty SNAT (wstępnie przydzielonych [efemeryczne porty](#preallocatedports) posługują się [osobisty token dostępu](#pat)), masz kilka opcji ogólnych środki zaradcze. Zapoznaj się z tymi opcjami i określić, jaka jest dostępna i najlepsze w przypadku danego scenariusza. Istnieje możliwość, że co najmniej jeden ułatwiają zarządzanie tego scenariusza.

Jeśli problemy z zrozumienie zachowania połączeń wychodzących można użyć statystyki stos IP (netstat). Lub może być przydatne do przestrzegania zachowania połączenia przy użyciu przechwytywania pakietów. Możesz wykonać te przechwytywania pakietów w systemie operacyjnym gościa wystąpienia lub użyć [usługi Network Watcher przechwytywania pakietów](../network-watcher/network-watcher-packet-capture-manage-portal.md).

#### <a name="connectionreuse"></a>Modyfikowanie aplikacji to ponowne użycie połączenia 
Można zmniejszyć zapotrzebowanie na portów tymczasowych, które są używane do SNAT dzięki ponownemu wykorzystaniu połączeń w aplikacji. Jest to szczególnie istotne dla protokołów, takich jak HTTP/1.1, których ponowne użycie połączenia jest ustawieniem domyślnym. I innych protokołów, które korzystają z protokołu HTTP jako transportu (na przykład, interfejs API REST) mogą korzystać z kolei. 

Ponowne użycie zawsze jest lepsza niż osobę, atomic połączeń TCP dla każdego żądania. Ponowne użycie skutkuje wydajniej bardzo wydajny transakcji protokołu TCP.

#### <a name="connection pooling"></a>Modyfikowanie aplikacji do korzystania z puli połączeń
Można używać połączenia, buforowanie schemat w aplikacji, w której żądania wewnętrznie są rozsyłane ustalony zestaw elementów połączenia (każdego ponowne użycie w przypadku, gdy to możliwe). Ten schemat ogranicza liczbę portów tymczasowych w użyciu i tworzy środowisko bardziej przewidywalne. Ten schemat można również zwiększyć przepływność żądań, umożliwiając wiele jednoczesnych operacji, gdy blokuje przez pojedyncze połączenie w przypadku odpowiedzi operacji.  

Pula połączeń może już istnieć w ramach używanej do tworzenia aplikacji lub ustawienia konfiguracji dla aplikacji. Można łączyć, pula połączeń o ponowne użycie połączenia. Wiele żądań następnie zużyć je stałą, przewidywalną liczbę portów, które mają ten sam docelowy adres IP i port. Żądania, która jest również korzystać z efektywne wykorzystanie transakcji TCP zmniejszenie opóźnień i użycia zasobów. Transakcje UDP mogą również zyskać, ponieważ zarządzanie liczba przepływów UDP mogą z kolei uniknąć warunków spalin oraz zarządzać nimi SNAT użycie portu.

#### <a name="retry logic"></a>Modyfikowanie aplikacji do korzystania z łagodniej logikę ponawiania próby
Gdy [wstępnie przydzielonych portów tymczasowych](#preallocatedports) umożliwiający [osobisty token dostępu](#pat) są aplikacji lub został wyczerpany wystąpią błędy, wymuś agresywne lub atak ponawia próbę bez zanikania i wycofywania logiki spowodować wyczerpanie wystąpienia lub zachować. Można zmniejszyć zapotrzebowanie na portów tymczasowych za pomocą łagodniej logikę ponawiania próby. 

Porty efemeryczne ma 4-minutowy limit czasu bezczynności (nie można dostosować). W przypadku zbyt agresywne ponowne próby wyczerpanie ma możliwość wyczyszczenia samodzielnie. W związku z tym biorąc pod uwagę sposób — i jak często — aplikacja ponawia próbę transakcji jest kluczową częścią projektu.

#### <a name="assignilpip"></a>Przypisywanie wystąpienia poziomu publicznego adresu IP do każdej maszyny Wirtualnej
Przypisywanie ILPIP zmienia dany scenariusz [wystąpienia poziom publicznego adresu IP do maszyny Wirtualnej](#ilpip). Wszystkie porty efemeryczne publicznego adresu IP, które są używane dla każdej maszyny Wirtualnej są dostępne dla maszyny Wirtualnej. (W przeciwieństwie do scenariuszy, w którym portów tymczasowych z publicznym adresem IP są udostępniane wszystkie maszyny wirtualne skojarzone z pulą zaplecza odpowiednich.) Brak kompromisów należy wziąć pod uwagę, takie jak dodatkowych kosztów publicznych adresów IP i potencjalnego wpływu umieszczania na białej liście dużej liczby poszczególnych adresów IP.

>[!NOTE] 
>Ta opcja nie jest dostępne dla ról procesów roboczych w sieci web.

#### <a name="multifesnat"></a>Użyj wiele frontonów

Korzystając z publicznego standardowego modułu równoważenia obciążenia, Przypisz [wiele adresów IP frontonu dla połączeń wychodzących](#multife) i [należy pomnożyć liczbę dostępnych portów SNAT](#preallocatedports).  Utwórz konfigurację adresu IP frontonu, reguły i pulę zaplecza, aby wyzwolić programowanie SNAT na publiczny adres IP frontonu.  Reguła nie musi działać i sondę kondycji nie niezbędnych do osiągnięcia sukcesu.  Jeśli używasz wielu frontonów dla ruchu przychodzącego oraz (a nie tylko dla ruchu wychodzącego), należy użyć niestandardowych sond kondycji oraz w celu zapewnienia niezawodności.

>[!NOTE]
>W większości przypadków wyczerpanie portów SNAT jest znakiem zły projektu.  Upewnij się, że rozumiesz, dlaczego są porty wyczerpuje przed użyciem frontonów więcej, aby dodać porty SNAT.  Może być maskowanie problemu, co może prowadzić do awarii później.

#### <a name="scaleout"></a>Skalowanie w poziomie

[Wstępnie przydzielonych portów](#preallocatedports) są przypisywane w zależności od rozmiaru puli zaplecza i zgrupowane w warstwach, aby zminimalizować zakłócenia, gdy niektóre z portów, zostają przeniesione w celu uwzględnienia następnej warstwy rozmiar większych puli zaplecza.  Masz możliwość zwiększenia intensywności SNAT użycie portu dla danego serwera sieci Web przy użyciu skalowania puli wewnętrznej bazy danych do maksymalnego rozmiaru dla danej warstwy.  Wymaga to aplikacja może efektywnie skalować w poziomie.

Na przykład dwie maszyny wirtualne w puli zaplecza musi 1024 SNAT portów dostępnych na konfiguracji IP, co daje w sumie 2048 SNAT portów dla wdrożenia.  Gdyby można zwiększyć do 50 wirtualnego wdrożenie maszyn, nawet wtedy, gdy liczba portów pozostaje stała na maszynę wirtualną, w sumie 51,200 (50 x 1024) wstępnie przydzielonych portów SNAT może służyć przez wdrożenie.  Jeśli chcesz skalować wdrożenie, sprawdź liczbę [wstępnie przydzielonych portów](#preallocatedports) na warstwę, aby upewnić się, że kształt usługi skalowania w poziomie do maksymalnej liczby odpowiedniej warstwy.  W poprzednim przykładzie Jeżeli wybrano skalowanie w poziomie do 51 zamiast 50 wystąpień, czy postępy do następnej warstwy i na końcu się przy użyciu mniejszej liczby portów SNAT na maszynę Wirtualną oraz jak Suma.

Skalowanie do następnej warstwy rozmiar większych puli wewnętrznej bazy danych czy potencjalną niektórych połączeń wychodzących przekroczenie limitu czasu Jeśli przydzielonych portów odbiorczego.  Jeśli używasz tylko niektóre z portów SNAT, skalowanie w poziomie na następny większy rozmiar puli wewnętrznej bazy danych nie jest istotny.  Połowa istniejących portów zostanie przesunięte na każdym razem, gdy przeniesiesz do następnej warstwy wewnętrznej bazy danych w puli.  Jeśli nie ma to miejsce, musisz kształt wdrożenia do warstwy o rozmiarze.  Lub aplikacji można wykryć i spróbuj ponownie gdy jest to konieczne.  TCP keepalives mogą pomóc w Wykryj, kiedy użyć funkcji SNAT względem porty nie są już funkcji z powodu przydzielany.

### <a name="idletimeout"></a>Użyj keepalives, aby zresetować wychodzącego limitu czasu bezczynności

Połączenia wychodzące ma 4-minutowy limit czasu bezczynności. Limit czasu nie jest zmieniane. Jednak można użyć transportu (na przykład keepalives TCP) lub warstwy aplikacji keepalives odświeżanie bezczynności przepływu i zresetować ten limit czasu bezczynności, jeśli to konieczne.  

Przy użyciu protokołu TCP keepalives, wystarczy je włączyć na jednej stronie połączenia. Na przykład jest wystarczające, aby je włączyć po stronie serwera, tylko po to, aby zresetować czasomierza bezczynności przepływu i nie jest konieczne dla obu stron inicjowane keepalives TCP.  Istnieją podobne pojęcia warstwy aplikacji, w tym konfiguracji klient serwer bazy danych.  Sprawdź po stronie serwera, na jakie opcje istnieje dla keepalives określonych aplikacji.

## <a name="discoveroutbound"></a>Odnajdywania publicznego adresu IP, który korzysta z maszyny Wirtualnej
Istnieje wiele sposobów, aby określić źródłowy publiczny adres IP połączenia wychodzącego. OpenDNS zapewnia usługę, który można wyświetlić publiczny adres IP swojej maszyny wirtualnej. 

Za pomocą polecenia nslookup, możesz wysłać zapytanie DNS dotyczące myip.opendns.com nazwa OpenDNS program rozpoznawania nazw. Usługa zwraca źródłowy adres IP, który został użyty do wysłania zapytania. Po uruchomieniu następującego zapytania z maszyny Wirtualnej, odpowiedź jest publiczny adres IP używany dla tej maszyny Wirtualnej:

    nslookup myip.opendns.com resolver1.opendns.com

## <a name="preventoutbound"></a>Co uniemożliwia nawiązanie połączenia wychodzące
Czasami jest niepożądane dla maszyny Wirtualnej mają być dozwolone, aby utworzyć przepływ ruchu wychodzącego. Może być wymagane do zarządzania, które miejsc docelowych można osiągnąć za pomocą przepływy wychodzące lub które miejsc docelowych można rozpocząć przepływy ruchu przychodzącego. W takim przypadku można użyć [sieciowe grupy zabezpieczeń](../virtual-network/security-overview.md) Zarządzanie miejsc docelowych, które maszyna wirtualna może nawiązać połączenie. Sieciowe grupy zabezpieczeń służy również do zarządzania, które publiczne miejsce docelowe mogą inicjować przepływy ruchu przychodzącego.

Gdy sieciowa grupa zabezpieczeń zostanie zastosowany do maszyn wirtualnych ze zrównoważonym obciążeniem, które należy zwrócić uwagę [tagów usług](../virtual-network/security-overview.md#service-tags) i [domyślnych regułach zabezpieczeń](../virtual-network/security-overview.md#default-security-rules). Należy się upewnić, czy maszyna wirtualna może odbierać żądań sondy kondycji usługi Azure Load Balancer. 

Jeśli sieciowa grupa zabezpieczeń blokuje żądania sondy kondycji z tagu domyślnego AZURE_LOADBALANCER, sondy kondycji Twojej maszyny Wirtualnej nie powiedzie się i maszyna wirtualna jest oznaczana w dół. Moduł równoważenia obciążenia przestaje wysyłania nowych przepływów do tej maszyny Wirtualnej.

## <a name="limitations"></a>Ograniczenia
- DisableOutboundSnat nie jest dostępny jako opcja, podczas konfigurowania reguły w portalu usługi równoważenia obciążenia.  Zamiast tego użyj narzędzia REST, szablonu lub klienta.
- Role procesów roboczych w sieci Web bez sieci wirtualnej i innych usług platformy Microsoft może być dostępny, gdy tylko wewnętrznego standardowego modułu równoważenia obciążenia jest używana z powodu efekt uboczny z jak pre-sieć wirtualna usługi i innych platform services — funkcja. Nie należy polegać na ten efekt uboczny jako sama usługa odpowiednich lub podstawowej platformy mogą ulec zmianie bez powiadomienia. Zawsze należy zakładać, że należy jawnie utworzyć łączności wychodzącej w razie potrzeby, korzystając z wewnętrznego standardowego modułu równoważenia obciążenia tylko. [Domyślne SNAT](#defaultsnat) Scenariusz 3 opisane w tym artykule nie jest dostępna.

## <a name="next-steps"></a>Kolejne kroki

- Dowiedz się więcej o [usłudze Load Balancer w warstwie Standardowa](load-balancer-standard-overview.md).
- Dowiedz się więcej o [reguł dla ruchu wychodzącego](load-balancer-outbound-rules-overview.md) dla standardowego publicznego modułu równoważenia obciążenia.
- Dowiedz się więcej o [modułu równoważenia obciążenia](load-balancer-overview.md).
- Dowiedz się więcej o [sieciowe grupy zabezpieczeń](../virtual-network/security-overview.md).
- Poznaj inne kluczowe [możliwości sieciowe](../networking/networking-overview.md) na platformie Azure.
