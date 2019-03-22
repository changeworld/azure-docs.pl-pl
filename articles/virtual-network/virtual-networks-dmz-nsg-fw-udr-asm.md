---
title: Przykład sieci obwodowej — tworzenie sieci obwodowej do ochrony sieci z zapory, przez i NSG | Dokumentacja firmy Microsoft
description: Tworzenie sieci obwodowej za pomocą zapory, zdefiniowane przez użytkownika routingu (przez) i grupy zabezpieczeń sieci (NSG)
services: virtual-network
documentationcenter: na
author: tracsman
manager: rossort
editor: ''
ms.assetid: dc01ccfb-27b0-4887-8f0b-2792f770ffff
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/01/2016
ms.author: jonor;sivae
ms.openlocfilehash: 9632bd339956aff7558461ed391cdd21c92f06ad
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/18/2019
ms.locfileid: "57995188"
---
# <a name="example-3--build-a-dmz-to-protect-networks-with-a-firewall-udr-and-nsg"></a>Przykład 3 — Tworzenie sieci obwodowej do ochrony sieci z zapory, przez i grupy NSG
[Wróć do strony zabezpieczeń granic najlepsze praktyki][HOME]

W tym przykładzie utworzy sieć obwodową z zapory, cztery serwery z systemem windows, użytkownik zdefiniowane routingu, przesyłania dalej protokołu IP i grup zabezpieczeń sieci. On również przeprowadzi każdego odpowiednie polecenia, aby zapewnić lepiej zrozumieć każdego kroku. Jest również sekcji scenariusza ruchu zapewnienie szczegółowe instrukcje, jak ruchu obejmującego warstw zabezpieczeń w sieci obwodowej. Na koniec w odwołaniach sekcja jest kompletny kod i instrukcje umożliwiające tworzenie tego środowiska do testowania i eksperymentowania z różnymi scenariuszami. 

![Dwukierunkowe sieci obwodowe NVA, NSG i przez][1]

## <a name="environment-setup"></a>Konfigurowanie środowiska
W tym przykładzie ma subskrypcję, która zawiera następujące elementy:

* Trzy usługi w chmurze: "SecSvc001", "FrontEnd001" i "BackEnd001"
* Sieć wirtualna "CorpNetwork", z trzema podsieciami: "SecNet", "Fronton" i "Wewnętrzna"
* Wirtualne urządzenie sieciowe, w tym przykładzie Zapora połączona z podsiecią SecNet
* Windows Server, który reprezentuje serwer aplikacji sieci web ("IIS01")
* Serwery dwa okna, które reprezentują aplikacji z powrotem zakończenia serwerów ("AppVM01", "AppVM02")
* Serwer Windows, który reprezentuje serwer DNS ("DNS01")

W poniższej sekcji odwołania są skrypt programu PowerShell, który zostanie skompilowany większość środowiska opisanych powyżej. Tworzenie maszyn wirtualnych i sieci wirtualne, chociaż są wykonywane tylko przez przykładowy skrypt, nie opisano szczegółowo w tym dokumencie.

Aby skompilować środowiska:

1. Zapisz plik xml konfiguracji sieci znajdujących się w części odwołania (zaktualizowane przy użyciu nazwy, lokalizacji i adresów IP adresy, które mają odpowiadać danym scenariuszu)
2. Zaktualizuj zmienne użytkownika w skrypcie, aby dopasować środowiska, które skrypt ma być uruchamiana względem (subskrypcji, nazwy usług itp.)
3. Uruchom skrypt programu PowerShell

**Uwaga**: Region oznaczony w skrypcie programu PowerShell musi odpowiadać oznaczony w pliku xml konfiguracji sieci.

Poniższe kroki skryptu używanego po utworzeniu mogą zostać podjęte, gdy skrypt zostanie uruchomiony pomyślnie:

1. Konfigurowanie reguł zapory, znajdują się w sekcji poniżej: Opis reguły zapory.
2. Opcjonalnie w sekcji odwołań są dwa skrypty do konfiguracji serwera sieci web oraz serwer aplikacji z prostą aplikację sieci web umożliwia testowanie za pomocą tej konfiguracji sieci obwodowej.

Po pomyślnym uruchomieniu skryptu zapory, których zasady będą musieli wykonać znajdują się w sekcji: Reguły zapory.

## <a name="user-defined-routing-udr"></a>Zdefiniowane przez użytkownika routingu (trasy zdefiniowanej przez użytkownika)
Domyślnie następujące trasy systemowe są definiowane jako:

        Effective routes : 
         Address Prefix    Next hop type    Next hop IP address Status   Source     
         --------------    -------------    ------------------- ------   ------     
         {10.0.0.0/16}     VNETLocal                            Active   Default    
         {0.0.0.0/0}       Internet                             Active   Default    
         {10.0.0.0/8}      Null                                 Active   Default    
         {100.64.0.0/10}   Null                                 Active   Default    
         {172.16.0.0/12}   Null                                 Active   Default    
         {192.168.0.0/16}  Null                                 Active   Default

VNETLocal jest zawsze prefiksy adresów zdefiniowanych sieci wirtualnej dla tej określonej sieci (ie zmieni z sieci wirtualnej do sieci wirtualnej w zależności od tego, jak jest zdefiniowany każdej określonej sieci wirtualnej). Pozostałe tras systemowych są statyczne i domyślne opisanych powyżej.

Jak w przypadku priorytetu trasy są przetwarzane za pomocą metody najdłuższego prefiksu dopasowania (LPM), w związku z tym naliczona zostałaby bardziej konkretny od pozostałych trasy w tabeli do zadanego miejsca docelowego adresu.

W związku z tym ruch w sieci (na przykład do serwera DNS01 10.0.2.4) do sieci lokalnej (10.0.0.0/16) może być kierowany w sieci wirtualnej do miejsca docelowego, z powodu trasy 10.0.0.0/16. Innymi słowy, w przypadku 10.0.2.4, trasy 10.0.0.0/16 jest najbardziej określoną trasę, mimo że 10.0.0.0/8 i 0.0.0.0/0 również można zastosować, ale ponieważ są one mniej określonych nie wpływają one na ten ruch. Dlatego ruch do 10.0.2.4 mają następnym przeskokiem do lokalnej sieci wirtualnej i po prostu trasy do miejsca docelowego.

Jeśli ruch jest przeznaczona do 10.1.1.1, na przykład, w takich sytuacjach przydałaby się trasy 10.0.0.0/16, ale 10.0.0.0/8 będzie najczęściej określonych i ruch będzie to porzucić ("black holed"), ponieważ następny przeskok ma wartość Null. 

Jeśli miejsce docelowe nie można zastosować do dowolnego z prefiksami o wartości Null lub prefiksy VNETLocal, a następnie wykonać co najmniej określonej trasy, 0.0.0.0/0 oraz kierować z Internetem w następnym skoku, a tym samym poziomie granicy Internetu platformy Azure.

Jeśli istnieją dwie identyczne prefiksy w tabeli tras, są następujące elementy w kolejności preferencji na podstawie atrybutu "źródło" trasy:

1. "VirtualAppliance" = ręcznie dodawane do tabeli trasy zdefiniowany przez użytkownika
2. "Bramy VPNGateway" = dynamiczny trasy protokołu BGP (gdy jest używane z sieci hybrydowe), dodane przez protokół dynamicznej sieci, te trasy może zmienić wraz z upływem czasu Protokół dynamicznej automatycznie uwzględnia zmiany w sieci równorzędnej
3. "Default" = trasy systemowe i lokalnej sieci wirtualnej i statyczne wpisy, jak pokazano w powyższej tabeli tras.

> [!NOTE]
> Można teraz używać zdefiniowaną przez użytkownika routingu (UDR) przy użyciu usługi ExpressRoute i bramy sieci VPN, aby wymusić wychodzące i przychodzące między środowiskami lokalnymi ruch do wirtualnego urządzenia sieciowego (WUS).
> 
> 

#### <a name="creating-the-local-routes"></a>Tworzenie tras lokalnych
W tym przykładzie są potrzebne dwie tabele routingu, jeden dla każdej z podsieciami frontonu i wewnętrznej bazy danych. Każda tabela jest załadowana trasy statyczne, które są odpowiednie dla danej podsieci. Na potrzeby tego przykładu każda tabela zawiera trzy trasy:

1. Ruchu w podsieci lokalnej za pomocą następnego skoku zdefiniowanymi tak, aby zezwolić na ruch z podsieci lokalnej ominąć zaporę
2. Ruchu w sieci wirtualnej za pomocą następnego skoku zdefiniowany jako zapora, ustawienie to zastępuje domyślną regułę zezwalającą na lokalny ruch w sieci wirtualnej kierować bezpośrednio
3. Pozostałe cały ruch (0/0) za pomocą następnego skoku zdefiniowany jako zapora

Po utworzeniu tabele routingu są powiązane swoje podsieci. Dla podsieci Frontend tabeli routingu, jeden raz utworzona i powiązane z podsieci powinien wyglądać następująco:

        Effective routes : 
         Address Prefix    Next hop type    Next hop IP address Status   Source     
         --------------    -------------    ------------------- ------   ------     
         {10.0.1.0/24}     VNETLocal                            Active 
         {10.0.0.0/16}     VirtualAppliance 10.0.0.4            Active    
         {0.0.0.0/0}       VirtualAppliance 10.0.0.4            Active


Na przykład następujące polecenia służą do tworzenia tabeli tras, Dodaj trasy zdefiniowanej przez użytkownika, a następnie powiązania tabeli tras do podsieci (Uwaga; wszystkie elementy poniżej zaczynający się od znaku dolara (np.: $BESubnet) są zmienne zdefiniowane przez użytkownika ze skryptu w Odwołanie do sekcji tego dokumentu):

1. Najpierw należy utworzyć podstawowej tabeli routingu. Ten fragment kodu przedstawia tworzenie tabeli podsieci wewnętrznej bazy danych. W skrypcie tworzony jest także odpowiedniej tabeli dla podsieci frontonu.

   ```powershell
   New-AzureRouteTable -Name $BERouteTableName `
       -Location $DeploymentLocation `
       -Label "Route table for $BESubnet subnet"
   ```

2. Po utworzeniu tabeli tras można dodać trasy zdefiniowane przez określonego użytkownika. W tym odbierającej cały ruch (0.0.0.0/0) będą kierowane za pośrednictwem urządzenia wirtualnego (zmienną $VMIP [0] jest używany do przekazywania adresu IP przypisanego podczas tworzenia urządzenia wirtualnego wcześniej w skrypcie). W skrypcie odpowiednia reguła jest tworzona w tabeli frontonu.

   ```powershell
   Get-AzureRouteTable $BERouteTableName | `
       Set-AzureRoute -RouteName "All traffic to FW" -AddressPrefix 0.0.0.0/0 `
       -NextHopType VirtualAppliance `
       -NextHopIpAddress $VMIP[0]
   ```

3. Powyższe wejście dla trasy przesłonią "0.0.0.0/0" trasy domyślnej, ale domyślna reguła 10.0.0.0/16 nadal istniejące umożliwiające ruch sieciowy w ramach sieci wirtualnej w celu kierowania bezpośrednio do miejsca docelowego, a nie wirtualnego urządzenia sieciowego. Aby poprawne to zachowanie reguły postępuj zgodnie z musi zostać dodany.

   ```powershell
   Get-AzureRouteTable $BERouteTableName | `
       Set-AzureRoute -RouteName "Internal traffic to FW" -AddressPrefix $VNetPrefix `
       -NextHopType VirtualAppliance `
       -NextHopIpAddress $VMIP[0]
   ```

4. W tym momencie jest wybór ma zostać wykonane. Za pomocą powyższych dwóch tras cały ruch będzie kierować do zapory dla oceny, nawet ruch sieciowy w ramach pojedynczej podsieci. To może być wskazane, jednak, aby zezwolić na ruch w obrębie podsieci do rozsyłania lokalnie bez angażowania w zaporze trzeci, można dodać bardzo szczegółowych reguł. Ta trasa stanów, które każdy adres destine dla lokalnej podsieci można po prostu ma kierować bezpośrednio (Typ następnego przeskoku = VNETLocal).

   ```powershell
   Get-AzureRouteTable $BERouteTableName | `
       Set-AzureRoute -RouteName "Allow Intra-Subnet Traffic" -AddressPrefix $BEPrefix `
           -NextHopType VNETLocal
   ```

5. Na koniec z tabeli routingu utworzony i wypełniony tras zdefiniowanych przez użytkownika, tabela musi teraz być powiązana z podsiecią. W skrypcie tabeli tras frontonu również jest powiązana z podsiecią Frontend. Oto skrypt powiązania dla podsieci zaplecza.

   ```powershell
   Set-AzureSubnetRouteTable -VirtualNetworkName $VNetName `
       -SubnetName $BESubnet `
       -RouteTableName $BERouteTableName
   ```

## <a name="ip-forwarding"></a>Przesyłanie dalej IP
Funkcja pomocnika trasy zdefiniowanej przez użytkownika, to przekazywanie adresów IP. To ustawienie na urządzenie wirtualne, które umożliwia odbieranie ruchu nie jest skierowana do urządzenia, a następnie przekazują ruch do ostatecznego miejsca przeznaczenia.

Na przykład jeśli ruch z AppVM01 kieruje żądanie do serwera DNS01 trasy zdefiniowanej przez użytkownika będzie kierować to do zapory. Z włączoną funkcją przekazywania adresów IP ruch dla miejsca docelowego DNS01 (10.0.2.4) zostaną zaakceptowane przez urządzenie (10.0.0.4) i następnie przekazywane do ostatecznego miejsca docelowego (10.0.2.4). Bez przekazywania adresów IP jest włączona Zapora ruch nie była akceptowana przez urządzenie, mimo że tabela tras ma zapory w następnym skoku. 

> [!IMPORTANT]
> Koniecznie pamiętać umożliwić przekazywanie adresów IP w połączeniu z routingu zdefiniowanego użytkownika.
> 
> 

Konfigurowanie przekazywania adresów IP jest pojedyncze polecenie i może odbywać się podczas tworzenia maszyny Wirtualnej. Przepływ w tym przykładzie fragment kodu jest pod koniec skryptu i zgrupowane za pomocą poleceń trasy zdefiniowanej przez użytkownika:

1. Wywołać wystąpienia maszyny Wirtualnej, który w tym przypadku jest urządzenie wirtualne zapory, a następnie włączyć funkcję przekazywania adresów IP (Uwaga; dowolny element w czerwonym zaczynający się od znaku dolara (np.: $VMName[0]) jest zdefiniowane przez użytkownika zmienną ze skryptu zamieszczone w tej sekcji niniejszego dokumentu. Zero w nawiasie [0] reprezentuje pierwszą maszynę Wirtualną w tablicy, której maszyny wirtualne, aby uzyskać przykładowy skrypt działać bez żadnych modyfikacji, pierwsza maszyna wirtualna (VM, 0) muszą być zapory):

    ```powershell
    Get-AzureVM -Name $VMName[0] -ServiceName $ServiceName[0] | `
        Set-AzureIPForwarding -Enable
    ```

## <a name="network-security-groups-nsg"></a>Sieciowe grupy zabezpieczeń (NSG)
W tym przykładzie grupa NSG jest wbudowana i następnie ładowane przy użyciu jednej reguły. Ta grupa jest następnie powiązany tylko z podsieciami frontonu i wewnętrznej bazy danych (nie SecNet). Deklaratywne budowanego następującą regułę:

1. Odmowa cały ruch (wszystkich portów) z Internetu do całej sieci wirtualnej (wszystkie podsieci)

Mimo że sieciowe grupy zabezpieczeń są używane w tym przykładzie, jego głównym celem jest jako dodatkowej warstwy obrony przed ręczne błędnej konfiguracji. Chcemy zablokować wszystkie przychodzący ruch z Internetu do każdego frontonu lub podsieci wewnętrznej bazy danych, ruch tylko powinna przepływać przez podsieci SecNet do zapory (i jeśli następnie właściwe frontonu lub wewnętrznej bazy danych podsieci). Ponadto za pomocą reguł trasy zdefiniowanej przez użytkownika w miejscu, cały ruch, która została wprowadzona do podsieci frontonu lub wewnętrznej bazy danych będzie nastąpi przekierowanie się do zapory (dzięki trasy zdefiniowanej przez użytkownika). Zapora widział to jako przepływ asymetryczne i będzie pomijać ruch wychodzący. Dlatego istnieją trzy warstwy zabezpieczeń, ochrony z podsieciami frontonu i wewnętrznej bazy danych; (1) otwórz punktów końcowych na FrontEnd001 i BackEnd001 usługi w chmurze, (2) sieciowe grupy zabezpieczeń odmawianie ruch z Internetu, (3) zapory usuwanie asymetrycznego ruch.

Jeden punkt interesujące dotyczące sieciowej grupy zabezpieczeń, w tym przykładzie jest to, czy zawiera on tylko jedną regułę, pokazano poniżej, aby zablokować ruch internetowy do całej sieci wirtualnej, która obejmuje podsieci zabezpieczeń, czyli. 

```powershell
Get-AzureNetworkSecurityGroup -Name $NSGName | `
    Set-AzureNetworkSecurityRule -Name "Isolate the $VNetName VNet `
    from the Internet" `
    -Type Inbound -Priority 100 -Action Deny `
    -SourceAddressPrefix INTERNET -SourcePortRange '*' `
    -DestinationAddressPrefix VIRTUAL_NETWORK `
    -DestinationPortRange '*' `
    -Protocol *
```

Jednak ponieważ sieciowa grupa zabezpieczeń jest powiązana tylko z podsieci frontonu i zaplecza, reguła nie jest przetwarzane na ruch przychodzący do podsieci zabezpieczeń. W rezultacie mimo że reguła sieciowej grupy zabezpieczeń jest wyświetlany komunikat nie ruch internetowy do dowolnego adresu w sieci wirtualnej, ponieważ sieciowa grupa zabezpieczeń nigdy nie została powiązana z podsiecią zabezpieczeń, ruch będzie przepływać do podsieci zabezpieczeń.

```powershell
Set-AzureNetworkSecurityGroupToSubnet -Name $NSGName `
    -SubnetName $FESubnet -VirtualNetworkName $VNetName

Set-AzureNetworkSecurityGroupToSubnet -Name $NSGName `
    -SubnetName $BESubnet -VirtualNetworkName $VNetName
```

## <a name="firewall-rules"></a>Reguły zapory
Na zaporze reguły przekazywania będzie muszą zostać utworzone. Ponieważ Zapora blokuje lub funkcji przekazywania, wszystkie przychodzące, wychodzące, sieć wirtualną wewnątrz ruchu wiele reguł zapory są wymagane. Ponadto wszystkie ruch przychodzący będzie trafień usługi zabezpieczeń publiczny adres IP (na różne porty) do przetworzenia przez zaporę. Najlepszym rozwiązaniem jest diagram logiczny przepływów, przed rozpoczęciem konfigurowania podsieci i reguły zapory, aby uniknąć przerabiać później. Poniższa ilustracja jest widok logiczny reguł zapory, w tym przykładzie:

![Widok logiczny reguł zapory][2]

> [!NOTE]
> Oparte na wirtualne urządzenie sieciowe używane, porty zarządzania będą się różnić. W tym przykładzie, który odwołuje się do Barracuda NextGen Firewall, który używa porty 22, 801 i 807. Zapoznaj się dokumentacją dostawcy urządzenia, aby znaleźć dokładnie portów używanych do zarządzania urządzeniami używana.
> 
> 

### <a name="logical-rule-description"></a>Opis reguły logiczne
W powyższym diagramie logicznym podsieci zabezpieczeń nie jest wyświetlany, ponieważ Zapora jest tylko zasobów tej podsieci, a ten diagram jest wyświetlane reguły zapory i jak one logicznie blokują lub zezwalają na ruch i nie rzeczywistej ścieżce trasowane. Ponadto portów zewnętrznych wybrany z ruchem RDP są wyższe zakres portów (8014 — 8026) i zostały wybrane do nieco wyrównane z ostatnich dwóch oktety adresu IP lokalnego czytelność (np. adres serwera lokalnego 10.0.1.4 jest skojarzony z portu zewnętrznego 8014), jednak można użyć żadnych portów wyższych bezkonfliktowe.

W tym przykładzie potrzebujemy 7 typów reguł, te typy reguł są opisane w następujący sposób:

* Reguły zewnętrzne (dla ruchu przychodzącego):
  1. Reguły zarządzania zapory: Ta reguła przekierowania aplikacji zezwala na ruch do przekazania do portów zarządzania wirtualnego urządzenia sieciowego.
  2. Reguły protokołu RDP (dla każdego serwera systemu windows): Te cztery reguły (po jednym dla każdego serwera) umożliwi Zarządzanie poszczególnych serwerów za pośrednictwem protokołu RDP. To, można również połączone w jedną regułę, w zależności od możliwości sieciowe urządzenie wirtualne używane.
  3. Reguły ruchu dla aplikacji: Istnieją dwie reguły ruchu aplikacji pierwszy do ruchu w sieci web frontonu, a drugi dla ruchu w sieci wewnętrznej (na przykład serwer sieci web do warstwy danych). Konfiguracji tych zasad będzie zależeć od architektury sieci (w którym są umieszczane serwery) i przepływy ruchu (kierunku przepływu ruchu, a które porty są używane).
     * Pierwsza reguła umożliwi ruch rzeczywisty aplikacji do serwera aplikacji. Inne zasady umożliwiające zabezpieczeń, zarządzania, itp., reguł aplikacji są co umożliwia użytkownikom zewnętrznym lub usług, uzyskać dostęp do aplikacji. W tym przykładzie jest jednym serwerze sieci web na porcie 80, dlatego pojedynczą aplikację regułę zapory przekieruje ruch przychodzący na zewnętrzny adres IP jako wewnętrzny adres IP w sieci web serwerów. Sesja przekierowanego ruchu będzie translatora adresów Sieciowych uwagę do wewnętrznego serwera.
     * Drugą regułę ruchu aplikacji jest zaplecza reguły zezwalającej na serwerze sieci Web na komunikowanie się z serwerem AppVM01 (ale nie AppVM02) za pośrednictwem dowolnego portu.
* Wewnętrzne zasady (dla ruchu między sieciami wirtualnymi wewnątrz)
  1. Ruch wychodzący do Internetu reguły: Ta zasada będzie zezwolić na ruch z żadną siecią do przekazania do wybranych sieci. Ta reguła jest zazwyczaj domyślna reguła już w zaporze, ale w stanie wyłączenia. Ta reguła powinna być włączona w tym przykładzie.
  2. Reguła DNS: Ta reguła zezwala tylko na ruch DNS (port 53) do przekazania do serwera DNS. Dla tego środowiska, których większość ruchu z frontonu do zaplecza jest zablokowane ta zasada wyraźnie dopuszczają DNS z dowolnej podsieci lokalnej.
  3. Reguła podsieci do sieci: Ta reguła jest umożliwienie dowolnego serwera w tej podsieci zaplecza, połączyć się z dowolnego serwera w podsieci frontonu (ale nie odwrotnie).
* Reguła odporne na uszkodzenia (dla ruchu, który nie spełnia żadnego z powyższych):
  1. Odmów wszystkie reguły ruchu: Powinna zawsze być końcowa reguła (w kategoriach priority), a związku z tym jeśli ruch przepływa nie pasuje do żadnej z powyższych zasad, które zostaną usunięte przez tę regułę. Jest to domyślna reguła i zazwyczaj aktywowana, nie są ogólnie potrzebne modyfikacje.

> [!TIP]
> Na drugiej reguły ruchu aplikacji każdy port jest dozwolony dla łatwy w tym przykładzie, w przypadku rzeczywistych najbardziej określonego portu i zakresy adresów powinny być używane do zmniejszyć obszar ataków tej reguły.
> 
> 

<br />

> [!IMPORTANT]
> Po utworzeniu wszystkich powyższych reguł jest ważne zapoznać się z priorytetem każdej reguły, aby upewnić się, ruch będzie dozwolony lub zabroniony zgodnie z potrzebami. W tym przykładzie reguły są w kolejności priorytetów. To ułatwia zostanie zablokowana zapory z powodu nieprawidłowo uporządkowane reguł. Jako minimum upewnij się, że zarządzanie zapory, sama jest zawsze bezwzględne reguły priorytet najwyższy.
> 
> 

### <a name="rule-prerequisites"></a>Reguły wymagań wstępnych
Jeden warunek wstępny dla maszyny wirtualnej z systemem zapory są publiczne punkty końcowe. Zapory do przetwarzania ruchu odpowiednie publicznych punktów końcowych musi być otwarty. Istnieją trzy typy ruchu sieciowego, w tym przykładzie; Ruch protokołu RDP (1) ruch związany z zarządzaniem do kontroli zapory i reguł zapory, 2) w celu kontrolowania serwerów z systemem windows i ruchu (3) aplikacji. Oto trzy kolumny typów ruchu sieciowego w prawym górnym połowę widok logiczny reguł zapory powyżej.

> [!IMPORTANT]
> Należy pamiętać, że jest tutaj kluczy takeway **wszystkich** ruchu pojawią się przez zaporę. Więc do pulpitu zdalnego z serwerem IIS01, mimo że jest w pierwszej usłudze końcowy w chmurze i w podsieci frontonu dostępu do tego serwera firma Microsoft będzie konieczne protokołu RDP do zapory na porcie 8014, a następnie pozwól zaporę, tak aby kierować żądania protokołu RDP wewnętrznie do IIS01 Por protokołu RDP t. Przycisk "Połącz" w witrynie Azure portal nie będą działać, ponieważ nie ma bezpośredniego RDP ścieżki do IIS01 (o ile zobaczyć portalu). Oznacza to, że wszystkie połączenia z Internetem będzie usługa zabezpieczeń i Port, np. secscv001.cloudapp.net:xxxx (odwołanie w powyższym wykresie mapowania portu zewnętrznego i wewnętrznym adresem IP i Port).
> 
> 

Punkt końcowy, można otworzyć zarówno w czasie tworzenia maszyny Wirtualnej lub po kompilacji, jak jest wykonywane w skrypcie przykład i przedstawionym poniżej na następujący fragment kodu (Uwaga; dowolnego elementu rozpoczynający się od znaku dolara (np.: $VMName[$i]) jest zdefiniowane przez użytkownika zmienną ze skryptu w sectio odwołania n tego dokumentu. "$I" w nawiasie [$i] reprezentuje tablicę określonej maszyny Wirtualnej w tablicy maszyn wirtualnych):

```powershell
Add-AzureEndpoint -Name "HTTP" -Protocol tcp -PublicPort 80 -LocalPort 80 `
    -VM (Get-AzureVM -ServiceName $ServiceName[$i] -Name $VMName[$i]) | `
    Update-AzureVM
```

Chociaż nie wyraźnie przedstawiony tutaj ze względu na użycie zmiennych, ale punkty końcowe są **tylko** otworzyć zabezpieczenia usługi w chmurze. Pozwala to upewnić się, że cały ruch przychodzący jest obsługiwane (kierowane, translatora adresów Sieciowych spadły) przez zaporę.

Klient zarządzania będzie muszą być zainstalowane na komputerze do zarządzania zaporą i tworzyć konfiguracje wymagane. Wyświetlone dostawcę dokumentacji zapory (lub innego urządzenia WUS) do zarządzania urządzeniem. W pozostałej części tej sekcji i kolejnej sekcji, tworzenie reguł zapory, opisano konfigurację zapory, za pomocą klienta zarządzania dostawcami, (tj. nie witryny Azure portal lub programu PowerShell).

Instrukcje dotyczące pobierania klienta i nawiązywania połączenia z Barracuda, używany w tym przykładzie można znaleźć tutaj: [Barracuda NG administratora](https://techlib.barracuda.com/NG61/NGAdmin)

Po zalogowaniu się na zaporze, ale przed utworzeniem reguł zapory, istnieją dwie klasy obiektu wymagań wstępnych, które mogą ułatwić tworzenie reguł łatwiejsze. Obiekty usługi & sieci.

W tym przykładzie trzy obiekty sieci o nazwie powinna być zdefiniowana (po jednym dla podsieci frontonu i podsieci wewnętrznej bazy danych, również do obiektu sieciowego dla adresu IP serwera DNS). Aby utworzyć sieć nazwane; począwszy od pulpitu nawigacyjnego klienta Barracuda NG administratora, przejdź na kartę Konfiguracja, w sekcji Konfiguracja operacyjnej kliknij zestaw reguł, kliknij pozycję "Sieci" w menu obiekty zapory, a następnie kliknij pozycję Nowy w menu Edycja sieci. Obiekt network teraz można utworzyć, dodając nazwę i prefiksu:

![Utwórz obiekt sieci frontonu][3]

Spowoduje to utworzenie nazwanego sieci dla podsieci frontonu, należy utworzyć podobne obiektu w podsieci wewnętrznej bazy danych. Teraz podsieci można łatwiej odwoływać się według nazwy w regułach zapory.

Aby uzyskać obiekt serwera DNS:

![Utwórz obiekt serwera DNS][4]

Ten jedno odwołanie adresu IP będą wykorzystywane w regule DNS w dalszej części dokumentu.

Drugi obiekty wymagań wstępnych są obiektami usługi. Te będzie reprezentować portów połączeń protokołu RDP dla każdego serwera. Ponieważ istniejący obiekt usługi RDP jest powiązany z portem RDP domyślne, 3389, nowych usług mogą być tworzone na ruch z portów zewnętrznych (8014 8026). Nowych portów, również może zostać dodany do istniejącej usługi protokołu RDP, ale w celu ułatwienia demonstracji, można utworzyć regułę dla każdego serwera. Aby utworzyć nową regułę protokołu RDP dla serwera; począwszy od pulpitu nawigacyjnego klienta Barracuda NG administratora, przejdź na kartę Konfiguracja, w sekcji Konfiguracja operacyjnej kliknij zestaw reguł, a następnie kliknij pozycję "Usługi" w menu obiekty zapory, przewiń w dół na liście usług i wybierz usługę "RDP". Kliknij prawym przyciskiem myszy wybierz kopię, a następnie kliknij prawym przyciskiem myszy i wybierz pozycję wklejania. Teraz jest obiektem usługi Copy1 protokołu RDP, które można edytować. Kliknij prawym przyciskiem myszy Copy1 protokołu RDP i wybierz pozycję Edytuj, usługa Edytuj obiekt okno zostanie wyświetlone w górę, jak pokazano poniżej:

![Kopiuj domyślna reguła protokołu RDP][5]

Następnie można edytować wartość do reprezentowania usługi protokołu RDP dla określonego serwera. Dla AppVM01 powinien być modyfikowany powyżej Reguła domyślna RDP, aby odzwierciedlały nową nazwę usługi, opis i zewnętrznych portu RDP używanej w diagramie rysunek 8 (Uwaga: porty są zmieniane z domyślna RDP 3389 na porcie zewnętrznym używane dla tego określonego serwera w przypadku AppVM01 porcie zewnętrznym jest 8025) zmodyfikowaną usługę znajdują się poniżej:

![Reguła AppVM01][6]

Ten proces należy powtórzyć do tworzenia usług protokołu RDP dla pozostałych serwerów; AppVM02 DNS01 i IIS01. Tworzenie usługi spowoduje, że tworzenie reguł prostszy i bardziej oczywisty w następnej sekcji.

> [!NOTE]
> Usługi protokołu RDP dla zapory nie są potrzebne do dwóch przyczyn; pierwszy 1), zapory maszyny Wirtualnej jest obrazem opartych na systemie Linux, dzięki czemu SSH będzie używana na porcie 22 dla zarządzania maszyną Wirtualną, a nie protokołu RDP i (2) port 22 i dwa porty zarządzania są dozwolone w pierwszej reguły zarządzania opisane poniżej, aby umożliwić łączność zarządzania.
> 
> 

### <a name="firewall-rules-creation"></a>Tworzenie reguły zapory
Istnieją trzy typy reguł zapory używane w tym przykładzie, wszystkie one mają różne ikony:

Reguła przekierowania aplikacji: ![Ikona przekierowania aplikacji][7]

Reguła NAT do docelowej: ![Ikona translatora adresów Sieciowych docelowego][8]

Reguła — dostęp próbny: ![Przekaż ikonę][9]

Więcej informacji na temat tych reguł można znaleźć w witrynie sieci web firmy Barracuda.

Utworzenie następujących reguł (lub Sprawdź istniejące reguły domyślne), zaczynając od administratora NG Barracuda klienta pulpitu nawigacyjnego, przejdź na kartę Konfiguracja w konfiguracji operacyjnej sekcji kliknij pozycję zestaw reguł. Siatce o nazwie "Main reguły" zostaną wyświetlone istniejące reguły aktywne i nieaktywne na tej zapory. W prawym górnym rogu tej siatce są małe, zielony "+" przycisk, kliknij tutaj, aby utworzyć nową regułę (Uwaga: Zapora może być "zablokowany" zmian, jeśli zostanie wyświetlony przycisk oznaczone jako "Zablokuj" i nie można utworzyć lub edytować reguły, kliknij ten przycisk, aby "Odblokuj" zestaw reguł i  Zezwól na edytowanie). Jeśli chcesz edytować istniejącą regułę, wybierz regułę, że prawym przyciskiem myszy i wybierz pozycję Edytuj regułę.

Gdy reguły są tworzone i/lub zmodyfikowane, musi być wypchnięte do zapory i następnie aktywowany, jeśli nie zostanie to zrobione zmian reguły nie zostały zastosowane. Proces wypychania i aktywacji jest opisane poniżej opisy reguł szczegółowe informacje.

Szczegółowe informacje na temat każdej reguły wymaganiem do wykonania w tym przykładzie opisano w następujący sposób:

* **Zarządzanie reguła zapory**: Ta reguła przekierowania aplikacji zezwala na ruch do przekazania do portów zarządzania sieciowe urządzenie wirtualne, w tym przykładzie zapora Barracuda NextGen. Porty zarządzania są 801 807 i opcjonalnie 22. Porty wewnętrznych i zewnętrznych są takie same (czyli nie tłumaczenie port). Reguły i ustawienia-MGMT-dostęp, reguła domyślna i domyślnie włączona (w wersji Barracuda NextGen Firewall 6.1).
  
    ![Reguły zarządzania zapory][10]

> [!TIP]
> Przestrzeń adresowa źródła w tej regule jest, jeśli zakresy adresów IP zarządzania są znane, co zmniejsza ten zakres będzie również zmniejszyć obszar ataków do portów zarządzania.
> 
> 

* **Reguły protokołu RDP**:  Te reguły translatora adresów Sieciowych docelowego umożliwi Zarządzanie poszczególnych serwerów za pośrednictwem protokołu RDP.
  Istnieją cztery pola krytyczne potrzebne, aby utworzyć tę regułę:
  
  1. Źródło — Zezwalaj na RDP z dowolnego miejsca i odwołania "Dowolne" jest używany w polu źródłowym.
  2. Usługi — użyj odpowiedniego obiektu usługi utworzone wcześniej w tym przypadku "AppVM01 RDP", portów zewnętrznego przekierowania do serwerów lokalny adres IP i port 3386 (domyślny port protokołu RDP). Ta reguła określonych ma dostęp RDP do AppVM01.
  3. Miejsce docelowe — powinno być *lokalny port w zaporze*, "DCHP 1 lokalny adres IP" lub eth0, jeśli za pomocą statycznych adresów IP. Liczbę porządkową (eth0, eth1 itp.) może się różnić, jeśli urządzenia sieciowego ma wiele interfejsów lokalnych. Jest to port zapory wysyła z (może być taki sam jak port odbieranie), rzeczywiste trasowane miejsce docelowe znajduje się w polu listy docelowej.
  4. Przekierowywanie — w tej sekcji informuje urządzenie wirtualne dokąd przekierowywać ostatecznie ten ruch. Najprostsza przekierowania jest umieszczenie adresów IP i portu (opcjonalnie) w polu listy docelowej. Jeśli port nie jest używany port docelowy dla ruchu przychodzącego żądania, będzie używany (ie nie tłumaczenie), jeśli port jest wyznaczony numer portu będzie również translatora adresów Sieciowych oraz adres IP zajmie się.
     
     ![Reguła protokołu RDP][11]
     
     Łącznie cztery reguły protokołu RDP potrzebne do utworzenia: 
     
     | Nazwa reguły | Serwer | Usługa | Listy docelowej |
     | --- | --- | --- | --- |
     | RDP-to-IIS01 |IIS01 |IIS01 RDP |10.0.1.4:3389 |
     | RDP-to-DNS01 |DNS01 |DNS01 RDP |10.0.2.4:3389 |
     | RDP-to-AppVM01 |AppVM01 |AppVM01 protokołu RDP |10.0.2.5:3389 |
     | RDP-to-AppVM02 |AppVM02 |AppVm02 RDP |10.0.2.6:3389 |

> [!TIP]
> Zawężając zakres pola źródłowego i usługi zostaną zmniejszyć obszar narażony na ataki. Najbardziej ograniczonym zakresie, które umożliwi funkcji powinna być używana.
> 
> 

* **Reguły ruchu aplikacji**: Istnieją dwie reguły ruchu aplikacji pierwszy do ruchu w sieci web frontonu, a drugi dla ruchu w sieci wewnętrznej (na przykład serwer sieci web do warstwy danych). Te reguły będzie zależeć od architektury sieci (w którym są umieszczane serwery) i przepływy ruchu (kierunku przepływu ruchu, a które porty są używane).
  
    Najpierw omówione jest reguła frontonu dla ruchu w sieci web:
  
    ![Reguła zapory w sieci Web][12]
  
    Ta reguła NAT do docelowej zezwala na ruch rzeczywiste aplikacje do osiągnięcia serwera aplikacji. Inne zasady umożliwiające zabezpieczeń, zarządzania, itp., reguł aplikacji są co umożliwia użytkownikom zewnętrznym lub usług, uzyskać dostęp do aplikacji. W tym przykładzie jest jednym serwerze sieci web na porcie 80, dlatego pojedynczą aplikację regułę zapory przekieruje ruch przychodzący na zewnętrzny adres IP jako wewnętrzny adres IP w sieci web serwerów.
  
    **Uwaga**: czy istnieje nie portu w polu listy docelowej przypisany, dlatego przychodzący port 80 (lub 443 dla wybranej usługi) będzie używany w przekierowania serwera sieci web. Jeśli serwer sieci web nasłuchuje na innym porcie, na przykład portu 8080, pole listy docelowej może zostać zaktualizowana w celu 10.0.1.4:8080, aby umożliwić na przekierowanie portów.
  
    Następną regułę ruchu aplikacji jest zaplecza reguły zezwalającej na serwerze sieci Web na komunikowanie się z serwerem AppVM01 (ale nie AppVM02) przy użyciu dowolnej usługi:
  
    ![Reguła AppVM01 zapory][13]
  
    Ta zasada — dostęp próbny umożliwia dowolnego serwera usług IIS w podsieci frontonu w celu osiągnięcia AppVM01 (adresu IP 10.0.2.5) na dowolnym porcie przy użyciu dowolnego protokołu dostępu do danych wymagane przez aplikację sieci web.
  
    W tym zrzucie ekranu "\<jawne dest\>" jest używana w pole docelowe oznaczającego 10.0.2.5 jako miejsca docelowego. Może to być albo jawne pokazany lub w nazwie obiektu Network (jak zostało to zrobione w sekcji wymagania wstępne dla serwera DNS). Jest to w gestii administratora zapory, które zostanie użyta metoda. Aby dodać 10.0.2.5 jako jawne miejsce docelowe, kliknij dwukrotnie ikonę na pierwszym pustym wierszu pod \<jawne dest\> i w oknie wyskakującym wprowadź adres.
  
    Z tą regułą przekazać odłączenia translatora adresów Sieciowych jest wymagana, ponieważ jest to ruch wewnętrzny, aby metoda połączenia można ustawić na "Brak SNAT".
  
    **Uwaga**: Sieć źródłowa w tej regule jest dowolnego zasobu w podsieci frontonu, jeśli będzie istnieć tylko jeden lub znanego określonej liczbie serwerów sieci web obiekt sieci można utworzyć zasobu na dokładniejszą do adresów IP dokładnego, a nie całej podsieci frontonu.

> [!TIP]
> Ta reguła jest używana usługa "Any" Przykładowa aplikacja ułatwiają konfigurowanie i używanie, to będzie również umożliwiać ICMPv4 (ping) w jednej reguły. Jednak to nie jest zalecanym rozwiązaniem. Porty i protokoły ("usługi") należy zawężony do minimum, możliwe umożliwiająca operacji aplikacji zmniejszyć obszar narażony na ataki przez tę granicę.
> 
> 

<br />

> [!TIP]
> Mimo że ta reguła zawiera odwołanie jawne dest używany, spójne podejście powinno być używane w całym konfigurację zapory. Zaleca się, że nazwany obiekt sieci używane w całym dla czytelność i obsługi. Jawne dest jest używany w tym miejscu tylko po to, aby wyświetlić alternatywne reference — metoda i ogólnie nie zaleca się (szczególnie w przypadku złożonych konfiguracji).
> 
> 

* **Ruch wychodzący do Internetu reguły**: Ta zasada — dostęp próbny umożliwi ruch z żadną siecią źródła do przekazania do wybranej sieci docelowej. Ta reguła jest domyślna reguła zwykle już na Zapora Barracuda NextGen, ale jest w stanie wyłączenia. Kliknij prawym przyciskiem myszy w tej regule dostęp polecenia reguły aktywacji. Reguła pokazane tutaj została zmodyfikowana, aby dodać dwie podsieci lokalne, które zostały utworzone jako odwołania w sekcji wymagań wstępnych w tym dokumencie z atrybutem źródłowym tej reguły.
  
    ![Reguły ruchu wychodzącego zapory][14]
* **Reguła DNS**: Ta zasada — dostęp próbny zezwala tylko na ruch DNS (port 53) do przekazania do serwera DNS. Dla tego środowiska, których większość ruchu z frontonu do zaplecza jest zablokowane ta zasada umożliwia specjalnie DNS.
  
    ![Reguła DNS][15]
  
    **Uwaga**: W tym zrzucie ekranu dołączono metodę połączenia. Ponieważ ta reguła ma wewnętrzny adres IP, aby ruch wewnętrzny adres IP, NATing nie jest wymagany, to metoda połączenia jest równa "No SNAT" dla tej reguły — dostęp próbny.
* **Reguła podsieci do sieci**: Ta reguła — dostęp próbny jest domyślną regułę, która została aktywowana i zmodyfikowane, aby każdy serwer w podsieci zaplecza, aby nawiązać połączenie z dowolnego serwera w podsieci frontonu. Ta reguła jest ruch wewnętrzny wszystkich, więc SNAT nie można ustawić metodę połączenia.
  
    ![Reguły sieci wirtualnej wewnątrz zapory][16]
  
    **Uwaga**: Dwukierunkowa zaznacz pole wyboru nie jest zaznaczone (ani nie jest zaewidencjonowany większość reguł), jest to istotne dla tej reguły, w tym, że to sprawia, że to reguły "w jedną stronę", połączenie może zostać zainicjowane z podsieci zaplecza sieć frontonu, ale nie odwrotnie. Ta zasada, pole wyboru zostało zaznaczone, czy włączyć ruch dwukierunkowy, który z naszych diagram logiczny nie jest wymagana.
* **Odmów wszystkie reguły ruchu**: Powinna zawsze być końcowa reguła (w kategoriach priority), a związku z tym jeśli ruch przepływa nie pasuje do żadnej z powyższych zasad, które zostaną usunięte przez tę regułę. Jest to domyślna reguła i zazwyczaj aktywowana, nie są ogólnie potrzebne modyfikacje. 
  
    ![Zapora regułę Odmów][17]

> [!IMPORTANT]
> Po utworzeniu wszystkich powyższych reguł jest ważne zapoznać się z priorytetem każdej reguły, aby upewnić się, ruch będzie dozwolony lub zabroniony zgodnie z potrzebami. W tym przykładzie reguły są w kolejności, w której powinny one zostać wyświetlone w siatce Main reguł w kliencie zarządzania Barracuda przekazywania.
> 
> 

## <a name="rule-activation"></a>Reguła aktywacji
Za pomocą modyfikacji ze specyfikacją diagram logiki zestaw reguł zestaw reguł musi być przekazane do zapory i następnie aktywowany.

![Aktywacja reguły zapory][18]

W prawym górnym rogu klienta zarządzania są klastra przycisków. Kliknij przycisk "Wyślij zmiany", aby wysyłać modyfikacji reguły zapory, a następnie kliknij przycisk "Aktywuj".

Za pomocą aktywacji zestawu reguł zapory na przykład kompilacji środowiska zostało ukończone.

## <a name="traffic-scenarios"></a>Scenariusze ruchu
> [!IMPORTANT]
> Należy pamiętać, że jest kluczowym wnioskiem **wszystkich** ruchu pojawią się przez zaporę. Więc do pulpitu zdalnego z serwerem IIS01, mimo że jest w pierwszej usłudze końcowy w chmurze i w podsieci frontonu dostępu do tego serwera firma Microsoft będzie konieczne protokołu RDP do zapory na porcie 8014, a następnie pozwól zaporę, tak aby kierować żądania protokołu RDP wewnętrznie do IIS01 Por protokołu RDP t. Przycisk "Połącz" w witrynie Azure portal nie będą działać, ponieważ nie ma bezpośredniego RDP ścieżki do IIS01 (o ile zobaczyć portalu). Oznacza to, że wszystkie połączenia z Internetem będzie usługa zabezpieczeń i Port, np. secscv001.cloudapp.net:xxxx.
> 
> 

W tych scenariuszach następujące reguły zapory powinny być stosowane:

1. Zarządzanie zaporą
2. RDP to IIS01
3. RDP to DNS01
4. Połączenie przez RDP AppVM01
5. Połączenie przez RDP AppVM02
6. Ruchem aplikacji sieci Web
7. Ruch aplikacji AppVM01
8. Ruch wychodzący do Internetu
9. Frontonu DNS01
10. Ruch wewnątrz podsieci (zaplecza do tylko fronton)
11. Odmów wszystkim

Zestaw reguł zapory rzeczywiste najprawdopodobniej będą mieć wiele zasad oprócz wspomnianych, reguł zapory, na danej będą mieć również liczb inny priorytet niż wymienione w tym miejscu. Tej liście, a skojarzone z nimi numery jest zapewnienie zgodności między tylko te reguły jedenaście i względny priorytet wśród nich. Innymi słowy; na zaporze rzeczywiste "RDP do IIS01" może być numer reguły 5, ale tak długo, jak jest poniżej reguły "Zarządzanie zaporą" i powyżej reguły "RDP DNS01" będzie wyrównanie zamiarem tej listy. Lista będzie również pomóc w poniższe scenariusze, dzięki czemu zwięzłości; np. "Reguła Zapory 9 (DNS)". Celu skrócenia programu, cztery reguły protokołu RDP będzie można zbiorczo skrót, "reguły protokołu RDP" podczas scenariusza ruch nie jest powiązana protokołu RDP.

Pamiętaj, że sieciowe grupy zabezpieczeń są w miejscu dla ruch przychodzący z Internetu na z podsieciami frontonu i wewnętrznej bazy danych.

#### <a name="allowed-internet-to-web-server"></a>(Dozwolone) Internet do serwera sieci Web
1. Stronę internetową użytkownika żądania HTTP z SecSvc001.CloudApp.Net (usługa chmury połączonego z Internetem)
2. Chmury usługi przekazuje ruch przez otwarty punkt końcowy na porcie 80 do interfejsu zapory 10.0.0.4:80
3. Brak sieciowej grupy zabezpieczeń przypisane do podsieci zabezpieczeń, dlatego reguły sieciowej grupy zabezpieczeń systemu zezwalają na ruch do zapory
4. Ruch osiąga wewnętrzny adres IP zapory (10.0.1.4)
5. Zapora rozpoczyna przetwarzanie reguł:
   1. 1 regułę Zapory (PD Mgmt) nie zastosować, przenieść następną regułę
   2. Reguły Zapory 2 do 5 (protokół RDP reguły) nie zastosować, przenieść następną regułę
   3. Reguła Zapory 6 (aplikacji: Stosowanie aplikacji sieci Web), ruch jest dozwolony, zapory NAT jego 10.0.1.4 (IIS01)
6. Podsieć frontonu rozpoczyna przetwarzanie reguł dla ruchu przychodzącego:
   1. 1 regułę sieciowej grupy zabezpieczeń (Internet bloku) nie ma zastosowania (ten ruch był translatora adresów Sieciowych będzie przez zaporę, dlatego adres źródłowy jest teraz zapory, która znajduje się w podsieci zabezpieczeń i widoczne dla podsieci Frontend sieciowa grupa zabezpieczeń to "local" ruchu i w związku z tym jest dozwolona), przenieść następną regułę
   2. Domyślne reguły sieciowej grupy zabezpieczeń zezwolić na ruch podsieci do sieci, ruch jest dozwolony, Zatrzymaj przetwarzanie reguł sieciowej grupy zabezpieczeń
7. IIS01 nasłuchuje ruchu w sieci web, odbiera żądania i rozpoczyna przetwarzanie żądania
8. Próby IIS01 inicjuje sesję FTP, aby AppVM01 podsieci wewnętrznej bazy danych
9. Trasa zdefiniowana przez użytkownika trasy podsieci frontonu sprawia, że Zapora następny przeskok
10. Nie reguł ruchu wychodzącego w podsieci Frontend jest dozwolony ruch
11. Zapora rozpoczyna przetwarzanie reguł:
    1. 1 regułę Zapory (PD Mgmt) nie zastosować, przenieść następną regułę
    2. Reguły Zapory 2 do 5 (protokół RDP reguły) nie zastosować, przenieść następną regułę
    3. Reguła Zapory 6 (aplikacji: Sieć Web) nie zastosować, przenieść następną regułę
    4. Reguła Zapory 7 (aplikacji: Zastosuj wewnętrznej bazy danych), ruch jest dozwolony, zapora przekazuje ruch do 10.0.2.5 (AppVM01)
12. Podsieci wewnętrznej bazy danych rozpoczyna się przetwarzanie reguł dla ruchu przychodzącego:
    1. 1 regułę sieciowej grupy zabezpieczeń (Internet bloku) nie zastosować, przenieść następną regułę
    2. Domyślne reguły sieciowej grupy zabezpieczeń zezwolić na ruch podsieci do sieci, ruch jest dozwolony, Zatrzymaj przetwarzanie reguł sieciowej grupy zabezpieczeń
13. AppVM01 odbiera żądanie i inicjuje sesję, a odpowiada
14. Trasa zdefiniowana przez użytkownika trasy podsieci zaplecza sprawia, że Zapora następny przeskok
15. Ponieważ brak wychodzących reguł sieciowej grupy zabezpieczeń w podsieci wewnętrznej bazy danych, odpowiedź jest dozwolone
16. Ponieważ to zwraca ruchu na ustanowienie sesji zapora przekazuje odpowiedź z powrotem do serwera sieci web (IIS01)
17. Podsieć frontonu rozpoczyna przetwarzanie reguł dla ruchu przychodzącego:
    1. 1 regułę sieciowej grupy zabezpieczeń (Internet bloku) nie zastosować, przenieść następną regułę
    2. Domyślne reguły sieciowej grupy zabezpieczeń zezwolić na ruch podsieci do sieci, ruch jest dozwolony, Zatrzymaj przetwarzanie reguł sieciowej grupy zabezpieczeń
18. Na serwerze usług IIS odbiera odpowiedź, wykonuje transakcję za pomocą AppVM01 i następnie kończy tworzenia odpowiedzi HTTP, odpowiedź HTTP są wysyłane do obiektu żądającego
19. Ponieważ brak wychodzących reguł sieciowej grupy zabezpieczeń w podsieci Frontend jest dozwolony w odpowiedzi
20. Odpowiedź HTTP trafienia zapory, a ponieważ to jest odpowiedź na ustanowienie sesji translatora adresów Sieciowych jest akceptowane przez zaporę
21. Zapora następnie przekierowuje odpowiedź z powrotem do użytkownika internetowego
22. Ponieważ nie wychodzące reguły sieciowej grupy zabezpieczeń lub przeskoków trasy zdefiniowanej przez użytkownika w podsieci frontonu, odpowiedź jest dozwolone i użytkownik Internetu odbiera żądanej strony sieci web.

#### <a name="allowed-internet-rdp-to-backend"></a>(Dozwolone) Internet protokołu RDP do wewnętrznej bazy danych
1. Administrator serwera w Internecie żądań sesji protokołu RDP, aby AppVM01 za pośrednictwem SecSvc001.CloudApp.Net:8025, gdzie 8025 jest numerem portu przypisanych przez użytkownika dla reguły zapory "RDP AppVM01"
2. Usługi w chmurze przekazuje ruch przez otwarty punkt końcowy na porcie 8025 do interfejsu zapory na 10.0.0.4:8025
3. Brak sieciowej grupy zabezpieczeń przypisane do podsieci zabezpieczeń, dlatego reguły sieciowej grupy zabezpieczeń systemu zezwalają na ruch do zapory
4. Zapora rozpoczyna przetwarzanie reguł:
   1. 1 regułę Zapory (PD Mgmt) nie zastosować, przenieść następną regułę
   2. PD reguła 2 (RDP IIS) nie zastosować, przenieść następną regułę
   3. PD zasady 3 (RDP DNS01) nie zastosować, przenieść następną regułę
   4. Zastosuj PD zasady 4 (protokół RDP AppVM01), ruch jest dozwolony, zapory NAT jego 10.0.2.5:3386 (portem RDP na AppVM01)
5. Podsieci wewnętrznej bazy danych rozpoczyna się przetwarzanie reguł dla ruchu przychodzącego:
   1. 1 regułę sieciowej grupy zabezpieczeń (Internet bloku) nie ma zastosowania (ten ruch był translatora adresów Sieciowych będzie przez zaporę, dlatego adres źródłowy jest teraz zapory, która znajduje się w podsieci zabezpieczeń i widoczne dla sieciowa grupa zabezpieczeń podsieci wewnętrznej bazy danych to "local" ruchu i w związku z tym jest dozwolona), przenieść następną regułę
   2. Domyślne reguły sieciowej grupy zabezpieczeń zezwolić na ruch podsieci do sieci, ruch jest dozwolony, Zatrzymaj przetwarzanie reguł sieciowej grupy zabezpieczeń
6. AppVM01 nasłuchuje ruchu protokołu RDP i odpowiada
7. Nie wychodzące reguły sieciowej grupy zabezpieczeń domyślne reguły stosuje się i jest dozwolony ruch powrotny
8. Trasa zdefiniowana przez użytkownika kieruje ruch wychodzący do zapory jako następnego przeskoku
9. Ponieważ to zwraca ruchu na ustanowienie sesji zapora przekazuje odpowiedź z powrotem do użytkownika internetowego
10. Sesję RDP jest włączona.
11. AppVM01 monituje o podanie hasła nazwy użytkownika

#### <a name="allowed-web-server-dns-lookup-on-dns-server"></a>(Dozwolone) Wyszukiwanie DNS serwera sieci Web na serwerze DNS
1. Wymagania serwera IIS01, strumieniowych źródeł danych w sieci Web w sieci Web\.data.gov, ale musi rozpoznać adresu.
2. W konfiguracji sieci VNet list DNS01 (10.0.2.4 w podsieci wewnętrznej bazy danych) jako podstawowy serwer DNS, IIS01 wysyła żądania DNS DNS01
3. Trasa zdefiniowana przez użytkownika kieruje ruch wychodzący do zapory jako następnego przeskoku
4. Brak wychodzących reguł sieciowej grupy zabezpieczeń są powiązane z podsiecią Frontend, ruch jest dozwolony.
5. Zapora rozpoczyna przetwarzanie reguł:
   1. 1 regułę Zapory (PD Mgmt) nie zastosować, przenieść następną regułę
   2. Reguły Zapory 2 do 5 (protokół RDP reguły) nie zastosować, przenieść następną regułę
   3. PD zasad 6 i 7 (reguły aplikacji) nie zastosować, przenieść następną regułę
   4. 8 reguły Zapory (Internet w celu) nie zastosować, przenieść następną regułę
   5. Zastosuj 9 reguły Zapory (DNS, Domain Name System), ruch jest dozwolony, zapora przekazuje ruch do 10.0.2.4 (DNS01)
6. Podsieci wewnętrznej bazy danych rozpoczyna się przetwarzanie reguł dla ruchu przychodzącego:
   1. 1 regułę sieciowej grupy zabezpieczeń (Internet bloku) nie zastosować, przenieść następną regułę
   2. Domyślne reguły sieciowej grupy zabezpieczeń zezwolić na ruch podsieci do sieci, ruch jest dozwolony, Zatrzymaj przetwarzanie reguł sieciowej grupy zabezpieczeń
7. Serwer DNS odbiera żądanie
8. Serwer DNS nie ma adresu pamięci podręcznej i prosi główny serwer DNS w Internecie
9. Trasa zdefiniowana przez użytkownika kieruje ruch wychodzący do zapory jako następnego przeskoku
10. Nie wychodzące reguły sieciowej grupy zabezpieczeń w podsieci wewnętrznej bazy danych, ruch jest dozwolony.
11. Zapora rozpoczyna przetwarzanie reguł:
    1. 1 regułę Zapory (PD Mgmt) nie zastosować, przenieść następną regułę
    2. Reguły Zapory 2 do 5 (protokół RDP reguły) nie zastosować, przenieść następną regułę
    3. PD zasad 6 i 7 (reguły aplikacji) nie zastosować, przenieść następną regułę
    4. Zastosuj 8 reguły Zapory (do Internetu), ruch jest dozwolony, sesja jest SNAT się do głównego serwera DNS w Internecie
12. Odpowiedzi serwera DNS w sieci Internet, ponieważ w tej sesji zostało zainicjowane przez zaporę, odpowiedź jest akceptowany przez zaporę
13. Jak jest to ustanowienie sesji, zapora przekazuje odpowiedź do serwera inicjujący DNS01
14. Podsieci wewnętrznej bazy danych rozpoczyna się przetwarzanie reguł dla ruchu przychodzącego:
    1. 1 regułę sieciowej grupy zabezpieczeń (Internet bloku) nie zastosować, przenieść następną regułę
    2. Domyślne reguły sieciowej grupy zabezpieczeń zezwolić na ruch podsieci do sieci, ruch jest dozwolony, Zatrzymaj przetwarzanie reguł sieciowej grupy zabezpieczeń
15. Serwer DNS odbiera będzie buforować odpowiedź i następnie odpowie na żądanie początkowy powrót do IIS01
16. Trasa zdefiniowana przez użytkownika trasy podsieci zaplecza sprawia, że Zapora następny przeskok
17. Brak wychodzących reguł sieciowej grupy zabezpieczeń istnieje w podsieci wewnętrznej bazy danych, ruch jest dozwolony.
18. Jest to ustanowienie sesji w zaporze, odpowiedź jest przesyłany dalej przez zaporę do serwera usług IIS
19. Podsieć frontonu rozpoczyna przetwarzanie reguł dla ruchu przychodzącego:
    1. Istnieje nie reguły sieciowej grupy zabezpieczeń, która ma zastosowanie do ruchu przychodzącego ruchu sieciowego z podsieci zaplecza do podsieci frontonu, aby Brak sieciowej grupy zabezpieczeń reguły, zastosuj
    2. Reguła systemowa domyślnej zezwalającej na ruch pomiędzy podsieciami będzie zezwalającej na ten ruch, więc ruch jest dozwolony
20. IIS01 odbiera odpowiedź od DNS01

#### <a name="allowed-backend-server-to-frontend-server"></a>(Dozwolone) Serwer wewnętrznej bazy danych, serwer frontonu
1. Administrator zalogowany do AppVM02 za pośrednictwem protokołu RDP zażąda pliku bezpośrednio z serwera IIS01 za pomocą Eksploratora plików systemu windows
2. Trasa zdefiniowana przez użytkownika trasy podsieci zaplecza sprawia, że Zapora następny przeskok
3. Ponieważ brak wychodzących reguł sieciowej grupy zabezpieczeń w podsieci wewnętrznej bazy danych, odpowiedź jest dozwolone
4. Zapora rozpoczyna przetwarzanie reguł:
   1. 1 regułę Zapory (PD Mgmt) nie zastosować, przenieść następną regułę
   2. Reguły Zapory 2 do 5 (protokół RDP reguły) nie zastosować, przenieść następną regułę
   3. PD zasad 6 i 7 (reguły aplikacji) nie zastosować, przenieść następną regułę
   4. 8 reguły Zapory (Internet w celu) nie zastosować, przenieść następną regułę
   5. 9 reguły Zapory (DNS, Domain Name System) nie zastosować, przenieść następną regułę
   6. Zastosuj 10 reguły Zapory (Intra-podsieć), ruch jest dozwolony, zapora przekazuje ruch do 10.0.1.4 (IIS01)
5. Podsieć frontonu rozpoczyna przetwarzanie reguł dla ruchu przychodzącego:
   1. 1 regułę sieciowej grupy zabezpieczeń (Internet bloku) nie zastosować, przenieść następną regułę
   2. Domyślne reguły sieciowej grupy zabezpieczeń zezwolić na ruch podsieci do sieci, ruch jest dozwolony, Zatrzymaj przetwarzanie reguł sieciowej grupy zabezpieczeń
6. Zakładając, że odpowiednie uwierzytelnianie i autoryzacja, IIS01 akceptuje żądania i odpowiedzi
7. Trasa zdefiniowana przez użytkownika trasy podsieci frontonu sprawia, że Zapora następny przeskok
8. Ponieważ brak wychodzących reguł sieciowej grupy zabezpieczeń w podsieci Frontend jest dozwolony w odpowiedzi
9. Jak jest to istniejącej sesji w obrębie zapory ta odpowiedź jest dozwolone, i zapory, zwraca odpowiedź do AppVM02
10. Podsieci wewnętrznej bazy danych rozpoczyna się przetwarzanie reguł dla ruchu przychodzącego:
    1. 1 regułę sieciowej grupy zabezpieczeń (Internet bloku) nie zastosować, przenieść następną regułę
    2. Domyślne reguły sieciowej grupy zabezpieczeń zezwolić na ruch podsieci do sieci, ruch jest dozwolony, Zatrzymaj przetwarzanie reguł sieciowej grupy zabezpieczeń
11. AppVM02 odbiera odpowiedź

#### <a name="denied-internet-direct-to-web-server"></a>(Odrzucony) Internet bezpośrednio do serwera sieci Web
1. Internet użytkownik próbuje uzyskać dostęp serwera sieci web IIS01, za pośrednictwem usługi FrontEnd001.CloudApp.Net
2. Punkty końcowe nie są otwarte dla ruchu HTTP, to nie przejdzie przez usługę w chmurze, a nie dociera do serwera
3. W przypadku punktów końcowych, które były otwarte niektóre przyczyny, ten ruch mogłyby spowodować zablokowanie (bloku Internet) sieciowej grupy zabezpieczeń w podsieci frontonu
4. Na koniec trasy zdefiniowanej przez użytkownika trasy podsieci frontonu prześle wszelki ruch wychodzący z IIS01 do zapory w następnym skoku, a zapora będzie to zobaczyć jako asymetrycznego ruchu i upuść wychodzące odpowiedzi Thus są co najmniej trzy warstwy niezależnych obrony między internet i IIS01 za pośrednictwem jego uniemożliwia nieautoryzowany niewłaściwego dostępu usługi w chmurze.

#### <a name="denied-internet-to-backend-server"></a>(Odrzucony) Internet do serwera wewnętrznej bazy danych
1. Internet użytkownik próbuje uzyskać dostęp pliku na AppVM01 za pośrednictwem usługi BackEnd001.CloudApp.Net
2. Punkty końcowe nie są otwarte dla udziału plików, to nie przejdzie z usługą w chmurze, a nie dociera do serwera
3. W przypadku punktów końcowych, które były otwarte niektóre przyczyny, ten ruch mogłyby spowodować zablokowanie sieciowej grupy zabezpieczeń (Block Internet)
4. Na koniec trasy UDR prześle wszelki ruch wychodzący z AppVM01 do zapory w następnym skoku, a zapora będzie to zobaczyć jako asymetrycznego ruchu i upuść wychodzące odpowiedzi, dlatego są co najmniej trzy warstwy niezależnych obrony między Internetem a AppVM01 za pośrednictwem jego uniemożliwia nieautoryzowany niewłaściwego dostępu usługi w chmurze.

#### <a name="denied-frontend-server-to-backend-server"></a>(Odrzucony) Serwer frontonu do serwera wewnętrznej bazy danych
1. Przyjęto założenie, IIS01 zostało naruszone i działa złośliwego kodu próby przeskanowania serwerów podsieci wewnętrznej bazy danych.
2. Trasa zdefiniowana przez użytkownika trasy podsieci frontonu będzie Wyślij cały ruch wychodzący z IIS01 do zapory w następnym skoku. To nie jest coś, co może być zmienione przez maszynę Wirtualną ze złamanymi zabezpieczeniami.
3. Zapora może przetwarzać ruchu sieciowego, jeśli żądanie zostało AppVM01 lub serwera DNS dla wyszukiwania DNS, które potencjalnie być dozwolony ruch przez zaporę (z powodu PD zasady 7 i 9). Wszystkie pozostałe rodzaje ruchu zostałby zablokowany przez 11 reguły Zapory (Odmów wszystkiego).
4. Jeśli zaawansowanego wykrywania zagrożeń został włączony na zaporze (nieuwzględnione w tym dokumencie, można znaleźć w dokumentacji dostawcy dla urządzenia sieciowego określonych zaawansowane możliwości zagrożeń), nawet ruch, który może być blokowany przez reguły przekazywania podstawowych omówione w tym dokumencie można zapobiec, jeśli ruch zawarte podpisów znanych lub wzorców, które Flaga regułę zaawansowanych zagrożeń.

#### <a name="denied-internet-dns-lookup-on-dns-server"></a>(Odrzucony) Wyszukiwanie DNS w sieci Internet, na serwerze DNS
1. Internet użytkownik podejmuje próbę wyszukiwania wewnętrznego rekord DNS na DNS01 za pośrednictwem usługi BackEnd001.CloudApp.Net 
2. Punkty końcowe nie są otwarte dla ruch DNS, to nie przejdzie przez usługę w chmurze, a nie dociera do serwera
3. W przypadku punktów końcowych, które były otwarte niektóre przyczyny, ten ruch mogłyby spowodować zablokowanie reguły sieciowej grupy zabezpieczeń (Block Internet), podsieci frontonu
4. Na koniec trasy zdefiniowanej przez użytkownika trasy podsieci wewnętrznej bazy danych będzie wysyłać wszelki ruch wychodzący z DNS01 do zapory w następnym skoku i Zapora będzie to zobaczyć jako asymetrycznego ruchu i upuść wychodzące odpowiedzi Thus są co najmniej trzy warstwy niezależnych obrony między Internet i DNS01 za pośrednictwem jego uniemożliwia nieautoryzowany niewłaściwego dostępu usługi w chmurze.

#### <a name="denied-internet-to-sql-access-through-firewall"></a>(Odrzucony) Internet w celu SQL dostęp przez zaporę
1. Internet użytkownik żąda danych serwera SQL z SecSvc001.CloudApp.Net (usługa chmury połączonego z Internetem)
2. Punkty końcowe nie są otwarte dla programu SQL, to nie przejdzie z usługą w chmurze i w takich sytuacjach przydałaby dotrzeć do zapory
3. W przypadku punktów końcowych SQL były otwarte niektóre przyczyny, zapora będzie rozpocząć przetwarzanie reguł:
   1. 1 regułę Zapory (PD Mgmt) nie zastosować, przenieść następną regułę
   2. Reguły Zapory 2 do 5 (protokół RDP reguły) nie zastosować, przenieść następną regułę
   3. PD zasady 6 i 7 (reguł aplikacji) nie zastosować, przenieść następną regułę
   4. 8 reguły Zapory (Internet w celu) nie zastosować, przenieść następną regułę
   5. 9 reguły Zapory (DNS, Domain Name System) nie zastosować, przenieść następną regułę
   6. 10 reguły Zapory (Intra-podsieci) nie zastosować, przenieść następną regułę
   7. Zastosuj zasady 11 Pd (Odmów wszystkiego), ruch jest przetwarzanie reguł blokowania, Zatrzymaj

## <a name="references"></a>Dokumentacja
### <a name="main-script-and-network-config"></a>Skrypt głównego i konfiguracji sieci
Pełny skrypt należy zapisać w pliku skryptu programu PowerShell. W pliku o nazwie "NetworkConf2.xml", można zapisać konfiguracji sieci.
Zmodyfikuj zmienne zdefiniowane przez użytkownika, zgodnie z potrzebami. Uruchom skrypt, a następnie postępuj zgodnie z powyższych instrukcji konfiguracji reguły zapory.

#### <a name="full-script"></a>Pełny skrypt
Ten skrypt będzie na podstawie zmiennych zdefiniowanych przez użytkownika:

1. Łączenie się z subskrypcją platformy Azure
2. Tworzenie nowego konta magazynu
3. Utwórz nową sieć wirtualną oraz trzy podsieci, zgodnie z definicją w pliku konfiguracji sieci
4. Tworzenie pięć maszyn wirtualnych; Zapora 1 i 4 systemu windows server maszyn wirtualnych
5. Skonfiguruj trasy zdefiniowanej przez użytkownika w tym:
   1. Utworzenie dwóch nowych tabel tras
   2. Dodawanie tras do tabel
   3. Powiąż tabel do odpowiednich podsieci
6. Włączanie przekazywania adresów IP na urządzeniu WUS
7. Konfigurowanie sieciowej grupy zabezpieczeń w tym:
   1. Tworzenie sieciowej grupy zabezpieczeń
   2. Dodawanie reguły
   3. Powiązanie odpowiednie podsieci sieciową grupę zabezpieczeń

Ten skrypt programu PowerShell można uruchamiać lokalnie na połączone z Internetem, komputera lub serwera.

> [!IMPORTANT]
> Po uruchomieniu tego skryptu można ostrzeżenia lub inne komunikaty informacyjne, które punktów obecności w programie PowerShell. Tylko komunikaty o błędach w kolorze czerwonym są zaniepokoić.
> 
> 

```powershell
    <# 
     .SYNOPSIS
      Example of DMZ and User Defined Routing in an isolated network (Azure only, no hybrid connections)

     .DESCRIPTION
      This script will build out a sample DMZ setup containing:
       - A default storage account for VM disks
       - Three new cloud services
       - Three Subnets (SecNet, FrontEnd, and BackEnd subnets)
       - A Network Virtual Appliance (NVA), in this case a Barracuda NextGen Firewall
       - One server on the FrontEnd Subnet
       - Three Servers on the BackEnd Subnet
       - IP Forwarding from the FireWall out to the internet
       - User Defined Routing FrontEnd and BackEnd Subnets to the NVA

      Before running script, ensure the network configuration file is created in
      the directory referenced by $NetworkConfigFile variable (or update the
      variable to reflect the path and file name of the config file being used).

     .Notes
      Everyone's security requirements are different and can be addressed in a myriad of ways.
      Please be sure that any sensitive data or applications are behind the appropriate
      layer(s) of protection. This script serves as an example of some of the techniques
      that can be used, but should not be used for all scenarios. You are responsible to
      assess your security needs and the appropriate protections needed, and then effectively
      implement those protections.

      Security Service (SecNet subnet 10.0.0.0/24)
       myFirewall - 10.0.0.4

      FrontEnd Service (FrontEnd subnet 10.0.1.0/24)
       IIS01      - 10.0.1.4

      BackEnd Service (BackEnd subnet 10.0.2.0/24)
       DNS01      - 10.0.2.4
       AppVM01    - 10.0.2.5
       AppVM02    - 10.0.2.6

    #>

    # Fixed Variables
        $LocalAdminPwd = Read-Host -Prompt "Enter Local Admin Password to be used for all VMs"
        $VMName = @()
        $ServiceName = @()
        $VMFamily = @()
        $img = @()
        $size = @()
        $SubnetName = @()
        $VMIP = @()

    # User Defined Global Variables
      # These should be changes to reflect your subscription and services
      # Invalid options will fail in the validation section

      # Subscription Access Details
        $subID = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"

      # VM Account, Location, and Storage Details
        $LocalAdmin = "theAdmin"
        $DeploymentLocation = "Central US"
        $StorageAccountName = "vmstore02"

      # Service Details
        $SecureService = "SecSvc001"
        $FrontEndService = "FrontEnd001"
        $BackEndService = "BackEnd001"

      # Network Details
        $VNetName = "CorpNetwork"
        $VNetPrefix = "10.0.0.0/16"
        $SecNet = "SecNet"
        $FESubnet = "FrontEnd"
        $FEPrefix = "10.0.1.0/24"
        $BESubnet = "BackEnd"
        $BEPrefix = "10.0.2.0/24"
        $NetworkConfigFile = "C:\Scripts\NetworkConf3.xml"

      # VM Base Disk Image Details
        $SrvImg = Get-AzureVMImage | Where {$_.ImageFamily -match 'Windows Server 2012 R2 Datacenter'} | sort PublishedDate -Descending | Select ImageName -First 1 | ForEach {$_.ImageName}
        $FWImg = Get-AzureVMImage | Where {$_.ImageFamily -match 'Barracuda NextGen Firewall'} | sort PublishedDate -Descending | Select ImageName -First 1 | ForEach {$_.ImageName}

      # UDR Details
        $FERouteTableName = "FrontEndSubnetRouteTable"
        $BERouteTableName = "BackEndSubnetRouteTable"

      # NSG Details
        $NSGName = "MyVNetSG"

    # User Defined VM Specific Config
        # Note: To ensure UDR and IP forwarding is setup
        # properly this script requires VM 0 be the NVA.

        # VM 0 - The Network Virtual Appliance (NVA)
          $VMName += "myFirewall"
          $ServiceName += $SecureService
          $VMFamily += "Firewall"
          $img += $FWImg
          $size += "Small"
          $SubnetName += $SecNet
          $VMIP += "10.0.0.4"

        # VM 1 - The Web Server
          $VMName += "IIS01"
          $ServiceName += $FrontEndService
          $VMFamily += "Windows"
          $img += $SrvImg
          $size += "Standard_D3"
          $SubnetName += $FESubnet
          $VMIP += "10.0.1.4"

        # VM 2 - The First Application Server
          $VMName += "AppVM01"
          $ServiceName += $BackEndService
          $VMFamily += "Windows"
          $img += $SrvImg
          $size += "Standard_D3"
          $SubnetName += $BESubnet
          $VMIP += "10.0.2.5"

        # VM 3 - The Second Application Server
          $VMName += "AppVM02"
          $ServiceName += $BackEndService
          $VMFamily += "Windows"
          $img += $SrvImg
          $size += "Standard_D3"
          $SubnetName += $BESubnet
          $VMIP += "10.0.2.6"

        # VM 4 - The DNS Server
          $VMName += "DNS01"
          $ServiceName += $BackEndService
          $VMFamily += "Windows"
          $img += $SrvImg
          $size += "Standard_D3"
          $SubnetName += $BESubnet
          $VMIP += "10.0.2.4"

    # ----------------------------- #
    # No User Defined Variables or   #
    # Configuration past this point #
    # ----------------------------- #

      # Get your Azure accounts
        Add-AzureAccount
        Set-AzureSubscription –SubscriptionId $subID -ErrorAction Stop
        Select-AzureSubscription -SubscriptionId $subID -Current -ErrorAction Stop

      # Create Storage Account
        If (Test-AzureName -Storage -Name $StorageAccountName) { 
            Write-Host "Fatal Error: This storage account name is already in use, please pick a different name." -ForegroundColor Red
            Return}
        Else {Write-Host "Creating Storage Account" -ForegroundColor Cyan 
              New-AzureStorageAccount -Location $DeploymentLocation -StorageAccountName $StorageAccountName}

      # Update Subscription Pointer to New Storage Account
        Write-Host "Updating Subscription Pointer to New Storage Account" -ForegroundColor Cyan 
        Set-AzureSubscription –SubscriptionId $subID -CurrentStorageAccountName $StorageAccountName -ErrorAction Stop

    # Validation
    $FatalError = $false

    If (-Not (Get-AzureLocation | Where {$_.DisplayName -eq $DeploymentLocation})) {
         Write-Host "This Azure Location was not found or available for use" -ForegroundColor Yellow
         $FatalError = $true}

    If (Test-AzureName -Service -Name $SecureService) { 
        Write-Host "The SecureService service name is already in use, please pick a different service name." -ForegroundColor Yellow
        $FatalError = $true}
    Else { Write-Host "The FrontEndService service name is valid for use." -ForegroundColor Green}

    If (Test-AzureName -Service -Name $FrontEndService) { 
        Write-Host "The FrontEndService service name is already in use, please pick a different service name." -ForegroundColor Yellow
        $FatalError = $true}
    Else { Write-Host "The FrontEndService service name is valid for use" -ForegroundColor Green}

    If (Test-AzureName -Service -Name $BackEndService) { 
        Write-Host "The BackEndService service name is already in use, please pick a different service name." -ForegroundColor Yellow
        $FatalError = $true}
    Else { Write-Host "The BackEndService service name is valid for use." -ForegroundColor Green}

    If (-Not (Test-Path $NetworkConfigFile)) { 
        Write-Host 'The network config file was not found, please update the $NetworkConfigFile variable to point to the network config xml file.' -ForegroundColor Yellow
        $FatalError = $true}
    Else { Write-Host "The network config file was found" -ForegroundColor Green
            If (-Not (Select-String -Pattern $DeploymentLocation -Path $NetworkConfigFile)) {
                Write-Host 'The deployment location was not found in the network config file, please check the network config file to ensure the $DeploymentLocation variable is correct and the network config file matches.' -ForegroundColor Yellow
                $FatalError = $true}
            Else { Write-Host "The deployment location was found in the network config file." -ForegroundColor Green}}

    If ($FatalError) {
        Write-Host "A fatal error has occurred, please see the above messages for more information." -ForegroundColor Red
        Return}
    Else { Write-Host "Validation passed, now building the environment." -ForegroundColor Green}

    # Create VNET
        Write-Host "Creating VNET" -ForegroundColor Cyan 
        Set-AzureVNetConfig -ConfigurationPath $NetworkConfigFile -ErrorAction Stop

    # Create Services
        Write-Host "Creating Services" -ForegroundColor Cyan
        New-AzureService -Location $DeploymentLocation -ServiceName $SecureService -ErrorAction Stop
        New-AzureService -Location $DeploymentLocation -ServiceName $FrontEndService -ErrorAction Stop
        New-AzureService -Location $DeploymentLocation -ServiceName $BackEndService -ErrorAction Stop

    # Build VMs
        $i=0
        $VMName | Foreach {
            Write-Host "Building $($VMName[$i])" -ForegroundColor Cyan
            If ($VMFamily[$i] -eq "Firewall") 
                { 
                New-AzureVMConfig -Name $VMName[$i] -ImageName $img[$i] –InstanceSize $size[$i] | `
                    Add-AzureProvisioningConfig -Linux -LinuxUser $LocalAdmin -Password $LocalAdminPwd  | `
                    Set-AzureSubnet  –SubnetNames $SubnetName[$i] | `
                    Set-AzureStaticVNetIP -IPAddress $VMIP[$i] | `
                    New-AzureVM –ServiceName $ServiceName[$i] -VNetName $VNetName -Location $DeploymentLocation
                # Set up all the EndPoints we'll need once we're up and running
                # Note: All traffic goes through the firewall, so we'll need to set up all ports here.
                #       Also, the firewall will be redirecting traffic to a new IP and Port in a forwarding
                #       rule, so all of these endpoint have the same public and local port and the firewall
                #       will do the mapping, NATing, and/or redirection as declared in the firewall rules.
                Add-AzureEndpoint -Name "MgmtPort1" -Protocol tcp -PublicPort 801  -LocalPort 801  -VM (Get-AzureVM -ServiceName $ServiceName[$i] -Name $VMName[$i]) | Update-AzureVM
                Add-AzureEndpoint -Name "MgmtPort2" -Protocol tcp -PublicPort 807  -LocalPort 807  -VM (Get-AzureVM -ServiceName $ServiceName[$i] -Name $VMName[$i]) | Update-AzureVM
                Add-AzureEndpoint -Name "HTTP"      -Protocol tcp -PublicPort 80   -LocalPort 80   -VM (Get-AzureVM -ServiceName $ServiceName[$i] -Name $VMName[$i]) | Update-AzureVM
                Add-AzureEndpoint -Name "RDPWeb"    -Protocol tcp -PublicPort 8014 -LocalPort 8014 -VM (Get-AzureVM -ServiceName $ServiceName[$i] -Name $VMName[$i]) | Update-AzureVM
                Add-AzureEndpoint -Name "RDPApp1"   -Protocol tcp -PublicPort 8025 -LocalPort 8025 -VM (Get-AzureVM -ServiceName $ServiceName[$i] -Name $VMName[$i]) | Update-AzureVM
                Add-AzureEndpoint -Name "RDPApp2"   -Protocol tcp -PublicPort 8026 -LocalPort 8026 -VM (Get-AzureVM -ServiceName $ServiceName[$i] -Name $VMName[$i]) | Update-AzureVM
                Add-AzureEndpoint -Name "RDPDNS01"  -Protocol tcp -PublicPort 8024 -LocalPort 8024 -VM (Get-AzureVM -ServiceName $ServiceName[$i] -Name $VMName[$i]) | Update-AzureVM
                # Note: A SSH endpoint is automatically created on port 22 when the appliance is created.
                }
            Else
                {
                New-AzureVMConfig -Name $VMName[$i] -ImageName $img[$i] –InstanceSize $size[$i] | `
                    Add-AzureProvisioningConfig -Windows -AdminUsername $LocalAdmin -Password $LocalAdminPwd  | `
                    Set-AzureSubnet  –SubnetNames $SubnetName[$i] | `
                    Set-AzureStaticVNetIP -IPAddress $VMIP[$i] | `
                    Set-AzureVMMicrosoftAntimalwareExtension -AntimalwareConfiguration '{"AntimalwareEnabled" : true}' | `
                    Remove-AzureEndpoint -Name "RemoteDesktop" | `
                    Remove-AzureEndpoint -Name "PowerShell" | `
                    New-AzureVM –ServiceName $ServiceName[$i] -VNetName $VNetName -Location $DeploymentLocation
                }
            $i++
        }

    # Configure UDR and IP Forwarding
        Write-Host "Configuring UDR" -ForegroundColor Cyan

      # Create the Route Tables
        Write-Host "Creating the Route Tables" -ForegroundColor Cyan 
        New-AzureRouteTable -Name $BERouteTableName `
            -Location $DeploymentLocation `
            -Label "Route table for $BESubnet subnet"
        New-AzureRouteTable -Name $FERouteTableName `
            -Location $DeploymentLocation `
            -Label "Route table for $FESubnet subnet"

      # Add Routes to Route Tables
        Write-Host "Adding Routes to the Route Tables" -ForegroundColor Cyan 
        Get-AzureRouteTable $BERouteTableName `
            |Set-AzureRoute -RouteName "All traffic to FW" -AddressPrefix 0.0.0.0/0 `
            -NextHopType VirtualAppliance `
            -NextHopIpAddress $VMIP[0]
        Get-AzureRouteTable $BERouteTableName `
            |Set-AzureRoute -RouteName "Internal traffic to FW" -AddressPrefix $VNetPrefix `
            -NextHopType VirtualAppliance `
            -NextHopIpAddress $VMIP[0]
        Get-AzureRouteTable $BERouteTableName `
            |Set-AzureRoute -RouteName "Allow Intra-Subnet Traffic" -AddressPrefix $BEPrefix `
            -NextHopType VNETLocal
        Get-AzureRouteTable $FERouteTableName `
            |Set-AzureRoute -RouteName "All traffic to FW" -AddressPrefix 0.0.0.0/0 `
            -NextHopType VirtualAppliance `
            -NextHopIpAddress $VMIP[0]
        Get-AzureRouteTable $FERouteTableName `
            |Set-AzureRoute -RouteName "Internal traffic to FW" -AddressPrefix $VNetPrefix `
            -NextHopType VirtualAppliance `
            -NextHopIpAddress $VMIP[0]
        Get-AzureRouteTable $FERouteTableName `
            |Set-AzureRoute -RouteName "Allow Intra-Subnet Traffic" -AddressPrefix $FEPrefix `
            -NextHopType VNETLocal

      # Associate the Route Tables with the Subnets
        Write-Host "Binding Route Tables to the Subnets" -ForegroundColor Cyan 
        Set-AzureSubnetRouteTable -VirtualNetworkName $VNetName `
            -SubnetName $BESubnet `
            -RouteTableName $BERouteTableName
        Set-AzureSubnetRouteTable -VirtualNetworkName $VNetName `
            -SubnetName $FESubnet `
            -RouteTableName $FERouteTableName

     # Enable IP Forwarding on the Virtual Appliance
        Get-AzureVM -Name $VMName[0] -ServiceName $ServiceName[0] `
            |Set-AzureIPForwarding -Enable

    # Configure NSG
        Write-Host "Configuring the Network Security Group (NSG)" -ForegroundColor Cyan

      # Build the NSG
        Write-Host "Building the NSG" -ForegroundColor Cyan
        New-AzureNetworkSecurityGroup -Name $NSGName -Location $DeploymentLocation -Label "Security group for $VNetName subnets in $DeploymentLocation"

      # Add NSG Rule
        Write-Host "Writing rules into the NSG" -ForegroundColor Cyan
        Get-AzureNetworkSecurityGroup -Name $NSGName | Set-AzureNetworkSecurityRule -Name "Isolate the $VNetName VNet from the Internet" -Type Inbound -Priority 100 -Action Deny `
            -SourceAddressPrefix INTERNET -SourcePortRange '*' `
            -DestinationAddressPrefix VIRTUAL_NETWORK -DestinationPortRange '*' `
            -Protocol *

      # Assign the NSG to two Subnets
        # The NSG is *not* bound to the Security Subnet. The result
        # is that internet traffic flows only to the Security subnet
        # since the NSG bound to the Frontend and Backback subnets
        # will Deny internet traffic to those subnets.
        Write-Host "Binding the NSG to two subnets" -ForegroundColor Cyan
        Set-AzureNetworkSecurityGroupToSubnet -Name $NSGName -SubnetName $FESubnet -VirtualNetworkName $VNetName
        Set-AzureNetworkSecurityGroupToSubnet -Name $NSGName -SubnetName $BESubnet -VirtualNetworkName $VNetName

    # Optional Post-script Manual Configuration
      # Configure Firewall
      # Install Test Web App (Run Post-Build Script on the IIS Server)
      # Install Backend resource (Run Post-Build Script on the AppVM01)
      Write-Host
      Write-Host "Build Complete!" -ForegroundColor Green
      Write-Host
      Write-Host "Optional Post-script Manual Configuration Steps" -ForegroundColor Gray
      Write-Host " - Configure Firewall" -ForegroundColor Gray
      Write-Host " - Install Test Web App (Run Post-Build Script on the IIS Server)" -ForegroundColor Gray
      Write-Host " - Install Backend resource (Run Post-Build Script on the AppVM01)" -ForegroundColor Gray
      Write-Host
```

#### <a name="network-config-file"></a>Plik konfiguracji sieci
Zapisz ten plik xml z lokalizacją zaktualizowane i dodać link do tego pliku do zmiennej $NetworkConfigFile w skrypcie powyżej.

```xml
    <NetworkConfiguration xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/ServiceHosting/2011/07/NetworkConfiguration">
      <VirtualNetworkConfiguration>
        <Dns>
          <DnsServers>
            <DnsServer name="DNS01" IPAddress="10.0.2.4" />
            <DnsServer name="Level3" IPAddress="209.244.0.3" />
          </DnsServers>
        </Dns>
        <VirtualNetworkSites>
          <VirtualNetworkSite name="CorpNetwork" Location="Central US">
            <AddressSpace>
              <AddressPrefix>10.0.0.0/16</AddressPrefix>
            </AddressSpace>
            <Subnets>
              <Subnet name="SecNet">
                <AddressPrefix>10.0.0.0/24</AddressPrefix>
              </Subnet>
              <Subnet name="FrontEnd">
                <AddressPrefix>10.0.1.0/24</AddressPrefix>
              </Subnet>
              <Subnet name="BackEnd">
                <AddressPrefix>10.0.2.0/24</AddressPrefix>
              </Subnet>
            </Subnets>
            <DnsServersRef>
              <DnsServerRef name="DNS01" />
              <DnsServerRef name="Level3" />
            </DnsServersRef>
          </VirtualNetworkSite>
        </VirtualNetworkSites>
      </VirtualNetworkConfiguration>
    </NetworkConfiguration>
```

#### <a name="sample-application-scripts"></a>Przykładowe skrypty aplikacji
Jeśli chcesz zainstalować przykładową aplikację w tym i inne przykłady strefy DMZ, jedno zostało podane z łącza: [Przykładowy skrypt aplikacji][SampleApp]

<!--Image References-->
[1]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/example3design.png "Dwukierunkowe sieci obwodowe NVA, NSG i przez"
[2]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/example3firewalllogical.png "Widok logiczny reguł zapory"
[3]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/createnetworkobjectfrontend.png "Utwórz obiekt sieci frontonu"
[4]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/createnetworkobjectdns.png "Utwórz obiekt serwera DNS"
[5]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/createnetworkobjectrdpa.png "Kopiuj domyślna reguła protokołu RDP"
[6]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/createnetworkobjectrdpb.png "Reguła AppVM01"
[7]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/iconapplicationredirect.png "Ikona przekierowania aplikacji"
[8]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/icondestinationnat.png "Ikona translatora adresów Sieciowych docelowego"
[9]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/iconpass.png "Przekaż ikonę"
[10]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/rulefirewall.png "Reguły zarządzania zapory"
[11]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/rulerdp.png "Reguła protokołu RDP"
[12]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/ruleweb.png "Reguła zapory w sieci Web"
[13]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/ruleappvm01.png "Reguła AppVM01 zapory"
[14]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/ruleoutbound.png "Reguły ruchu wychodzącego zapory"
[15]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/ruledns.png "Reguła DNS"
[16]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/ruleintravnet.png "Reguły sieci wirtualnej wewnątrz zapory"
[17]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/ruledeny.png "Zapora regułę Odmów"
[18]: ./media/virtual-networks-dmz-nsg-fw-udr-asm/firewallruleactivate.png "Aktywacja reguły zapory"

<!--Link References-->
[HOME]: ../best-practices-network-security.md
[SampleApp]: ./virtual-networks-sample-app.md
